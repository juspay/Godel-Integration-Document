# Getting started


## Platform & API Level support

Currently only android based applications are supported. Android versions >= API 8 are supported.

## Android Permissions

The following are the minimum set of permissions required for the library to work as expected:

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

#### Additional permissions preferred:

```
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.SEND_SMS"/>
```


In order to support sending SMS for handling CITI Bank, we will need SEND_SMS permission. However, this permission is optional. When this permission is not available, CITI Bank card transactions will not be enhanced by the library.

## Manifest file changes

In the manifest file, for the activity that includes our fragment, please ensure that the following properties are defined as follows. You may add any other additional properties as you desire.
```

<activity
    android:name="com.example.PaymentActivity"
    android:screenOrientation="portrait"
    android:windowSoftInputMode="adjustResize">
</activity>
```

# Getting the SDK

The library can be added as a direct dependency when you use Gradle. If you don’t use Gradle, we provide a downloadable SDK which you can add as a project dependency.

## Gradle

Add the following maven repository to the build.gradle. Please make sure that you add it in the project dependency section and NOT the buildScript section.

```
repositories {
    mavenCentral()
    maven { 
       url "https://s3-ap-southeast-1.amazonaws.com/godel-release/godel/"
    }
}
```


Add the following compile dependency to your project. Here again make sure you don’t accidentally add it to script dependencies. 

```
dependencies {
    compile 'com.android.support:support-v4:+'
    compile 'in.juspay:godel:0.5rc2'
}
```

## Non Gradle (or Eclipse style integration) 

Download the binary from this location:

