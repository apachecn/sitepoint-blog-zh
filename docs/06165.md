# iPhone 应用程序开发–网络

> 原文：<https://www.sitepoint.com/ios-application-development-networking/>

在学习 iOS 应用程序开发的过程中，通过网络获取数据是另一个需要熟悉的重要概念。

有两个基本的 API 可以帮助我们通过网络同步或异步地获取数据:NSURLConnection 和 NSURLSession。

后者是作为 iOS7 SDK 的一部分引入的，具有附加功能。有许多库是建立在这些 API 之上的。

在本教程中，我们将专注于这两个 API。

## NSURLConnection

“NSURLRequest”类帮助创建对指定 URL 的请求。它指定了发出 URL 请求时应该采用的目标 URL 和缓存策略。还可以指定 HTTP 请求类型、其头字段和发布数据。因此，我们应该用所需的请求信息创建 NSURLRequest 的实例。

应传递此请求对象以初始化“NSURLConnection”对象并激发请求。

还应该提到连接委托。

### 异步连接

#### 获取请求示例

```
NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://samplewebservice.com?x=1&y=2"]];
    NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self];
```

#### 带有 HTTP 头的 Post 请求示例

```
NSMutableURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://samplewebservice.com"]];
    request.HTTPMethod = @"POST";
    [request setValue:@"text/plain" forHTTPHeaderField:@"Content-Type"];
    NSString *str = @"Post data";
    NSData *postData = [str dataUsingEncoding:NSUTF8StringEncoding];
    request.HTTPBody = postData;
    NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self];
```

#### 带有缓存策略的示例请求

```
NSMutableURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://samplewebservice.com"]
                                                    cachePolicy: NSURLRequestReloadIgnoringCacheData
                                                timeoutInterval:20.0];
    NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self];
```

在请求被触发后，我们应该实现 NSURLConnection 委托方法来接收响应数据。

```
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response {

  //Declare a NSMutableData variable 'responseData' to hold the response from the server.
  //Initialize or reset the 'responseData' variable here.
    _responseData = [[NSMutableData alloc] init];
}

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data {
    // Append the data that we receive to the 'responseData' variable
    [self.responseData appendData:data];
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection {
  //The response data is fully received.
  //Parsing can be done here
        NSString *strResponse = [[NSString alloc] initWithData:self.responseData encoding:NSUTF8StringEncoding];
    NSLog(@"Response %@",strResponse);
}

- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error {
    // The request has failed due to some error
    NSLog(@"Error %@",[error description]);

}
```

有一个简单的 API，可以用来触发异步请求。

```
[NSURLConnection sendAsynchronousRequest:request
                                       queue:[NSOperationQueue mainQueue]
                           completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                           //Parse the data here

                           }];
```

这就是我们应该如何触发和处理异步 URL 连接。

### 同步连接:

“NSURLConnection”类的“sendSynchronousRequest”方法用于对服务器进行同步调用。

```
NSURLRequest * request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://samplewebservice.com"]];
    NSURLResponse * response = nil;
    NSError * error = nil;
    NSData * responsedata = [NSURLConnection sendSynchronousRequest:request
                                          returningResponse:&response
                                                      error:&error];

    if (error == nil)
    {
        // Parse response data here
    }
```

响应和错误变量作为参数传递给方法。如果成功建立了连接并且收到了响应，那么将使用原始响应填充响应变量。如果无法收到响应数据，将使用错误详细信息填充错误变量。

在进行数据解析之前，我们应该检查错误值是否为零。

## NSURLSession

使用 NSURLSession，我们可以为每个会话配置缓存、cookie 和凭证策略，而不是在整个应用程序中共享它们。NSURLSessionConfiguration 对象有助于指定这些策略，NSURLSession 对象是用这些配置对象实例化的。

NSURLSessionConfiguration API 提供了三种配置设置。

