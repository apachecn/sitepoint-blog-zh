# 如何用 Ruby 迁移 Oracle 数据

> 原文：<https://www.sitepoint.com/using-oci8-migrate-oracle-data-with-ruby/>

![oracle1](img/358100320bc4c332ac6d84d94ef04e8f.png)

数据迁移是任何 web 开发项目的顶峰。感觉就像爬到山顶只是为了回头往下看然后跳。在一个典型的项目中，您可以根据新的模式测试一切。不顾一切，以每小时 100 英里的速度做出彻底的改变。但是，仍在生产中的遗留数据怎么办？

在本文中，我将深入探讨如何在 Oracle 中成功完成数据迁移。现代 web 开发人员有许多工具可供他们使用。在这里，我将解释为什么在 Ruby 中使用数据迁移脚本是一种乐趣。我将讨论 Oracle，而不是 Rails 中的迁移。

我将在我的例子中使用 OCI8，它是一个用于 Ruby 的 Oracle 适配器。它允许您编写 Ruby 代码来与 Oracle 数据库服务器对话。

首先，数据迁移涉及数据库标准化。这篇[维基百科文章](http://en.wikipedia.org/wiki/Database_normalization)解释道:

> 数据库规范化是组织关系数据库的字段和表以最小化冗余的过程。规范化通常包括将大表划分成较小的(冗余较少的)表，并定义它们之间的关系。目标是隔离数据，以便只在一个表中添加、删除和修改一个字段，然后使用定义的关系传播到数据库的其余部分。

我刚刚完成了一个项目，其中我的遗留数据库充满了重复数据。这导致了数据质量问题和一些奇怪的代码。一个臃肿丑陋的数据库保证了代码充满了错误和怪异的攻击。简而言之，我的项目急需一家美容院。

## 设置

让我们定义几个表:

```
CREATE TABLE answer (
  answer_id NUMBER PRIMARY KEY,
  question_id NUMBER,
  answer_sequence NUMBER,
  answer_type VARCHAR2(7),
  answer_text VARCHAR2(31),
  updt_date DATE
  );
CREATE TABLE question (
  question_id NUMBER PRIMARY KEY,
  question_text VARCHAR2(31),
  placement NUMBER,
  updt_date DATE
  );
```

看这个例子，我们正在处理你的基本问卷。每个答案都属于一个问题，答案都有特定的类型。根据问题的不同，答案可以是单选框或复选框。

这是我们数据的样子。

第一个问题

```
INSERT INTO question (
  question_id, -- why not just id?
  question_text, -- question gets implied
  placement, -- good, I like it!
  updt_date -- where is create date?
  )
VALUES (
  1,
  'A question',
  1,
  sysdate
  );
INSERT INTO answer (
  answer_id,
  question_id,
  answer_sequence, -- how about placement?
  answer_type,
  answer_text,
  updt_date
  )
VALUES (
  1,
  1,
  1,
  'MLT', -- duplicate
  'First answer',
  sysdate
  );
INSERT INTO answer (
  answer_id,
  question_id,
  answer_sequence,
  answer_type,
  answer_text,
  updt_date
  )
VALUES (
  2,
  1,
  2,
  'MLT', -- duplicate
  'Second answer',
  sysdate
  );
INSERT INTO answer (
  answer_id,
  question_id,
  answer_sequence,
  answer_type,
  answer_text,
  updt_date
  )
VALUES (
  3,
  1,
  3,
  'MULTI', -- what? that’s, messed up!
  'Third answer',
  sysdate
  );
```

第二个问题:

```
INSERT INTO question (
  question_id,
  question_text,
  placement,
  updt_date
  )
VALUES (
  2,
  'Another question',
  2,
  sysdate
  );
INSERT INTO answer (
  answer_id,
  question_id,
  answer_sequence,
  answer_type,
  answer_text,
  updt_date
  )
VALUES (
  4,
  2,
  1,
  'CHK', -- duplicate
  'First answer',
  sysdate
  );
INSERT INTO answer (
  answer_id,
  question_id,
  answer_sequence,
  answer_type,
  answer_text,
  updt_date
  )
VALUES (
  5,
  2,
  2,
  'CHK', -- duplicate
  'Second answer',
  sysdate
  );
```

让我们看看数据:

```
SQL> SELECT answer_id, question_id, answer_sequence, answer_type, updt_date FROM answer;

 ANSWER_ID QUESTION_ID ANSWER_SEQUENCE ANSWER_ UPDT_DATE
---------- ----------- --------------- ------- ---------
         1           1               1 MLT     23-JUN-14
         2           1               2 MLT     23-JUN-14
         3           1               3 MULTI   23-JUN-14
         4           2               1 CHK     23-JUN-14
         5           2               2 CHK     23-JUN-14

SQL> SELECT question_id, placement, updt_date FROM question;

QUESTION_ID  PLACEMENT UPDT_DATE
----------- ---------- ---------
          1          1 23-JUN-14
          2          2 23-JUN-14
```

呸！我有重复的数据、不一致且缺乏想象力的列名和半生不熟的时间戳。直接的后果是，我的数据现在存在严重的数据质量问题。哦天啊。

我们可以做得更好:

```
CREATE TABLE answers (
  id NUMBER CONSTRAINT answers_pk PRIMARY KEY,
  question_id NUMBER,
  placement NUMBER,
  text VARCHAR2(31),
  created_at DATE,
  updated_at DATE
  );
CREATE TABLE questions (
  id NUMBER CONSTRAINT questions_pk PRIMARY KEY,
  type_id NUMBER,
  placement NUMBER,
  text VARCHAR2(31),
  created_at DATE,
  updated_at DATE
  );
CREATE TABLE question_types (
  id NUMBER CONSTRAINT question_types_pk PRIMARY KEY,
  name VARCHAR(7),
  created_at DATE,
  updated_at DATE
  );
```

我这里的数据模型受 Rails 的影响很大。答案类型被完全从答案表中剥离出来，放在问题中。我没有将重复的问题类型名称放在表中，而是放在一个单独的表中，并添加了一个关系。列名简单而直观。

## 陷阱

假设您已经有了针对新模式的工作代码，并通过了测试。现在让我们把注意力转向生产数据。

务必安装 gem:

```
gem install ruby-oci8
```

让我们从这个简单的迁移脚本开始:

```
require 'oci8'

src = OCI8.new('username/password@schema')
tgt = OCI8.new('username/password@schema')
tgt.autocommit = false
src.exec("SELECT question_id,
            (
              SELECT answer.answer_type
              FROM answer
              WHERE answer.question_id = question.question_id
                AND ROWNUM = 1
            ) question_type,
            question_text,
            placement,
            updt_date
          FROM question") do |qr|
  src.exec("SELECT answer_id,
              answer_sequence,
              answer_text,
              updt_date
            FROM answer
            WHERE question_id = :1", qr[0].to_i) do |ar|
    puts ar
  end
end
src.logoff
tgt.logoff
```

OCI8 的错误信息不直观，所以我推荐增量步骤。在这里，我查询`src`数据以确保一切正常。注意我如何使用基本的 Oracle 将参数传递给`exec`方法。我将类型转换为 Ruby 类型，以确保 OCI8 中没有任何东西被误解。`tgt.autocommit = false`让我测试我的迁移代码，直到我准备好提交更改。`.logoff`调用优雅地终止了与 Oracle 的连接。因为我将答案类型附加到问题，所以我必须对其进行子查询。

## 更多示例

有了这个良好的基础，让我们开始填充`questions`表。假设我们在`qr`回调中:

```
QUESTION_TYPE_IDS = { "CHK" => 1, "MLT" => 2 }
tgt.exec("SELECT questions_seq.nextval FROM dual") do |qid|
  tgt.exec("INSERT INTO questions (
              id,
              type_id,
              placement,
              text,
              created_at,
              updated_at
              )
            VALUES (
              :1,
              :2,
              :3,
              :4,
              :5,
              :6
              )",
    qid[0].to_i,
    QUESTION_TYPE_IDS[qr[1]].to_i,
    qr[3].to_i,
    qr[2].to_s[0..30],
    qr[4].to_date,
    qr[4].to_date)
  end
end
```

我使用一个名为`QUESTION_TYPE_IDS`的简单散列将问题类型转换成 id。Oracle 强制主键序列，这迫使我将`INSERT`代码放在`qid`回调中。在我的散列中，我进行了类型转换，以确保没有`nil`值渗入到插入中。如果没有匹配的散列，OCI8 将抛出令人讨厌和难以理解的错误。

对于问题文本，`VARCHAR2`类型有一个我们不想超过的最大长度。为此，只需转换成一个字符串，并执行`[0..30]`来截断该值。即使字符串比限制值短，这也是有效的。

最后，让我们把注意力集中到`answers`桌上。这段代码放在`ar`回调函数中。

```
tgt.exec("SELECT answers_seq.nextval FROM dual") do |aid|
  tgt.exec("INSERT INTO answers (
              id,
              question_id,
              placement,
              text,
              created_at,
              updated_at
              )
            VALUES (
              :1,
              :2,
              :3,
              :4,
              :5,
              :6
              )",
    aid[0].to_i,
    qid[0].to_i,
    ar[1].to_i,
    ar[2].to_s[0..30],
    ar[3].to_date,
    ar[3].to_date)
end
```

这是我们在`questions`迁移中看到的大部分内容。在这里，我们从问题模块的`qid`中映射出`question_id`。这样，我的数据迁移尊重对象之间的关系。

所有这一切都结束了，扳动开关，看看魔法是如何展开的。

```
tgt.autocommit = true
```

现在是最困难的壮举:坐好，把手放在头后，让它做它该做的事。对于我的特定项目，这种迁移需要 15 分钟以上。我感觉自己陷入了深渊的深处，充满了难以言喻的焦虑，时间停止了。但是，它成功地完成了，我安全地降落在另一边。

## 包扎

是时候检查我们最后的`answers`和`questions`表了，看看一切看起来如何。为了简单起见，我将省略`VARCHAR2`字段。我在 Oracle 中使用`sqlplus`:

```
SQL> SELECT id, question_id, placement, created_at, updated_at FROM answers;

        ID QUESTION_ID  PLACEMENT CREATED_A UPDATED_A
---------- ----------- ---------- --------- ---------
         1           1          1 10-JUN-14 10-JUN-14
         2           1          2 10-JUN-14 10-JUN-14
         3           1          3 10-JUN-14 10-JUN-14
         4           2          1 10-JUN-14 10-JUN-14
         5           2          2 10-JUN-14 10-JUN-14

SQL> SELECT id, type_id, placement, created_at, updated_at FROM questions;

        ID    TYPE_ID  PLACEMENT CREATED_A UPDATED_A
---------- ---------- ---------- --------- ---------
         1          2          1 10-JUN-14 10-JUN-14
         2          1          2 10-JUN-14 10-JUN-14
```

太美了。

如果有兴趣，你可以从 GitHub 下载所有的代码样本。

黑客快乐！

## 分享这篇文章