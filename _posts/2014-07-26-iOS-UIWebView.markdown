---
layout: post
title:  "iOS UIWebView"
date:   2014-07-25
categories: uiwebview 
---

###Delegate
如果是在空白view中添加了一个webView，可以把原来的controller设置为UIWebViewDelegate的委托：在controller.h中加上<UIWebViewDelegate>，在controller.m中加一个webView的outlet，设置self.webView.delegate = self。然后就可以在controller.m中实现以下代理方法：
{% highlight objective-c %}
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
- (void)webViewDidStartLoad:(UIWebView *)webView
- (void)webViewDidFinishLoad:(UIWebView *)webView
- (void)webView:(UIWebView *)webView didFailLoadWithError:(NSError *)error
{% endhighlight %}

由上到下一次表示：**开始请求 、已经开始加载 、 加载完成 、 加载错误**

#### shouldStartLoadWithRequest
此代理方法可以捕获到一切请求，非常有用。
比如在进行oAuth2时，一般进行向server发送两次请求，第一获得authorize **code**，第二次使用前面得到的authorize code的到access **token**。那么就可以在这个代理方法中进行分析操作。
下面以新浪微博为例：
{% highlight objective-c %}
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    //get request URL string
    NSString *urlString = request.URL.absoluteString;
    
    // if WeiBo server returned authorize code
    if ([urlString hasPrefix:@"https://api.weibo.com/oauth2/default.html?code="]) {
        // get authorize code
        self.authorizeCode = [urlString componentsSeparatedByString:@"="][1];
        
        // combine the "POST" request for requiring access token
        NSString *urlString = [NSString stringWithFormat:@"https://api.weibo.com/oauth2/access_token?client_id=%@&client_secret=%@&grant_type=authorization_code&redirect_uri=%@&code=%@", self.clientId, self.clientSecret, self.redirectUri, self.authorizeCode];
        NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:[NSURL URLWithString:urlString]];
        [request setHTTPMethod:@"POST"];
        
        // get access token from server
        NSData *tokenData = [NSURLConnection sendSynchronousRequest:request returningResponse:nil error:nil];
        NSString *tokenInfoString = [[NSString alloc] initWithData:tokenData encoding:NSUTF8StringEncoding];
        NSLog(@"token info: %@", tokenInfoString);
        
        NSDictionary *tokenInfoDict = [NSJSONSerialization JSONObjectWithData:tokenData options:NSJSONReadingMutableContainers error:nil];
        self.accessToken = tokenInfoDict[@"access_token"];
        NSLog(@"access token: %@", self.accessToken);
    }

    return YES;
}
{% endhighlight %}