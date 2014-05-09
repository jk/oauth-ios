# OAuth.io iOS SDK [![License Apache 2.0](https://go-shields.herokuapp.com/license-Apache-blue.png)](https://tldrlegal.com/license/apache-license-2.0-(apache-2.0))
[![Build Status](https://travis-ci.org/jk/oauth-ios.svg?branch=feature/travis)](https://travis-ci.org/jk/oauth-ios)
[![Badge w/ Version](https://cocoapod-badges.herokuapp.com/v/OAuth.io/badge.svg)](http://cocoadocs.org/docsets/OAuth.io/)
[![Badge w/ Platform](https://cocoapod-badges.herokuapp.com/p/OAuth.io/badge.svg)](http://cocoadocs.org/docsets/OAuth.io/)

This is the official iOS SDK for [OAuth.io](https://oauth.io) !

 * Supported on iOS 5 or later

### License

See LICENSE file

### OAuth.io Requirements and Set-Up

To use this plugin you will need to make sure you've registered your OAuth.io app and have a public key (https://oauth.io/docs)

### Installation

#### CocoaPods
That's the prefered way to install OAuth.io within your iOS app. Just add the following to your `Podfile`:

```ruby
pod 'OAuth.io', '~>1.0'
```

An example Podfile could be look like this:

```ruby
platform :ios, "7.1"

pod 'OAuth.io', '~>1.0'
```

#### Manual
Copy the source files within oauth-ios/Src to your project.

### Usage

To get the response from OAuthIO service, you must define a custom scheme and identifier in your plist file. (e.g myappname://localhost)

![custom_scheme](https://oauth.io/img/custom_scheme.png)

Implement the method bellow in your AppDelegate File 

```objc
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
   [OAuthIOModal handleOAuthIOResponse:url];
   return (YES);
}
```

Put #import "OAuthIOModal.h" in your source file and don't forget to implement the OAuthIO protocol then in your ViewController instanciate the OAuthIOModal object

`ViewController.h`:
```objc
#import <UIKit/UIKit.h>
#import "OAuthIOModal.h"

@interface ViewController : UIViewController<OAuthIODelegate>
```

`ViewController.m`:
```objc
// …

OAuthIOModal oauthioModal = [[OAuthIOModal alloc] initWithKey:@"Public key" delegate:self];
[oauthioModal showWithProvider:@"github"];

// Or use this if a state parameter needs to be passed through:
//[oauthioModal showWithProvider:@"github" options:@{@"state": @"STATE_VALUE"}];

// …
```

Implement these delegate methods in your ViewController

```objc
#pragma mark OAuthIO delegate methods
- (void)didReceiveOAuthIOResponse:(OAuthIORequest *)request
{
   NSDictionary *params = @{@"name": @"New repo"};
        
  [request setContentType:@"json"]; // Github specification - This line convert params to JSON 

  [request post:@"/user/repos" withParams:params success:^(NSString *output, NSHTTPURLResponse *httpResponse)           
  { 
     NSLog(@"output:%@, status code:%i", output, httpResponse.statusCode);
  }];

}

- (void)didFailWithOAuthIOError:(NSError *)error
{
  NSLog(@"Error : %@", error.description);
}
```
    
### Available methods for OAuthIORequest object

```objc
- (void)addHeaderWithKey:(NSString *)key andValue:(NSString *)value;

- (void)get:(NSString *)resource withParams:(id)params success:(RequestSuccessBlock)success;

- (void)post:(NSString *)resource withParams:(id)params success:(RequestSuccessBlock)success;

- (void)put:(NSString *)resource withParams:(id)params success:(RequestSuccessBlock)success;

- (void)patch:(NSString *)resource withParams:(id)params success:(RequestSuccessBlock)success;

- (void)delete:(NSString *)resource success:(RequestSuccessBlock)success;
```
