# 构建以太坊 DApps:使用自定义令牌投票

> 原文：<https://www.sitepoint.com/building-ethereum-dapps-voting-custom-tokens/>

在这个关于用以太坊构建 DApps 的系列教程的第 5 部分中，我们讨论了如何给故事添加内容，看看如何给参与者添加从 DAO 购买代币的能力，以及如何向故事中添加提交内容。现在是 DAO 最终形式的时候了:投票、黑名单/取消黑名单以及红利分配和收回。为了更好的衡量，我们将加入一些额外的帮助函数。

如果你迷失在这一切中，完整的源代码可以在[repo](https://github.com/bitfalls/storydao)中找到。

## 投票和提案

我们将发布提案并投票表决。我们需要两个新结构:

```
struct Proposal {
    string description;
    bool executed;
    int256 currentResult;
    uint8 typeFlag; // 1 = delete
    bytes32 target; // ID of the proposal target. I.e. flag 1, target XXXXXX (hash) means proposal to delete submissions[hash]
    uint256 creationDate;
    uint256 deadline;
    mapping (address => bool) voters;
    Vote[] votes;
    address submitter;
}

Proposal[] public proposals;
uint256 proposalCount = 0;
event ProposalAdded(uint256 id, uint8 typeFlag, bytes32 hash, string description, address submitter);
event ProposalExecuted(uint256 id);
event Voted(address voter, bool vote, uint256 power, string justification);

struct Vote {
    bool inSupport;
    address voter;
    string justification;
    uint256 power;
} 
```

一个提案将有一个投票者的映射，以防止人们对一个提案投票两次，以及其他一些元数据应该是不言自明的。投票将是“是”或“否”,并会记住投票人以及他们以某种方式投票的理由和投票权——他们希望为该提案投票的令牌数。我们还添加了一个提议数组，这样我们就可以将它们存储在某个地方，还添加了一个计数器来计算有多少个提议。

现在让我们构建它们的附属函数，从投票函数开始:

```
modifier tokenHoldersOnly() {
    require(token.balanceOf(msg.sender) >= 10**token.decimals());
    _;
}

function vote(uint256 _proposalId, bool _vote, string _description, uint256 _votePower) tokenHoldersOnly public returns (int256) {

    require(_votePower > 0, "At least some power must be given to the vote.");
    require(uint256(_votePower) <= token.balanceOf(msg.sender), "Voter must have enough tokens to cover the power cost.");

    Proposal storage p = proposals[_proposalId];

    require(p.executed == false, "Proposal must not have been executed already.");
    require(p.deadline > now, "Proposal must not have expired.");
    require(p.voters[msg.sender] == false, "User must not have already voted.");

    uint256 voteid = p.votes.length++;
    Vote storage pvote = p.votes[voteid];
    pvote.inSupport = _vote;
    pvote.justification = _description;
    pvote.voter = msg.sender;
    pvote.power = _votePower;

    p.voters[msg.sender] = true;

    p.currentResult = (_vote) ? p.currentResult + int256(_votePower) : p.currentResult - int256(_votePower);
    token.increaseLockedAmount(msg.sender, _votePower);

    emit Voted(msg.sender, _vote, _votePower, _description);
    return p.currentResult;
} 
```

请注意函数修饰符:通过将该修饰符添加到我们的契约中，我们可以将其附加到任何未来的函数，并确保只有令牌持有者才能执行该函数。这是可重复使用的安全检查！

vote 函数做一些健全性检查，如投票权为正，投票人有足够的令牌实际投票等。然后，我们从仓库中取出建议，并确保它既没有过期也没有被执行。对一项已经完成的提案进行投票是没有意义的。我们还需要确保这个人还没有投票。我们可以允许改变投票权，但是这给 DAO 带来了一些漏洞，比如人们在最后一分钟撤回他们的投票等等。也许是未来版本的候选？

然后，我们在提案中注册一个新的投票，更改当前结果以便于查找分数，最后发出投票事件。但是什么是`token.increaseLockedAmount`？

这一逻辑增加了用户的锁定令牌数量。该函数只能由令牌契约的所有者执行(此时希望是 DAO ),并且将防止用户发送超过其帐户注册的锁定数量的令牌。这个锁在提议失败或执行后被解除。

现在让我们编写建议删除条目的函数。

## 投票删除和黑名单

如本系列第 1 部分中所述，我们计划了三个条目删除功能:

1.  **删除条目**:投票确认后，目标条目被删除。投票时间: **48 小时**。
2.  **紧急移除入口【仅限所有者】**:只能由所有者触发。当通过投票确认时，目标条目被删除。投票时间: **24 小时**。
3.  **紧急删除图像【仅所有者】**:仅适用于图像条目。只能由所有者触发。当通过投票确认时，目标条目被删除。投票时间: **4 小时**。

单个地址条目的五次删除导致黑名单。

让我们看看我们现在能做些什么。首先，删除功能:

```
modifier memberOnly() {
    require(whitelist[msg.sender]);
    require(!blacklist[msg.sender]);
    _;
}

function proposeDeletion(bytes32 _hash, string _description) memberOnly public {

    require(submissionExists(_hash), "Submission must exist to be deletable");

    uint256 proposalId = proposals.length++;
    Proposal storage p = proposals[proposalId];
    p.description = _description;
    p.executed = false;
    p.creationDate = now;
    p.submitter = msg.sender;
    p.typeFlag = 1;
    p.target = _hash;

    p.deadline = now + 2 days;

    emit ProposalAdded(proposalId, 1, _hash, _description, msg.sender);
    proposalCount = proposalId + 1;
}

function proposeDeletionUrgent(bytes32 _hash, string _description) onlyOwner public {

    require(submissionExists(_hash), "Submission must exist to be deletable");

    uint256 proposalId = proposals.length++;
    Proposal storage p = proposals[proposalId];
    p.description = _description;
    p.executed = false;
    p.creationDate = now;
    p.submitter = msg.sender;
    p.typeFlag = 1;
    p.target = _hash;

    p.deadline = now + 12 hours;

    emit ProposalAdded(proposalId, 1, _hash, _description, msg.sender);
    proposalCount = proposalId + 1;
}    

function proposeDeletionUrgentImage(bytes32 _hash, string _description) onlyOwner public {

    require(submissions[_hash].image == true, "Submission must be existing image");

    uint256 proposalId = proposals.length++;
    Proposal storage p = proposals[proposalId];
    p.description = _description;
    p.executed = false;
    p.creationDate = now;
    p.submitter = msg.sender;
    p.typeFlag = 1;
    p.target = _hash;

    p.deadline = now + 4 hours;

    emit ProposalAdded(proposalId, 1, _hash, _description, msg.sender);
    proposalCount = proposalId + 1;
} 
```

一旦被提议，提议就被添加到提议列表中，并记录哪个条目是条目散列的目标。保存描述并添加一些默认值，根据建议类型计算截止日期。提议添加事件被发出，提议总数增加。

接下来，让我们看看如何执行一个建议。要被执行，提案必须有足够的票数，并且必须超过截止日期。执行函数将接受要执行的建议的 ID。没有简单的方法让 EVM 立刻执行所有悬而未决的提议。可能会有太多的事务等待执行，它们会对 DAO 中的数据进行大的修改，这可能会超过以太坊块的 gas 限制，从而导致事务失败。构建一个任何人都可以使用定义良好的规则调用的手动执行函数要容易得多，因此社区可以密切关注需要执行的提议。

```
function executeProposal(uint256 _id) public {
    Proposal storage p = proposals[_id];
    require(now >= p.deadline && !p.executed);

    if (p.typeFlag == 1 && p.currentResult > 0) {
        assert(deleteSubmission(p.target));
    }

    uint256 len = p.votes.length;
    for (uint i = 0; i < len; i++) {
        token.decreaseLockedAmount(p.votes[i].voter, p.votes[i].power);
    }

    p.executed = true;
    emit ProposalExecuted(_id);
} 
```

我们通过 ID 获取提议，检查它是否满足尚未执行和截止日期已过的要求，然后如果提议的类型是删除提议并且投票结果是肯定的，我们使用已经编写好的删除函数，最后发出我们添加的新事件(将其添加到契约的顶部)。`assert`调用与`require`语句的作用相同:assert 通常在您“断言”结果为真时使用。Require 用于先决条件。它们在功能上是相同的，不同之处在于`assert`语句在失败时不能接受消息参数。该功能通过解锁该提案中所有投票的令牌来结束。

我们可以使用相同的方法来添加其他类型的提议，但是首先，让我们更新`deleteSubmission`函数来禁止在其帐户上删除了五个或更多内容的用户:这意味着他们一直在提交社区投票反对的内容。让我们更新一下`deleteSubmission`函数:

```
function deleteSubmission(bytes32 hash) internal returns (bool) {
    require(submissionExists(hash), "Submission must exist to be deletable.");
    Submission storage sub = submissions[hash];

    sub.exists = false;
    deletions[submissions[hash].submitter] += 1;
    if (deletions[submissions[hash].submitter] >= 5) {
        blacklistAddress(submissions[hash].submitter);
    }

    emit SubmissionDeleted(
        sub.index,
        sub.content,
        sub.image,
        sub.submitter
    );

    nonDeletedSubmissions -= 1;
    return true;
} 
```

那更好。五次删除自动列入黑名单。然而，不给黑名单上的地址一个挽回的机会是不公平的。我们还需要定义黑名单功能本身。让我们做这两件事，并将取消清单费用设置为，例如，0.05 以太。

```
function blacklistAddress(address _offender) internal {
    require(blacklist[_offender] == false, "Can't blacklist a blacklisted user :/");
    blacklist[_offender] == true;
    token.increaseLockedAmount(_offender, token.getUnlockedAmount(_offender));
    emit Blacklisted(_offender, true);
}

function unblacklistMe() payable public {
    unblacklistAddress(msg.sender);
}

function unblacklistAddress(address _offender) payable public {
    require(msg.value >= 0.05 ether, "Unblacklisting fee");
    require(blacklist[_offender] == true, "Can't unblacklist a non-blacklisted user :/");
    require(notVoting(_offender), "Offender must not be involved in a vote.");
    withdrawableByOwner = withdrawableByOwner.add(msg.value);
    blacklist[_offender] = false;
    token.decreaseLockedAmount(_offender, token.balanceOf(_offender));
    emit Blacklisted(_offender, false);
}

function notVoting(address _voter) internal view returns (bool) {
    for (uint256 i = 0; i < proposalCount; i++) {
        if (proposals[i].executed == false && proposals[i].voters[_voter] == true) {
            return false;
        }
    }
    return true;
} 
```

请注意，黑名单帐户的令牌会被锁定，直到他们提交取消黑名单费用。

## 其他类型的投票

利用我们上面写的函数的灵感，试着写其他的提议。对于剧透，查看该项目的 [GitHub repo](https://github.com/bitfalls/storydao) 并从那里复制最终代码。为了简洁起见，让我们继续讨论 DAO 中的其他函数。

## 章节结尾

一旦达到故事的时间或章节限制，就该结束故事了。任何人都可以在允许提取红利的日期之后调用结束函数。首先，我们需要一个新的 StoryDAO 属性和事件:

```
bool public active = true;
event StoryEnded(); 
```

然后，让我们构建函数:

```
function endStory() storyActive external {
    withdrawToOwner();
    active = false;
    emit StoryEnded();
} 
```

简单:它在将收取的费用发送给所有者后停用故事，并发出事件。但实际上，这并没有真正改变整个 DAO 中的任何东西:其他函数并没有对它的结束做出反应。因此，让我们建立另一个修改器:

```
modifier storyActive() {
    require(active == true);
    _;
} 
```

然后，我们将这个修饰符添加到除了`withdrawToOwner`之外的所有函数中，就像这样:

```
function whitelistAddress(address _add) storyActive public payable { 
```

如果在 DAO 中留下任何标记，让我们将它们收回并接管这些标记的所有权，以便能够在以后的另一个故事中使用它们:

```
function withdrawLeftoverTokens() external onlyOwner {
    require(active == false);
    token.transfer(msg.sender, token.balanceOf(address(this)));
    token.transferOwnership(msg.sender);
}

function unlockMyTokens() external {
    require(active == false);
    require(token.getLockedAmount(msg.sender) > 0);

    token.decreaseLockedAmount(msg.sender, token.getLockedAmount(msg.sender));
} 
```

`unlockMyTokens`函数用于解锁所有锁定的令牌，以防某些令牌为特定用户锁定。它不应该发生，这个功能应该通过大量的测试来消除。

## 股息分配和提取

既然故事已经结束，收取的提交费用需要分配给所有令牌持有者。我们可以重复使用我们的白名单来标记每个提取费用的人:

```
function withdrawDividend() memberOnly external {
    require(active == false);
    uint256 owed = address(this).balance.div(whitelistedNumber);
    msg.sender.transfer(owed);
    whitelist[msg.sender] = false;
    whitelistedNumber--;
} 
```

如果这些股息没有在一定期限内提取，所有者可以获得剩余部分:

```
function withdrawEverythingPostDeadline() external onlyOwner {
    require(active == false);
    require(now > deadline + 14 days);
    owner.transfer(address(this).balance);
} 
```

对于家庭作业，考虑重用这个已部署的 smart 契约、清除其数据、保留令牌并在不重新部署的情况下重新开始下一章的难易程度。尝试自己做这件事，并关注回购，以便将来更新涵盖这一点的教程系列！还要考虑额外的激励机制:也许账户中的代币数量会影响他们从故事结尾获得的红利？你的想象力是极限！

## 部署问题

鉴于我们的合同现在相当大，部署和/或测试它可能会超过以太坊块的气体限制。这就是限制大型应用程序部署在以太坊网络上的原因。无论如何都要部署它，尝试在编译期间使用代码优化器，方法是更改`truffle.js`文件以包含用于优化的`solc`设置，如下所示:

```
// ...

module.exports = {
  solc: {
    optimizer: {
      enabled: true,
      runs: 200
    }
  },
  networks: {
    development: {
// ... 
```

这将在代码中运行优化器 200 次，以找到可以在部署前缩小、删除或抽象的区域，这将显著降低部署成本。

## 结论

我们详尽的 DAO 开发到此结束——但是课程还没有结束！我们仍然需要为这个故事构建和部署 UI。幸运的是，由于后端完全托管在区块链上，构建前端要简单得多。让我们在本系列的倒数第二部分来看看。

## 分享这篇文章