**默认会话:**使用全局或共享 cookie、缓存和凭证存储对象的配置。行为类似于 NSURLConnection。
**短暂会话:**不持久存储 cookie、高速缓存和凭证存储对象的私有会话配置。顾名思义，配置设置是短暂的，当会话失效时会被删除。
**后台会话:**类似于默认会话，但即使在应用程序处于暂停状态时也可以上传或下载数据。

会话中实际的上传或下载功能是在 NSURLSessionTask 对象的帮助下完成的。

有三种类型的会话任务

**数据任务:**数据以 NSData 对象的形式发送和接收
**下载任务:**数据以文件的形式接收
**上传任务:**数据通常以文件的形式发送。

可以使用完成处理程序或委托来创建任务。

创建会话时，请遵循以下步骤

1.  创建 NSURLSessionConfiguration 对象。(默认/短暂/背景)
2.  借助创建的会话配置对象，设置缓存、协议、cookie 或凭据策略以及其他配置设置
3.  创建 NSURLSession 对象
4.  创建 NSURLSessionTask 对象(数据/下载/上传)
5.  实现完成处理程序或委托方法

### 带有完成处理程序的示例数据任务

阅读行内注释，并尝试理解下面给出的示例代码

```
// Create a default session configuration object
    NSURLSessionConfiguration *sessionConfig = [NSURLSessionConfiguration defaultSessionConfiguration];
    //Set policies and other session configuration parameters if required
    [sessionConfig setRequestCachePolicy:NSURLRequestReloadIgnoringCacheData];

    //Create a session with the configuration object
    //Set delegate to nil if you are going to use completion handler
    //Set the queue for the completion handlers
    NSURLSession *session = [NSURLSession sessionWithConfiguration:sessionConfig delegate:nil delegateQueue:[NSOperationQueue mainQueue]];

    //Create a NSURLRequest object with the webservice URL
    NSURLRequest * request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://samplewebservice.com"]];
    //Create a data task object with the session and the request object
    // Implement the completion handler that should be triggered at the end of the request
    NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
        if(error == nil)
        {
            NSString *strResponse = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
            NSLog(@"Response %@",strResponse);
        }

    }];

    //All the session tasks will be in the suspended state. //Call resume method on the tasks to trigger the request
    [dataTask resume];
```

### 带委托的示例数据任务

#### 请求启动

```
NSURLSessionConfiguration *sessionConfig = [NSURLSessionConfiguration defaultSessionConfiguration];
    [sessionConfig setRequestCachePolicy:NSURLRequestReloadIgnoringCacheData];

    //Specify the custom delegate when creating the session
    NSURLSession *session = [NSURLSession sessionWithConfiguration:sessionConfig delegate:self delegateQueue:[NSOperationQueue mainQueue]];
    NSURL *url = [NSURL URLWithString: @"http://www.example.com/"];
    NSURLSessionDataTask *dataTask = [session dataTaskWithURL: url];
    [dataTask resume];
```

#### 数据任务委托方法

```
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data
{
    //handle data here
}
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
    //Called when the data transfer is complete
    //Client side errors are indicated with the error parameter
}
```

下载任务也有特殊的代表

1.委托以指示下载已完成。下载的文件应复制到新位置，因为它将从临时位置删除。

```
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask
                              didFinishDownloadingToURL:(NSURL *)location;
```

2.委托来指示下载进度。可以基于下载的数据量来更新进度指示器。

```
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask
                                           didWriteData:(int64_t)bytesWritten
                                      totalBytesWritten:(int64_t)totalBytesWritten
                              totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite;
```

3.下载恢复时调用委托

```
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask
                                      didResumeAtOffset:(int64_t)fileOffset
                                     expectedTotalBytes:(int64_t)expectedTotalBytes;
```

上传任务没有特殊的委托人，但是可以使用下面的委托方法来检查上传进度

```
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task
                                didSendBodyData:(int64_t)bytesSent
                                 totalBytesSent:(int64_t)totalBytesSent
                       totalBytesExpectedToSend:(int64_t)totalBytesExpectedToSend;
```

## 结论

您可以自己探索更多的代理和网络 API。希望这篇文章对网络概念有所了解。

## 分享这篇文章