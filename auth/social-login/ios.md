---
title: Social Login
description: User login with 3rd party authentication in iOS. Facebook, Google+, Twitter and more. 
---

Skygear allows you to authenticate users using their 3rd party accounts. Currently following providers are supported.

- Google
- Facebook
- LinkedIn
- Instagram

## Setup

For different 3rd party party, please find the setup instruction below

## Login with 3rd party account in iOS

Skygear SDK supports two kind of login flow. Supported login flow: 

- [Login with web flow][login-with-web-flow]
- [login with access token][login-with-access-token]

### Login with web flow

1. Configure Callback URLs

The callback url is URL of your application that skygear redirects after user authenticated. You can configure the app scheme by adding following lines into `Info.plist`, replacing `${YOUR_APP_SCHEME}` with your url scheme.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLName</key>
        <string>skygear</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>${YOUR_APP_SCHEME}</string>
        </array>
    </dict>
</array>
```

Skygear SDK will redirect back to your application after user authenticated through following link. For production app, please make sure your skygear portal *Allow redirect URLs* contains the below link. All URLS will be allowed, if *Allow redirect URLs* are empty.

```
${YOUR_APP_SCHEME}://skygear.io/auth_handler
```

SCREENSHOT!!!

2. Trigger login with web flow

By calling `loginOAuthProvider`, Skygear SDK will complete the login flow by using browser to sign up and login user.

```obj-c
NSString *providerID = @"google"; // the provider name in lowercase

// scheme - you app scheme
// scope - optional, overwrite the portal default permissions if it is provided
NSDictionary *options = @{
    @"scheme": @"${YOUR_APP_SCHEME}",
    @"scope": @[@"email"]
};

SKYContainer *container = [SKYContainer defaultContainer];
[container.auth loginOAuthProvider:providerID
                           options:options
                 completionHandler:^(SKYRecord *user, NSError *error) {
                    if (error) {
                        NSLog(@"error login with oauth provider: %@", error);
                        return;
                    }

                    NSLog(@"sign up successful");
                    NSLog(@"user record: %@", user);
                }];
```

```swift
let providerID = "google"; // the provider name in lowercase

// scheme - you app scheme
// scope - optional, will overwrite portal default permissions if it is provided
let options = [
    "scheme": "${YOUR_APP_SCHEME}",
    "scope": ["email"]
];

SKYContainer.default().auth.loginOAuthProvider(providerID, options: options) {(user, error) in
    if error != nil {
        print("error login with provider: \(error)")
        return
    }

    print("Login user %@", user.debugDescription)
}
```

### Login with access token

If you obtain the access token by 3rd party provider SDK, you can call login with access token to sign up and login user.

```obj-c
NSString *providerID = @"google"; // the provider name in lowercase
NSString *accessToken = @"access token from 3rd party sdk";

SKYContainer *container = [SKYContainer defaultContainer];
[container.auth loginOAuthProvider:providerID
                       accessToken:accessToken
                 completionHandler:^(SKYRecord *user, NSError *error) {
                    if (error) {
                        NSLog(@"error login with provider: %@", error);
                        return;
                    }

                    NSLog(@"sign up successful");
                    NSLog(@"user record: %@", user);
                }];
```

```swift
let providerID = "google"; // the provider name in lowercase
let accessToken = "access token from 3rd party sdk"

SKYContainer.default().auth.loginOAuthProvider(providerID, accessToken: accessToken) {(user, error) in
    if error != nil {
        print("error login with provider: \(error)")
        return
    }

    print("Login user %@", user.debugDescription)
}
```

## Link existing user with web login flow

Follow steps in [Login with web flow][login-with-web-flow], calling `linkOAuthProvider` instead of `loginOAuthProvider` after user login.

```obj-c
NSString *providerID = @"google"; // the provider name in lowercase

