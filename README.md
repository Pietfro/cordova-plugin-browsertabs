# cordova-plugin-browsertabs
  Cordova plugin to open URLs in browser tabs with access to cookies (iOS, Android)

# Platforms

* iOS ([SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller), [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) since iOS 11)
* Android ([Chrome Custom Tabs](https://developer.chrome.com/multidevice/android/customtabs) support since Chrome 45) 

## Usage

To open a URL in an in-app browser tab:

    cordova.plugins.browsertabs.openUrl(url, {
        authSession: Boolean // >= iOS11 [iOS only]. Pass true if you need cookies, storage, etc(uses SFAuthenticationSession). Otherwise open SFSafariViewController.
        scheme: String // provide scheme if you want that plugin catch handleOpenUrl and send result in success callback 
        onOpen: Function // if present, this function is called when the browsertab is successfully opened
    }, onSuccess, onError);

You can also open a URL in the system browser:

    cordova.plugins.browsertabs.openUrlInBrowser(url, onSuccess, onError);

In this case, your WebView will not continue to run Javascript until you switch back to the app.

Your Cordova WebView continues to run Javascript, so you can close the tab later with:

    cordova.plugins.browsertabs.close();

To make this method workable on Android we will use trick with LaunchMode "singleTask".

The user could close the browsertab manually, or you can accomplish it in the JS by navigating to `closeUrl = "<scheme of app>://#put_extra_information_here"` and this URL will be sent as the first parameter in `onSuccess` as a `String`.


## oAuth

Typically you would use this plugin for authentication flows, to grab oAuth tokens. In this case, you will have to supply the `scheme` option to be used in iOS 11:

    cordova.plugins.browsertabs.openUrl(url, {scheme: "myapp://", authSession:true}, onSuccess, onError);

On iOS 11 the user will have an additional dialog before the flow proceeds, telling the user your app wants to authenticate with a certain website.

Note that the oAuth flows such as [facebook login](https://developers.facebook.com/docs/facebook-login/manually-build-a-login-flow/) will only agree to redirect you to a web url that begins with `http://` or `https://`. That means a successful login will still leave the user looking at the browser tab. Thus, you should redirect them to your website, which should then use `location.href = "myapp://something?parameters=here";` to send a message back to your Cordova app. Alternatively, you can set up [Universal Links](https://developer.apple.com/library/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html) on iOS and [App Links](https://developer.android.com/training/app-links/index.html) on Android to make this step more seamless (and possibly more future-proof if custom URL schemes ever go away).

In any case, you will need a plugin such as [this one](https://github.com/EddyVerbruggen/Custom-URL-scheme) to run Javascript on `handleOpenURL`. This returns control to your WebView and you can now call `close()` on the previously opened browser tab.
