#AFNetworking 源码学习

核心目标：AFNetworking 框架是如何实现的，如何在日常中实现 HTTP 请求、构建网络层

1. README，Quick start
2. Architecture
	- NSURLSession
		- AFURLSessionManager
		- AFHTTPSessionManager
	- Serialization
		- AFURLRequestSerialization
			- AFHTTPRequestSerializer
			- AFJSONRequestSerializer
			- AFPropertyListRequestSerializer
		- AFURLResponseSerialization
			- AFHTTPResponseSerializer
			- AFJSONResponseSerializer
			- AFXMLParserResponseSerializer
			- AFXMLDocumentResponseSerializer (Mac OS X)
			- AFPropertyListResponseSerializer
			- AFImageResponseSerializer
			- AFCompoundResponseSerializer
	- Additional Functionality
	  - Security
	  - Reachability
	  - UIKit

3.方法调用栈

每次发 POST 请求时，都会创建新的 dataTask 和 request 、taskDelegate（跟 task 相关） 

- AFHTTPSessionManager.m
 - POST: parameters: success: failure:
   - POST: parameters: progress: success: failure:
     - dataTaskWithHTTPMethod: URLString: parameters: uploadProgress: downloadProgress: success: failure: （通过 AFHTTPRequestSerializer 创建一个 request，返回一个 dataTask,这个方法中会回调外面传进来的成功、失败 block）
         - dataTaskWithRequest: uploadProgress: downloadProgress: completionHandler:（这个方法中会调用 NSURLSession 的 dataTaskWithRequest: 方法创建一个 dataTask）
             - addDelegateForDataTask: uploadProgress: downloadProgress: completionHandler:（这个方法中创建了一个 AFURLSessionManagerTaskDelegate 对象，所有的回调都赋给 delegate ，）
             - setDelegate: forTask:（根据 taskID 来保存 delegate，铜鼓 delegate 设置进度，同时监听 task 的 resume 和 suspend）


网络回调 block

- URLSession: task: didCompleteWithError:(AFURLSessionManagerTaskDelegate)
  - dataTaskWithHTTPMethod: URLString: parameters: uploadProgress: downloadProgress: success: failure: (AFHTTPSessionManager)
     - 最外层的网络库回调

网络请求回来时移除 delegate 

- URLSession: task: didCompleteWithError: （AFHTTPSessionManager）
   
   
问题：
1.AFNetworking 的作用是什么？不用 AFNetworking 直接用系统的不也可以吗？
2.AFNetworking 框架的设计思路和原理是什么？
3.AFNetworking 和 MKNetworkKit 以及 ASINetwork 有什么不同?
4.Draveness 和 南峰子老驴是怎么读源码的？文章是怎么写的？
   
知识点：  
 1. HTTP 请求中的 content-type   

 - [Wikipedia - Media type](https://en.wikipedia.org/wiki/Media_type) 
 - [HTTP content-type 参照表](http://www.runoob.com/http/http-content-type.html)  
 - [HTTP协议探索之Content-Type(MIME)](http://www.tuicool.com/articles/vYnuM3)

2.[What is http multipart request?](http://stackoverflow.com/questions/16958448/what-is-http-multipart-request)

3.NSProgress

4.[URL Session Programming Guide](https://developer.apple.com/library/prerelease/content/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html#//apple_ref/doc/uid/10000165i)