// scheme - you app scheme
// scope - optional, will overwrite portal default permissions if it is provided
NSDictionary *options = @{
    @"scheme": @"${YOUR_APP_SCHEME}",
    @"scope": @[@"email"]
};

SKYContainer *container = [SKYContainer defaultContainer];
[container.auth linkOAuthProvider:providerID
                          options:options
                completionHandler:^(SKYRecord *user, NSError *error) {
                    if (error) {
                        NSLog(@"error link with provider: %@", error);
                        return;
                    }

                    NSLog(@"sign up successful");
                    NSLog(@"user record: %@", user);
                }];
```

```swift
let providerID = "google"; // the provider name in lowercase

// scheme - you app scheme
// scope - optional, will overwrite portal default permissions if it is provided
let options = [
    "scheme": "${YOUR_APP_SCHEME}",
    "scope": ["email"]
];

SKYContainer.default().auth.linkOAuthProvider(providerID, options: options) {(error) in
    if error != nil {
        print("error link with provider: \(error)")
        return
    }

    print("link user with google account successfully")
}
```

## Link existing user with access token

After obtain the access token by 3rd party provider SDK, you can link the current user to provider as follows:

```obj-c
NSString *providerID = @"google"; // the provider name in lowercase
NSString *accessToken = @"access token from 3rd party sdk";

SKYContainer *container = [SKYContainer defaultContainer];
[container.auth linkOAuthProvider:providerID
                       accessToken:accessToken
                 completionHandler:^(NSError *error) {
                    if (error) {
                        NSLog(@"error link with provider: %@", error);
                        return;
                    }

                    NSLog(@"link user with google account successfully");
                }];
```

```swift
let providerID = "google"; // the provider name in lowercase
let accessToken = "access token from 3rd party sdk"

SKYContainer.default().auth.linkOAuthProvider(providerID, accessToken: accessToken) {(error) in
    if error != nil {
        print("error link with provider: \(error)")
        return
    }

    print("link user with google account successfully")
}
```

### Unlink existing user with web login flow

To unlink the current user with a provider.

```obj-c
NSString *providerID = @"google"; // the provider name in lowercase
NSString *accessToken = @"access token from 3rd party sdk";

SKYContainer *container = [SKYContainer defaultContainer];
[container.auth linkOAuthProvider:providerID
                 completionHandler:^(NSError *error) {
                    if (error) {
                        NSLog(@"error unlink with provider: %@", error);
                        return;
                    }

                    NSLog(@"unlink user google account successfully");
                }];
```

```swift
let providerID = "google"; // the provider name in lowercase

SKYContainer.default().auth.unlinkOAuthProvider(providerID) {(error) in
    if error != nil {
        print("error unlink with provider: \(error)")
        return
    }

    print("unlink user google account successfully")
}
```


### To get the 3rd party provider user profile

`getOAuthProviderProfilesWithCompletionHandler` api will return a dictionary of user connected providers profile. Key is the provider ID and value is the profile dictionary. Every time when user login, Skygear will request and update the 3rd party user profile. This is useful to identify if user links with provider and obtain user information. Key will only exists if user connected the given provider.

```obj-c
SKYContainer *container = [SKYContainer defaultContainer];
[container.auth getOAuthProviderProfilesWithCompletionHandler:^(NSDictionary *result, NSError *error) {
    if (error) {
        NSLog(@"error to get provider profile: %@", error);
        return;
    }

    NSString *providerID = @"google"; // the provider name in lowercase
    if let profile = result?[providerID] {
        print("Current user linked with google")
        print("Google profile \(profile)")
    } else {
        print("Google profile \(profile)")
    }
}];
```


```swift
SKYContainer.default().auth.getOAuthProviderProfiles {(result, error) in
    if error != nil {
        print("error to get provider profile: \(error)")
        return
    }

    if let profile = result?["google"] {
                print("Google profile \(profile)")
                return
            }
}
```

[login-with-web-flow]: #login-with-web-flow
[login-with-access-token]: #login-with-access-token
