# 有用的内置 Node.js APIs

> 原文：<https://www.sitepoint.com/useful-built-in-node-js-apis/>

我们已经编译了一个列表，列出了内置于标准 Node.js 运行时的最常用和最有用的[API](https://nodejs.org/api/)。对于每个模块，你都会找到简单的英语解释和例子来帮助你理解。

本指南改编自我的课程 **[Node.js:新手到忍者](https://www.sitepoint.com/premium/books/node-js-novice-to-ninja/read/1/)** 。查看它，跟随综合课程来构建您自己的多用户实时聊天应用程序。它还包括测验，视频，运行你自己的 docker 容器的代码。

当[构建您的第一个 Node.js](https://www.sitepoint.com/build-simple-beginner-app-node-bootstrap-mongodb/) 应用程序时，了解 Node 提供了哪些现成的实用程序和 API 来帮助满足常见用例及开发需求是很有帮助的。

### 有用的 Node.js APIs

*   [**进程**](#h-process) :检索环境变量、参数、CPU 使用和报告的信息。
*   [**OS**](#h-os) :检索节点运行的 OS 和系统相关信息:CPU、操作系统版本、主目录等。
*   **[Util](#h-util)** :一组有用且常用的方法，有助于解码文本、类型检查和比较对象。
*   **[网址](#h-url)** :轻松创建和解析网址。
*   **[文件系统 API](#h-file-system-api)** :与文件系统交互，创建、读取、更新、删除文件、目录、权限。
*   **[事件](#h-events)** :用于发出和订阅 Node.js 中的事件，工作方式类似于客户端事件监听器。
*   **[流](#h-streams)** :用于以更小、更易管理的块来处理大量数据，以避免内存问题。
*   **[工作线程](#h-worker-threads)** :用于将函数的执行分开在单独的线程上，以避免瓶颈。对于 CPU 密集型 JavaScript 操作非常有用。
*   **[子流程](#h-child-processes)** :允许您运行子流程，您可以根据需要监控和终止这些子流程。
*   **[集群](#h-clusters)** :允许您跨内核分叉任意数量的相同进程，以更高效地处理负载。

## 过程

[`process`对象](https://nodejs.org/dist/latest/docs/api/process.html)提供了关于 Node.js 应用程序以及控制方法的信息。使用它来获取环境变量、CPU 和内存使用等信息。`process`是全局可用的:您可以在没有`import`的情况下使用它，尽管 Node.js 文档建议您显式引用它:

```
import process from 'process';
```

*   [`process.argv`](https://nodejs.org/dist/latest/docs/api/process.html#processargv) 返回一个数组，其中前两项是 Node.js 可执行文件路径和脚本名。索引 2 处的项是传递的第一个参数。
*   [`process.env`](https://nodejs.org/dist/latest/docs/api/process.html#processenv) :返回包含环境名/值对的对象——如`process.env.NODE_ENV`。
*   [`process.cwd()`](https://nodejs.org/dist/latest/docs/api/process.html#processcwd) :返回当前工作目录。
*   [`process.platform`](https://nodejs.org/dist/latest/docs/api/process.html#processplatform) :返回标识操作系统的字符串:`'aix'`、`'darwin'` (macOS)、`'freebsd'`、`'linux'`、`'openbsd'`、`'sunos'`或`'win32'` (Windows)。
*   [`process.uptime()`](https://nodejs.org/dist/latest/docs/api/process.html#processuptime) :返回 Node.js 进程运行的秒数。
*   [`process.cpuUsage()`](https://nodejs.org/dist/latest/docs/api/process.html#processcpuusagepreviousvalue) :返回当前进程的用户和系统 CPU 时间使用情况——如`{ user: 12345, system: 9876 }`。将对象传递回方法以获取相对读数。
*   [`process.memoryUsage()`](https://nodejs.org/dist/latest/docs/api/process.html#processmemoryusage) :返回一个以字节为单位描述内存使用情况的对象。
*   [`process.version`](https://nodejs.org/dist/latest/docs/api/process.html#processversion) :返回 Node.js 版本字符串——如`18.0.0`。
*   [`process.report`](https://nodejs.org/dist/latest/docs/api/process.html#processreport) :生成[诊断报告](https://nodejs.org/dist/latest/docs/api/report.html)。
*   [`process.exit(code)`](https://nodejs.org/dist/latest/docs/api/process.html#processexitcode) :退出当前应用。使用退出代码`0`表示成功，或在必要时使用[适当的错误代码](https://nodejs.org/dist/latest/docs/api/process.html#exit-codes)。

## 操作系统（Operating System）

[`os` API](https://nodejs.org/dist/latest/docs/api/os.html) 与`process`有相似之处(参见上面的“进程”部分)，但是它也可以返回关于运行 Node.js 的操作系统的信息。这提供了操作系统版本、CPU 和运行时间等信息。

*   [`os.cpus()`](https://nodejs.org/dist/latest/docs/api/os.html#oscpus) :返回包含每个逻辑 CPU 内核信息的对象数组。下面的“集群”部分引用了`os.cpus()`来派生流程。在 16 核 CPU 上，运行 16 个 Node.js 应用程序实例来提高性能。
*   [`os.hostname()`](https://nodejs.org/dist/latest/docs/api/os.html#oshostname):OS 主机名。
*   [`os.version()`](https://nodejs.org/dist/latest/docs/api/os.html#osversion) :标识 OS 内核版本的字符串。
*   [`os.homedir()`](https://nodejs.org/dist/latest/docs/api/os.html#oshomedir) :用户主目录的完整路径。
*   [`os.tmpdir()`](https://nodejs.org/dist/latest/docs/api/os.html#ostmpdir) :操作系统默认临时文件目录的完整路径。
*   [`os.uptime()`](https://nodejs.org/dist/latest/docs/api/os.html#osuptime) :操作系统运行的秒数。

## 跑龙套

[`util`模块](https://nodejs.org/dist/latest/docs/api/util.html)提供了一系列有用的 JavaScript 方法。最有用的一个是 [`util.promisify(function)`](https://nodejs.org/dist/latest/docs/api/util.html#utilpromisifyoriginal) ，它采用错误优先的回调样式函数，返回基于承诺的函数。Util 模块还可以帮助处理常见的模式，如解码文本、类型检查和检查对象。

*   [`util.callbackify(function)`](https://nodejs.org/dist/latest/docs/api/util.html#utilcallbackifyoriginal) :取一个返回承诺的函数，返回一个基于回调的函数。
*   [`util.isDeepStrictEqual(object1, object2)`](https://nodejs.org/dist/latest/docs/api/util.html#utilisdeepstrictequalval1-val2) :当两个对象完全相等时返回`true`(所有子属性必须匹配)。
*   [`util.format(format, [args])`](https://nodejs.org/dist/latest/docs/api/util.html#utilformatformat-args) :返回一个使用类似 [printf 格式](https://en.wikipedia.org/wiki/Printf_format_string)的字符串。
*   [`util.inspect(object, options)`](https://nodejs.org/dist/latest/docs/api/util.html#utilinspectobject-options) :返回调试对象的字符串表示。这类似于使用`console.dir(object, { depth: null, color: true });`。
*   [`util.stripVTControlCharacters(str)`](https://nodejs.org/dist/latest/docs/api/util.html#utilstripvtcontrolcharactersstr) :从字符串中剥离 ANSI 转义码。
*   [`util.types`](https://nodejs.org/dist/latest/docs/api/util.html#utiltypes) 为常见的 JavaScript 和 Node.js 值提供类型检查。例如:

```
import util from 'util';

util.types.isDate( new Date() ); // true
util.types.isMap( new Map() );  // true
util.types.isRegExp( /abc/ ); // true
util.types.isAsyncFunction( async () => {} ); // true
```

## 统一资源定位器

[`URL`](https://nodejs.org/dist/latest/docs/api/url.html) 是另一个全局对象，让您安全地创建、解析和修改 web URLs。这对于从 URL 中快速提取协议、端口、参数和散列非常有用，而不需要借助正则表达式。例如:

```
{
  href: 'https://example.org:8000/path/?abc=123#target',
  origin: 'https://example.org:8000',
  protocol: 'https:',
  username: '',
  password: '',
  host: 'example.org:8000',
  hostname: 'example.org',
  port: '8000',
  pathname: '/path/',
  search: '?abc=123',
  searchParams: URLSearchParams { 'abc' => '123' },
  hash: '#target'
}
```

您可以查看和更改任何属性。例如:

```
myURL.port = 8001;
console.log( myURL.href );
// https://example.org:8001/path/?abc=123#target
```

然后，您可以使用 [`URLSearchParams` API](https://nodejs.org/dist/latest/docs/api/url.html#class-urlsearchparams) 来修改查询字符串值。例如:

```
myURL.searchParams.delete('abc');
myURL.searchParams.append('xyz', 987);
console.log( myURL.search );
// ?xyz=987
```

还有一些方法可以将文件系统路径转换成 URL，然后再将 T2 转换成 URL。

[`dns`模块](https://nodejs.org/dist/latest/docs/api/dns.html)提供了域名解析功能，因此您可以查找 IP 地址、域名服务器、TXT 记录和其他域名信息。

## 文件系统 API

[`fs` API](https://nodejs.org/dist/latest/docs/api/fs.html) 可以创建、读取、更新和删除文件、目录和权限。Node.js 运行时的最新版本在`fs/promises` 中提供了基于[承诺的函数，这使得管理异步文件操作变得更加容易。](https://nodejs.org/dist/latest/docs/api/fs.html#promises-api)

您将经常使用`fs`和 [`path`](https://nodejs.org/dist/latest/docs/api/path.html) 来解析不同操作系统上的文件名。

以下示例模块使用 [`stat`](https://nodejs.org/dist/latest/docs/api/fs.html#fspromisesstatpath-options) 和 [`access`](https://nodejs.org/dist/latest/docs/api/fs.html#fspromisesaccesspath-mode) 方法返回关于文件系统对象的信息:

```
// fetch file information
import { constants as fsConstants } from 'fs';
import { access, stat } from 'fs/promises';

export async function getFileInfo(file) {

  const fileInfo = {};

  try {
    const info = await stat(file);
    fileInfo.isFile = info.isFile();
    fileInfo.isDir = info.isDirectory();
  }
  catch (e) {
    return { new: true };
  }

  try {
    await access(file, fsConstants.R_OK);
    fileInfo.canRead = true;
  }
  catch (e) {}

  try {
    await access(file, fsConstants.W_OK);
    fileInfo.canWrite = true;
  }
  catch (e) {}

  return fileInfo;

}
```

当传递一个文件名时，该函数返回一个包含该文件信息的对象。例如:

```
{
  isFile: true,
  isDir: false,
  canRead: true,
  canWrite: true
}
```

主`filecompress.js`脚本使用`path.resolve()`将命令行上传递的输入和输出文件名解析为绝对文件路径，然后使用上面的`getFileInfo()`获取信息:

```
#!/usr/bin/env node
import path from 'path';
import { readFile, writeFile } from 'fs/promises';
import { getFileInfo } from './lib/fileinfo.js';

// check files
let
  input = path.resolve(process.argv[2] || ''),
  output = path.resolve(process.argv[3] || ''),
  [ inputInfo, outputInfo ] = await Promise.all([ getFileInfo(input), getFileInfo(output) ]),
  error = [];
```

代码验证路径，并在必要时以错误消息终止:

```
// use input file name when output is a directory
if (outputInfo.isDir && outputInfo.canWrite && inputInfo.isFile) {
  output = path.resolve(output, path.basename(input));
}

// check for errors
if (!inputInfo.isFile || !inputInfo.canRead) error.push(`cannot read input file ${ input }`);
if (input === output) error.push('input and output files cannot be the same');

if (error.length) {

  console.log('Usage: ./filecompress.js [input file] [output file|dir]');
  console.error('\n  ' + error.join('\n  '));
  process.exit(1);

}
```

然后使用 [`readFile()`](https://nodejs.org/dist/latest/docs/api/fs.html#fspromisesreadfilepath-options) 将整个文件读入一个名为`content`的字符串:

```
// read file
console.log(`processing ${ input }`);
let content;

try {
  content = await readFile(input, { encoding: 'utf8' });
}
catch (e) {
  console.log(e);
  process.exit(1);
}

let lengthOrig = content.length;
console.log(`file size ${ lengthOrig }`);
```

JavaScript 正则表达式删除注释和空白:

```
// compress content
content = content
  .replace(/\n\s+/g, '\n')                // trim leading space from lines
  .replace(/\/\/.*?\n/g, '')              // remove inline // comments
  .replace(/\s+/g, ' ')                   // remove whitespace
  .replace(/\/\*.*?\*\//g, '')            // remove /* comments */
  .replace(/<!--.*?-->/g, '')             // remove <!-- comments -->
  .replace(/\s*([<>(){}}[\]])\s*/g, '$1') // remove space around brackets
  .trim();

let lengthNew = content.length;
```

使用 [`writeFile()`](https://nodejs.org/dist/latest/docs/api/fs.html#fspromiseswritefilefile-data-options) 将结果字符串输出到文件，并显示保存状态消息:

```
let lengthNew = content.length;

// write file
console.log(`outputting ${output}`);
console.log(`file size ${ lengthNew } - saved ${ Math.round((lengthOrig - lengthNew) / lengthOrig * 100) }%`);

try {
  content = await writeFile(output, content);
}
catch (e) {
  console.log(e);
  process.exit(1);
}
```

使用示例 HTML 文件运行项目代码:

```
node filecompress.js ./test/example.html ./test/output.html
```

## 事件

当事情发生时，你经常需要执行多个函数。例如，用户在您的应用程序上注册，因此代码必须将他们的详细信息添加到数据库中，启动一个新的登录会话，并发送一封欢迎电子邮件。事件模块:

```
// example pseudo code
async function userRegister(name, email, password) {

  try {

    await dbAddUser(name, email, password);
    await new UserSession(email);
    await emailRegister(name, email);

  }
  catch (e) {
    // handle error
  }

}
```

这一系列函数调用与用户注册紧密相关。进一步的活动引起进一步的函数调用。例如:

```
// updated pseudo code
try {

  await dbAddUser(name, email, password);
  await new UserSession(email);
  await emailRegister(name, email);

  await crmRegister(name, email); // register on customer system
  await emailSales(name, email);  // alert sales team

}
```

您可以在这个单一的、不断增长的代码块中管理几十个调用。

Node.js [事件 API](https://nodejs.org/dist/latest/docs/api/events.html) 提供了另一种使用发布-订阅模式构建代码的方法。在用户的数据库记录被创建后，`userRegister()`函数可以*发出*一个事件——可能命名为`newuser`。

任意数量的事件处理函数可以订阅和响应`newuser`事件；没有必要改变`userRegister()`功能。每个处理程序独立于其他处理程序运行，因此它们可以按任何顺序执行。

#### 客户端 JavaScript 中的事件

客户端 JavaScript 中经常使用事件和处理函数，例如，当用户单击某个元素时运行一个函数:

```
// client-side JS click handler
  document.getElementById('myelement').addEventListener('click', e => {

    // output information about the event
    console.dir(e);

  });
```

在大多数情况下，您会为用户或浏览器事件附加处理程序，尽管您可以引发自己的[自定义事件](https://developer.mozilla.org/docs/Web/API/CustomEvent)。Node.js 中的事件处理在概念上是相似的，但是 API 是不同的。

发出事件的对象必须是 Node.js [`EventEmitter`类](https://nodejs.org/dist/latest/docs/api/events.html#class-eventemitter)的实例。它们有一个引发新事件的 [`emit()`](https://nodejs.org/dist/latest/docs/api/events.html#emitteremiteventname-args) 方法和一个附加处理程序的 [`on()`方法](https://nodejs.org/dist/latest/docs/api/events.html#emitteroneventname-listener)。

[事件示例项目](https://github.com/spbooks/ultimatenode1/tree/main/ch12/event)提供了一个在预定义的时间间隔触发`tick`事件的类。`./lib/ticker.js`模块输出一个`default class`，该`extends EventEmitter`:

```
// emits a 'tick' event every interval
import EventEmitter from 'events';
import { setInterval, clearInterval } from 'timers';

export default class extends EventEmitter {
```

它的`constructor`必须调用父构造函数。然后它将`delay`参数传递给一个`start()`方法:

```
constructor(delay) {
  super();
  this.start(delay);
}
```

`start()`方法检查 delay 是否有效，必要时重置当前计时器，并设置新的`delay`属性:

```
start(delay) {

  if (!delay || delay == this.delay) return;

  if (this.interval) {
    clearInterval(this.interval);
  }

  this.delay = delay;
```

然后它启动一个新的间隔计时器，运行事件名为`"tick"`的`emit()`方法。该事件的订阅者收到一个对象，该对象具有自 Node.js 应用程序启动以来的延迟值和[秒数](https://nodejs.org/dist/latest/docs/api/perf_hooks.html#performancenow) :C

```
// start timer
    this.interval = setInterval(() => {

      // raise event
      this.emit('tick', {
        delay:  this.delay,
        time:   performance.now()
      });

    }, this.delay);

  }

}
```

主`event.js`入口脚本导入模块，并设置一秒钟的`delay`(`1000`毫秒):复制

```
// create a ticker
import Ticker from './lib/ticker.js';

// trigger a new event every second
const ticker = new Ticker(1000);
```

它附加了每次`tick`事件发生时触发的处理函数:

```
// add handler
ticker.on('tick', e => {
  console.log('handler 1 tick!', e);
});

// add handler
ticker.on('tick', e => {
  console.log('handler 2 tick!', e);
});
```

第三个处理程序仅使用 [`once()`](https://nodejs.org/dist/latest/docs/api/events.html#emitteronceeventname-listener) 方法触发第一个`tick`事件:

```
// add handler
ticker.once('tick', e => {
  console.log('handler 3 tick!', e);
});
```

最后，输出当前的侦听器数量:

```
// show number of listenersconsole.log(`listeners: ${ // show number of listeners
console.log(`listeners: ${ ticker.listenerCount('tick') }`);
```

用`node event.js`运行项目代码。

输出显示处理器 3 触发一次，而处理器 1 和 2 每隔`tick`运行一次，直到应用程序终止。

## 流

上面的文件系统示例代码(在“文件系统”一节中)在输出缩小的结果之前将整个文件读入内存。如果文件比可用的 RAM 大怎么办？Node.js 应用程序会因“内存不足”错误而失败。

解决方案是**流**。这将处理更小、更易管理的传入数据。流可以是:

*   **可读**:来自文件、HTTP 请求、TCP 套接字、stdin 等。
*   **可写**:对一个文件，一个 HTTP 响应，TCP 套接字，stdout 等。
*   **duplex** :既可读又可写的流
*   **转换**:转换数据的双工流

每个数据块作为一个 [`Buffer`对象](https://nodejs.org/dist/latest/docs/api/buffer.html)返回，它代表一个固定长度的字节序列。您可能需要[将它转换成字符串](https://nodejs.org/dist/latest/docs/api/buffer.html#buftostringencoding-start-end)或其他合适的类型进行处理。

示例代码有一个 [`filestream`项目](https://github.com/spbooks/ultimatenode1/tree/main/ch12/filestream)，它使用一个转换流来解决`filecompress`项目中的文件大小问题。和以前一样，在声明一个扩展了 [`Transform`](https://nodejs.org/dist/latest/docs/api/stream.html#class-streamtransform) 的`Compress`类之前，它接受并验证`input`和`output`文件名:

```
import { createReadStream, createWriteStream } from 'fs';
import { Transform } from 'stream';

// compression Transform
class Compress extends Transform {

  constructor(opts) {
    super(opts);
    this.chunks = 0;
    this.lengthOrig = 0;
    this.lengthNew = 0;
  }

  _transform(chunk, encoding, callback) {

    const
      data = chunk.toString(),                  // buffer to string
      content = data
        .replace(/\n\s+/g, '\n')                // trim leading spaces
        .replace(/\/\/.*?\n/g, '')              // remove // comments
        .replace(/\s+/g, ' ')                   // remove whitespace
        .replace(/\/\*.*?\*\//g, '')            // remove /* comments */
        .replace(/<!--.*?-->/g, '')             // remove <!-- comments -->
        .replace(/\s*([<>(){}}[\]])\s*/g, '$1') // remove bracket spaces
        .trim();

    this.chunks++;
    this.lengthOrig += data.length;
    this.lengthNew += content.length;

    this.push( content );
    callback();

  }

}
```

当新的`chunk`数据准备好时，调用 [`_transform`方法](https://nodejs.org/dist/latest/docs/api/stream.html#transform_transformchunk-encoding-callback)。它作为一个`Buffer`对象被接收，并被转换成一个字符串，然后使用`push()`方法输出。一旦块处理完成，就会调用一个`callback()`函数。

应用程序启动[文件读取](https://nodejs.org/dist/latest/docs/api/fs.html#fscreatereadstreampath-options)和[写入流](https://nodejs.org/dist/latest/docs/api/fs.html#fscreatewritestreampath-options)，并实例化一个新的`compress`对象:

```
// process streamconst  readStream = createReadStream(input),  wr// process stream
const
  readStream = createReadStream(input),
  writeStream = createWriteStream(output),
  compress = new Compress();

console.log(`processing ${ input }`)
```

传入的文件读取流定义了 [`.pipe()`方法](https://nodejs.org/dist/latest/docs/api/stream.html#readablepipedestination-options)，这些方法通过一系列可能(也可能不会)改变内容的函数来输入数据。在输出通过*管道*传输到可写文件之前，数据通过`compress`转换通过*管道*传输。一旦流结束，最终的 [`on('finish')`事件处理函数](https://nodejs.org/dist/latest/docs/api/stream.html#event-finish)执行:

```
readStream.pipe(compress).pipe(writeStream).on('finish', () => {
  console.log(`file size ${ compress.lengthOrig }`);  console.log(`output ${ output }`);  console.log(`chunks     readStream.pipe(compress).pipe(writeStream).on('finish', () => {

  console.log(`file size  ${ compress.lengthOrig }`);
  console.log(`output     ${ output }`);
  console.log(`chunks     ${ compress.chunks }`);
  console.log(`file size  ${ compress.lengthNew } - saved ${ Math.round((compress.lengthOrig - compress.lengthNew) / compress.lengthOrig * 100) }%`);

});
```

使用任意大小的示例 HTML 文件运行项目代码:

```
node filestream.js ./test/example.html ./test/output.html
```

![filestream.js output](img/2fed49c9f9be1abb9104e3c89b3b7184.png)

这是 Node.js 流的一个小演示。流处理是一个复杂的主题，您可能不经常使用它们。在某些情况下，诸如 Express 之类的模块在幕后使用流，但是从您那里抽象出复杂性。

您还应该意识到数据分块的挑战。块可以是任意大小，并且以不方便的方式分割传入的数据。考虑缩短这段代码:

```
<script type="module">
  // example script
  console.log('loaded');
</script>
```

两个组块可以依次到达:

```
<script type="module">
// example
```

并且:

```
<script>
  console.log('loaded');
</script>
```

独立处理每个块会导致以下无效的精简脚本:

```
<script type="module">script console.log('loaded');</script>
```

解决方案是对每个块进行预解析，并将其分割成可以处理的完整部分。在某些情况下，块(或块的一部分)将被添加到下一个块的开头。

缩小最好应用于整行，尽管会出现额外的复杂性，因为`<!-- -->`和`/* */`注释可以跨越多行。以下是每个传入块的可能算法:

1.  将以前块中保存的任何数据追加到新块的开头。
2.  从大块上移除任何完整的`<!--`到`-->`和`/*`到`*/`部分。
3.  将剩余的块分成两部分，其中`part2`从找到的第一个`<!--`或`/*`开始。如果存在，从`part2`中移除除该符号之外的更多内容。如果都没有找到，则在最后一个回车符处拆分。如果没有找到，将`part1`设置为空字符串，将`part2`设置为整个块。如果`part2`变得非常大——可能超过 100，000 个字符，因为没有回车符——将`part2`追加到`part1`并将`part2`设置为空字符串。这将确保保存的部分不会无限增长。
4.  缩小并输出`part1`。
5.  保存`part2`(添加到下一个块的开头)。

对于每个传入的块，该过程再次运行。

这是你的下一个编码挑战— *如果你愿意接受的话！*

## 工作线程

来自[文档](https://nodejs.org/api/worker_threads.html#worker-threads):“Workers(线程)对于执行 CPU 密集型 JavaScript 操作很有用。它们对 I/O 密集型工作帮助不大。Node.js 内置的异步 I/O 操作比 Workers 更高效”。

假设用户可以在您的 Express 应用程序中触发一个复杂的、十秒钟的 JavaScript 计算。计算将成为一个瓶颈，使所有用户的处理停止。在它完成之前，您的应用程序不能处理任何请求或运行其他功能。

#### 异步计算

处理来自文件或数据库的数据的复杂计算可能问题较少，因为每个阶段在等待数据到达时都是异步运行的。处理发生在事件循环的单独迭代中。

然而，仅用 JavaScript 编写的长时间运行的计算——如图像处理或机器学习算法——将占用事件循环的当前迭代。

一个解决方案是[工作线程](https://nodejs.org/dist/latest/docs/api/worker_threads.html)。这些类似于[浏览器 web workers](https://developer.mozilla.org/docs/Web/API/Web_Workers_API) ，在一个单独的线程上启动一个 JavaScript 进程。主线程和工作线程可以交换消息来触发或终止处理。

#### 工人和事件循环

Workers 对于 CPU 密集型 JavaScript 操作非常有用，尽管主 Node.js 事件循环仍然应该用于异步 I/O 活动。

示例代码有一个 [`worker`项目](https://github.com/spbooks/ultimatenode1/tree/main/ch12/worker)，它导出了`lib/dice.js`中的一个`diceRun()`函数。这将投掷任意数量的 N 面骰子若干次，并记录总分数的计数(这将产生一条[正态分布曲线](https://en.wikipedia.org/wiki/Normal_distribution)):

```
// dice throwing
export function diceRun(runs = 1, dice = 2, sides = 6) {

  const stat = [];

  while (runs > 0) {

    let sum = 0;
    for (let d = dice; d > 0; d--) {
      sum += Math.floor( Math.random() * sides ) + 1;
    }

    stat[sum] = (stat[sum] || 0) + 1;

    runs--;
  }

  return stat;

}
```

`index.js`中的代码启动一个每秒运行一次的进程，并输出一条消息:

```
// run process every second
const timer = setInterval(() => {
  console.log('  another process');
}, 1000);
```

然后，使用对`diceRun()`函数的标准调用，将两个骰子投掷十亿次:

```
import { diceRun } from './lib/dice.js';

// throw 2 dice 1 billion times
const
  numberOfDice = 2,
  runs = 999_999_999;

const stat1 = diceRun(runs, numberOfDice);
```

这将停止计时器，因为 Node.js 事件循环在计算完成之前无法继续下一次迭代。

然后，代码在新的 [`Worker`](https://nodejs.org/dist/latest/docs/api/worker_threads.html#new-workerfilename-options) 中尝试相同的计算。这将加载一个名为`worker.js`的脚本，并在 options 对象的`workerData`属性中传递计算参数:

```
import { Worker } from 'worker_threads';

const worker = new Worker('./worker.js', { workerData: { runs, numberOfDice } });
```

事件处理程序被附加到运行`worker.js`脚本的`worker`对象上，因此它可以接收传入的结果:

```
// result returned
worker.on('message', result => {
  console.table(result);
});
```

…并处理错误:

```
// worker error
worker.on('error', e => {
  console.log(e);
});
```

…并在处理完成后进行整理:

```
// worker complete
worker.on('exit', code => {
  // tidy up
});
```

`worker.js`脚本启动`diceRun()`计算，并在计算完成时向父进程发送一条消息——这条消息由上面的`"message"`处理程序接收:

```
// worker threadimport { workerData, parentPort } from 'worker_threads';import { diceRun } from './lib/dice.js';
// worker thread
import { workerData, parentPort } from 'worker_threads';
import { diceRun } from './lib/dice.js';

// start calculation
const stat = diceRun( workerData.runs, workerData.numberOfDice );

// post message to parent script
parentPort.postMessage( stat );
```

当工作线程运行时，计时器不会暂停，因为它在另一个 CPU 线程上执行。换句话说，Node.js 事件循环继续迭代，没有长时间的延迟。

用`node index.js`运行项目代码。

![The worker output](img/d46d786e5d4caaf06d5d7ae6a7a4e5f5.png)

您应该注意到，基于 worker 的计算运行得稍微快一些，因为线程完全专用于该进程。如果在应用程序中遇到性能瓶颈，可以考虑使用 workers。

## 子进程

有时有必要调用不是用 Node.js 编写的或者有失败风险的应用程序。

#### 现实世界的例子

我开发了一个 Express 应用程序，它生成了一个模糊的图像哈希来识别相似的图形。它异步运行，运行良好——直到有人上传了一个包含循环引用的格式错误的 GIF(动画帧 a 引用了帧 b，帧 b 又引用了帧 a)。

哈希计算从未结束。用户放弃，再次尝试上传。再一次。再一次。整个应用程序最终因内存错误而崩溃。

通过在子进程中运行哈希算法修复了该问题。Express 应用程序保持稳定，因为它启动、监控并在计算时间过长时终止计算。

[子流程 API](https://nodejs.org/dist/latest/docs/api/child_process.html) 允许您运行子流程，您可以根据需要监控和终止这些子流程。有三个选项:

*   [`spawn`](https://nodejs.org/dist/latest/docs/api/child_process.html#child_processspawncommand-args-options) :产生一个子进程。
*   [`fork`](https://nodejs.org/dist/latest/docs/api/child_process.html#child_processforkmodulepath-args-options) :启动新 Node.js 进程的一种特殊类型的 spawn。
*   [`exec`](https://nodejs.org/dist/latest/docs/api/child_process.html#child_processexeccommand-options-callback) :生成一个 shell 并运行一个命令。当进程结束时，结果被缓冲并返回给回调函数。

与工作线程不同，子进程独立于主 Node.js 脚本，不能访问同一个内存。

## 簇

当 Node.js 应用程序在单核上运行时，您的 64 核服务器 CPU 是否未得到充分利用？ **[集群](https://nodejs.org/dist/latest/docs/api/cluster.html)** 允许您派生任意数量的相同流程，以便更高效地处理负载。

最初的[主进程](https://nodejs.org/dist/latest/docs/api/cluster.html#clusterisprimary)可以自己分叉——也许对于由 [`os.cpus()`](https://nodejs.org/dist/latest/docs/api/os.html#oscpus) 返回的每个 CPU 分叉一次。它还可以在进程失败时处理重启，并在分叉的进程之间代理通信消息。

集群工作得非常好，但是您的代码可能会变得复杂。更简单、更强大的选项包括:

*   流程管理器，如 [PM2](https://pm2.keymetrics.io/) ，它提供了一个自动化的[集群模式](https://pm2.keymetrics.io/docs/usage/cluster-mode/)
*   集装箱管理系统，如 [Docker](https://www.docker.com/) 或 [Kubernetes](https://kubernetes.io/)

两者都可以启动、监视和重启同一个 Node.js 应用程序的多个独立实例。即使一个应用程序出现故障，它也将保持活动状态。

#### 编写无状态应用程序

值得一提的是:*让你的应用程序无状态，以确保它可以伸缩和更有弹性*。应该可以启动任意数量的实例并分担处理负载。

## 摘要

本文提供了更有用的 Node.js APIs 的示例，但是我鼓励您[浏览文档](https://nodejs.org/dist/latest/docs/api/)并自己发现它们。文档总体来说很好，并且展示了简单的例子，但是在某些地方可能会很简洁。

如前所述，这个指南是基于我的课程 **[Node.js:新手到忍者](https://www.sitepoint.com/premium/books/node-js-novice-to-ninja/read/1/)** ，可以在 SitePoint Premium 上找到。

## 分享这篇文章