[https://s3-ap-southeast-1.amazonaws.com/godel-release/stable/godel-0.5rc2.tar.gz](https://s3-ap-southeast-1.amazonaws.com/godel-release/stable/godel-0.5rc2.tar.gz)


This can be added directly as a project dependency in Eclipse or Intellij Idea. Besides adding  the project as a dependency, you will have to add to the assets too. Once the library is extracted into a folder, navigate to ***godel/assets***. Add everything in the folder to your ***src/main/assets*** folder. 

```
cp $GODEL_LIBRARY_PATH/assets/* $PROJECT_HOME/src/main/assets
```

You should add appcompat-v4 from Google SDK as a library project as well.

# How Godel Works


In the normal flow, you would simply include the WebView in the view XML of your activity/fragment and load the URL (or postURL). Godel encapsulates the WebView into a fragment and all our enhancements are driven through this.

To integrate with Godel, you will replace the existing WebView with our Fragment. You could directly include the Fragment as is or use a FrameLayout to inject it later.


## Initializing Bundle Parameters

There are three ways of initializing the Juspay browser. 
Strategy #1 and #2 can be used by anyone (If you are not registered with JusPay simply set orderId as your Payment ID).
Strategy #3 is only applicable to merchants registered with Juspay. 

### Strategy #1: Start BrowserFragment using payment URL

```
Bundle args = new Bundle();
args.putString("merchantId", merchantId);
args.putString("orderId", orderId));
args.putString("url", url);
browserFragment.setArguments(args);
```

Should you need to **POST the data** to a URL, you can pass the HTTP payload in another parameter and we will initialize the session with HTTP POST. Example:

```
Bundle args = new Bundle();
args.putString("merchantId", merchantId);
args.putString("orderId", orderId));
args.putString("url", url);
args.putString("postData", postData);
browserFragment.setArguments(args);
```

### Strategy #2: Start BrowserFragment with ACS information

If you have obtained the **ACS information** yourself, you can set the parameters as shown below. This will have the benefit of cutting down one redirection from the payment flow. Whenever the backend supports this, we recommend that you follow this technique.

```
Bundle args = new Bundle();
args.putString("acs_url", acsUrl);
args.putString("term_url", termUrl);
args.putString("md", md);
args.putString("pareq", pareq);
args.putString("merchantId", juspayMerchantId);
args.putString("orderId", orderId);
browserFragment.setArguments(args);
```

### Strategy #3: Start BrowserFragment using Order and Card information 

This is applicable when you are using **Juspay's Payment Gateway** in the backend. Assuming you have collected the card information from the user, you can launch the fragment as follows:

```
Card card = new Card(cardNumber, cardExpiryMonth, cardExpiryYear);
card.setCVV(cvv);
Bundle args = new Bundle();
args.putString("merchantId", juspayMerchantId);
args.putString("orderId", orderId));
args.putString("card", card);
browserFragment.setArguments(args);
```

If you are using **Juspay to store your cards** you can launch the browser using a stored card token as follows:

```
StoredCard card = new StoredCard(cardToken, cardNumberMask, expiryMonth, expiryYear);
card.setCVV(cvv);
Bundle args = new Bundle();
args.putString("merchantId", juspayMerchantId);
args.putString("orderId", orderId));
args.putString("card", card);
browserFragment.setArguments(args);
```

This will first fetch the ACS url from juspay servers and then load the 3D secure page directly. This is assuming that the PG backend supports this. In other cases, we will follow the redirection mode.  If you would like to do the part on loading the acs url yourself or would like to direct the user to a third party url (Like PayU, Citrus etc) which will inturn direct them to the acs page see Strategy#1 and Strategy#2.

## Parameters

Clients must make sure to add these required metrics while invoking JuspayBrowserFragmet. These metrics are crucial for the transaction to complete and have seamless analytics. Find below the set of parameters which can be passed to us:

|  Variable             | Description                                                                                                                                 | Required |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| merchantId            | Indicates the merchantId                                                                                                                    | Yes      |
| clientId              | ClientID created in Merchant Portal                                                                                                         | Yes      |
| transactionId         | Indicates the transactionId                                                                                                                 | Yes      |
| orderId               | OrderID of the transaction                                                                                                                  | No       |
| customerId            | Unique identifier of the customer                                                                                                           | No       |
| displayNote           | Short note about transaction                                                                                                                | No       |
| remarks               | Remarks about transaction. This will automatically be filled up in the bank page.                                                           | No       |
| amount                | Amount of the transaction                                                                                                                   | No       |
| customerEmail         | Customer's email address                                                                                                                    | No       |
| customerPhoneNumber   | Customer's phone number                                                                                                                     | No       |
| progressDialogEnabled | If passed as false, juspay’s custom progress dialogs would not be shown while a page is loaded. Pass true to let juspay handle the dialogs. | No       |
| showJuspayAutoHelp    | If true, we show a help screen automatically on the first use of the library. Set argument to false if you dont want to show the help       | No       |
| card_brand            | This argument contains enums like MasterCard, Visa, Maestro, etc                                                                            | No       |
| card_type             | Indicates type of card used i.e. debit or credit                                                                                            | No       |

## Custom Parameters

|  Variable  | Description                  | Required |
| ---------- | ---------------------------- | -------- |
| udf_:var1  | Custom Field                 | No       |
| udf_:var2  | Custom Field                 | No       |
| udf_:var3  | Custom Field                 | No       |
| udf_:var4  | Custom Field                 | No       |
| udf_:var5  | Custom Field                 | No       |

```
Bundle args = new Bundle();
args.putString("url", "https://www.merchant-website.com"); 
args.putString("merchantId", your_merchant_id); // eg: "juspay_recharge"
args.putString("transactionId", unique_transaction_id); // eg "123aba321"
args.putString("clientId", your_client_id); // eg: "juspay_recharge_android"
args.putString("customerId", unique_customer_id); // eg: "cust_1221232"
args.putString("displayNote", "Custom note to display during transaction"); // eg: "Recharging 9829880095 with Rs. 10"
args.putString("remarks", "Custom remark to auto-fill on bank page"); // eg: "Transaction at Juspay Recharge"
args.putString("amount", transaction_amount); // eg: "10"
args.putString("customerEmail", customer_email); // eg: "support@juspay.in"
args.putString("customerPhoneNumber", customer_phone_number); // eg: "9829880095"
args.putSerializable("card_brand", CardBrand.VISA);
args.putSerializable("card_type", CardType.CREDIT_CARD);

// Custom parameters. Here we send 3 custom key-values related to a recharge transaction.
args.putString("udf_operator", recharge_operator);
args.putString("udf_circle", recharge_circle);
args.putString("udf_type", recharge_type);

browserFragment.setArguments(args);
```

# Adding JuspayBrowserFragment to View

As stated above you need to simply replace your current webview with the JuspayBrowserFragment and load the URL (or postURL). You could directly include the Fragment as is or use a FrameLayout to inject it later. Both the integration methods are included below.

## Embedding in layout.xml file

Once this code is added to the view file, you can access this in the onCreate method of your Activity. The Fragment can be initialized in different ways. Each of these ways is explained in the upcoming sections.

```
<fragment android:name="in.juspay.godel.ui.JuspayBrowserFragment"
    android:id="@+id/juspayBrowserFragment"
    android:layout_width="fill_parent"
    android:layout_height="match_parent" />
```

When embedding the Fragment directly in view file, you can pass the bundle by using the method ***JuspayBrowserFragment#startPaymentWithArguments***.

```
JuspayBrowserFragment browserFragment = (JuspayBrowserFragment) getSupportFragmentManager().findFragmentById(R.id.juspay_browser_fragment);
browserFragment.startPaymentWithArguments(initBundle);
```

Here ***initBundle*** contains the key-value pairs as stated in above sections.

## Using Fragment Manager

When using this method, the view XML is expected to contain a FrameLayout which acts as the container to the Fragment. 

```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:ignore="MergeRootFrame" />
```

Create an instance of JuspayBrowserFragment and initialize it with one of the three strategies outlined above. Example:

```
JuspayBrowserFragment juspayBrowserFragment = new JuspayBrowserFragment();
juspayBrowserFragment.setArguments(initBundle);
```

To activate the Fragment inside this Layout, add the Fragment to the View using the FragmentSupportManager as shown below. The transition is optional. It simply adds to the effect. You can modify or remove it, if needed.

```
FragmentTransaction transaction = this.getSupportFragmentManager().beginTransaction();
transaction.replace(R.id.container, juspayBrowserFragment);
transaction.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_FADE);
transaction.commit();
```

# Accessing JuspayWebView from JuspayBrowserFragment

If you want to gain access to JuspayWebView, you can use the callback interface which is called as soon as webview is ready. To set it up you can follow this code snippet below:

```
JuspayWebviewCallback juspayWebviewCallback = new JuspayWebviewCallback() {
        @Override
        public void webviewReady() {
            JuspayWebView webView = browserFragment.getWebView();
            webView.setWebViewClient(new CustomWebViewClient());
        }
};

browserFragment.setupJuspayWebviewCallbackInterface(juspayWebviewCallback);
```
If you just want to use custom webview and webchrome client and don't need webview instance specifically, you can override the two methods below and return the appropriate client instances:

```
@Override
protected JuspayWebChromeClient newWebChromeClient() {
    return getCustomWebChromeClient(this);
}

@Override
protected JuspayWebViewClient newWebViewClient(JuspayWebView juspayWebView) {
    return getCustomWebViewClient(juspayWebView, this);
}
```

# Custom Web Clients

Since the WebView is now encapsulated by the JuspayBrowserFragment class, it gets little tricky to have control over the WebView. The WebView is manipulated by our library using the WebViewClient and WebChromeClient. To achieve this we have extended these two classes to include our logic. 

In order to be able to have your logic in place (for methods like onPageStarted and onPageFinished), you will have to extend JuspayWebViewClient and JuspayWebChromeClient. However, please make sure to pass the call up (by calling on super.) on all occasions. 

```
public class CustomWebViewClient extends JuspayWebViewClient {
    @Override
    public void onPageFinished(WebView view, String url) {
        super.onPageFinished(view, url);
    }    
}

class MyWebChromeClient extends JuspayWebChromeClient {
  // overrides
}
```

# Track payment status

As soon as the payment is over, send us the notification on the status of the payment. These responses are crucial in order to track transaction success/failure metrics. For instance:

```
GodelTracker.getInstance().trackPaymentStatus(:transactionId,
        GodelTracker.PaymentStatus.SUCCESS);
```


Payment status can be one of: ***SUCCESS***, ***FAILURE*** or ***CANCELLED***.

# Tracking back button press

We also provide unblocking helps when the user presses the back button. This is intelligently controlled by assessing the current state of the transaction. You can call ***juspayBackPressedHandler*** in the onBackPressed method of your Activity. If you don't want Juspay to control the backbutton you can pass false as the argument in juspayBackPressedHandler, else pass true.

@Override
public void onBackPressed() {
    browserFragment.juspayBackPressedHandler(shouldShowJuspayCloseDialog);
    // Your code to show a dialog box if you are passing false.
}

If Juspay is unable to provide help and the user closes the transaction session using the "Cancel Transaction" dialog box, we provide a callback to the activity, ***JuspayBackButtonCallback#transactionCancelled***. Please note, this callback is not called if the user clicks any Abort/Cancel button in the webpage, this action is handled by the webpage itself.

```
JuspayBackButtonCallback backButtonCallback = new JuspayBackButtonCallback() {

        @Override
        public void transactionCancelled() {
            finish();
        }
    };
    
browserFragment.setupJuspayBackButtonCallbackInterface(backButtonCallback);
```
# Was Godel used in the last transaction

You can track if, for the last transaction, godel was used. You can callthis API anytime during the transaction. This would return *true* only if godel was enabled through the transaction. 

```
SessionInfo.getInstance().wasGodelEnabled()
```

# Proguard Rules

In case your application uses proguard, please do specify the following rule to avoid conflicts:

```
-keep class in.juspay.** {;}
```

# Merchant Checklist

Please ensure that the following steps are accomplished to have a cleaner integration.
1. The AndroidManifest.xml entry for the activity should say android:windowSoftInputMode="adjustResize".
2. The activity holding the Juspay Fragment should fix the orientation as portrait.
3. Ensure that correct value is sent for merchantId, transactionId and clientId
4. At the end of the flow, payment status is being tracked using the call GodelTracker.trackPaymentStatus
5. All the classes under in.juspay.godel namespace should be kept out of ProGuard optimization.