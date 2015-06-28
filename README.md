# app-redirect

Handles redirecting to the native app / app store on iOS and Android.

This is an example of how it will work for Twitter app: ([Click here to try](http://rawgit.com/danielgindi/app-redirect/master/redirect.html))

```javascript
    // You have the whole queryString ready for you here in a key->value form.
    // In the edge case where there are multiple values, the value is an array.
    var qs = AppRedirect.queryString;

    // Here we initiate the redirect process
    AppRedirect.redirect({

        iosApp: 'twitter://post?message=' + qs['message'],

        iosAppStore: 'https://itunes.apple.com/il/app/twitter/id333903271?mt=8&message=' + qs['message'],

        // For this, your app need to have category filter: android.intent.category.BROWSABLE
        android: {
            'host': 'post/?message=' + encodeURIComponent(qs['message']), // Host/path/querystring part in a custom scheme URL
            // 'action': '', // Equivalent to ACTION in INTENTs
            // 'category': '', // Equivalent to CATEGORY in INTENTs
            // 'component': '', // Equivalent to COMPONENT in INTENTs
            'scheme': 'twitter', // Scheme part in a custom scheme URL
            'package': 'com.twitter.android', // Package name in Play store
            'fallback': 'https://play.google.com/store/apps/details?id=com.twitter.android&hl=en&message=' + qs['message']
        }

    });
    
```

Of course you want to style the HTML as a nice landing page, and maybe with a logo and styled links as a last-resort fallback.


The flow on iOS:
---

1. Try to open the app itself
2. Try to open the App Store

Edge cases:
  * In case the app is installed, and the user returns to Safari, the App Store url will open.
  * In case the app is not installed, and the user returns to Safari from the App Store, he will see an "alert" error message that will disappear immediately.

The flow on Android:
---

Using a special Intent url, we can ask the system to open the app and fallback to the Play Store.
  * In case you wish to pass arguments to the Play Store, you can use the `fallback` url to specify a Play Store url with arguments.
  * `fallback` url does not have to be specified, as the Android will use `package` to find the Play Store page, but it can be specified as an alternative fallback, especially if `package` is ommited.
  * `package` does not have to be specified, but then you should specify `fallback`.
  * **Note** that in Chrome for Android, if the user writes the url manually, it assumes that he does <b>not</b> wish to be redirected outside of the browser. So you want to be redirected to this link or have clicked a link to this url.
