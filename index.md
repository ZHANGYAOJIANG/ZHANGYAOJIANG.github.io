## WKWebView使用问题

WKWebView如果出现点击链接无法跳转加载问题一般可能两种情况：

- 加载链接需要新跳转网页(target="_blank")导致.
解决方法，实现Delegate方法(二选一)
```
-(WKWebView *)webView:(WKWebView *)webView createWebViewWithConfiguration:(WKWebViewConfiguration *)configuration forNavigationAction:(WKNavigationAction *)navigationAction windowFeatures:(WKWindowFeatures *)windowFeatures
{
    if (!navigationAction.targetFrame.isMainFrame) {
        [webView loadRequest:navigationAction.request];
    }
    if (navigationAction.targetFrame == nil) {
        [webView loadRequest:navigationAction.request];
    }
    return nil;
}
-(void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler
{
    //如果是跳转一个新页面
    if (navigationAction.targetFrame == nil) {
        [webView loadRequest:navigationAction.request];
    }

    decisionHandler(WKNavigationActionPolicyAllow);
}

```
- HTML内部js没有被调用导致.
WKWebView与js调用其实很简单,但是默认是没有开启，需要我们代码来配置
```
    WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
    config.preferences.javaScriptEnabled = YES;
    config.preferences.javaScriptCanOpenWindowsAutomatically = YES;
```
同时上面的delegate还是需要实现的，记得初始化的时候都实现防止出现类似问题出现



