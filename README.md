# DeepWall Android SDK Usage

## Implementation

First of all, minSdkVersion must be API 21 for DeepWall implementation

After that you must add this maven repo to project’s build.gradle

```gradle
allprojects {
   repositories {
       google()
       jcenter()
       maven { url 'https://raw.githubusercontent.com/Teknasyon-Teknoloji/deepwall-android-sdk/master/' }
  }
} 
``` 

You must add this implementation to app’s build.gradle

implementation 'deepwall:deepwall-core:2.2.0'

# Initialize
The DeepWall library is started within the onCreate method of your application's launch activity class.

```kotlin
DeepWall.initDeepWallWith(
   application = application,
   activity = activity,
   apiKey = "DEEPWALL_API_KEY",
   environment = DeepWallEnvironment.SANDBOX,
   )
   ``` 



| Parameters | |
| ----- | ----- |
| application | Instance of application class |
| activity | Starting activity instancce |
| apiKey | DeepWall Api Key |
| environment | DeepWall environment. You should use SANDBOX for test, PRODUCTION for prod |




# Configuration

DeepWall needs to know the following user properties for targeting.

| Parameters | |
| ----- | ----- |
| deviceId: String | Unique identifier for specifying user profile or device. |
| Country : String | Two-letter country code; Usage: DeepWallCountry.TURKEY // TURKEY|
| Language: String | Two-letter language code; Usage: DeepWallLanguage.TURKISH // TURKISH  |
| Environment Style: DeepWallEnvironmentStyle | Environment style for Paywall page appearance. Values: AUTOMATIC: Uses system appearance (Default) , LIGHT: Light appearance, DARK: Dark appearance|





# Important:
You must call setUserProperties method before requesting any Paywall page.

```kotlin
DeepWall.setUserProperties( 
deviceId = androidId, 
languageCode = DeepWallLanguage.getLanguageCodeByName(DeepWallLanguage.ENGLISH_UNITED_STATES), 
countryCode = DeepWallCountry.getCountryCodeByName(DeepWallCountry.UNITED_STATES_OF_AMERICA),
environmentStyle = DeepWallEnvironmentStyle.DARK )
 ``` 
 
# Updating User Properties

You could update the following user properties with DeepWall.updateUserProperties method.
| Parameters | |
| ----- | ----- |
| Country : String | Two-letter country code; Usage: "TR" // TURKEY|
| Language: String | Two-letter language code; Usage: "tr-tr" // TURKISH  |
| Environment Style: DeepWallEnvironmentStyle | Environment style for paywall page appearance. Values: AUTOMATIC: Uses system appearance (Default) , LIGHT: Light appearance, DARK: Dark appearance|

 ```kotlin
DeepWall.updateUserProperties(
languageCode = "tr-tr", 
countryCode = "TR"
environmentStyle = DeepWallEnvironmentStyle.DARK  
)
 ``` 

# Requesting Paywall Page

 ``` kotlin
DeepWall.showPaywall(
   activity = this,
   action = “ACTION_KEY”
)
 ``` 

| Parameters | |
| ----- | ----- |
| activity | Instance of the activity where the showPaywall method is called |
| action | Action to be used for page display it is String value|

 
# Sending Extra Data
You could also use extraData parameter for sending extra data to paywall pages via Bundle. 

For example Example model

 ``` kotlin
val extraDataBundle = Bundle()
extraDataBundle.putString(“Name”, example.name)
extraDataBundle.putInt(“Id”, example.id)
 ``` 

 ``` kotlin 
DeepWall.showPaywall(
activity = this,
action = “ACTION”,
extraData = bundle
)
 ``` 
 
# Closing Paywall Page
You could use closePaywall method to close Paywall pages.
 ``` kotlin
DeepWall.closePaywall()
 ``` 
 
 # Consume Products
You can use consumeProduct method to consume your products.
 ``` kotlin
DeepWall.consumeProduct(productId : String)
 ``` 
 
 # Subscription Upgrade/Downgrade 
 
 If you want to subscription upgrade or downgrade processes in your app you can use setProductUpgradePolicy method for general use 
  ``` kotlin
DeepWall.setProductUpgradePolicy(
    prorationType = ProrationType.IMMEDIATE_WITHOUT_PRORATION,
    upgradePolicy = PurchaseUpgradePolicy.ENABLE_ALL_POLICIES
)
 ``` 
  
  If you have different policies for your products you can use updateProductUpgradePolicy method for your paywalls. 
``` kotlin
DeepWall.updateProductUpgradePolicy(
    prorationType = ProrationType.IMMEDIATE_WITHOUT_PRORATION,
    upgradePolicy = PurchaseUpgradePolicy.ENABLE_ALL_POLICIES)
 ``` 
 
 For the detailed information about ProrationTypes you can see this document
 https://developer.android.com/reference/com/android/billingclient/api/BillingFlowParams.ProrationMode
 
 | PurchaseUpgradePolicy | |
| ----- | ----- |
| ENABLE_ALL_POLICIES | All policies are open. Users can upgrade or downgrade their products|
| DISABLE_ALL_POLICIES | All policies are closed. Users can not upgrade or downgrade their products |
| ENABLE_ONLY_UPGRADE | Users can only upgrade their products |
| ENABLE_ONLY_DOWNGRADE | Users can only downgrade their products|
 
 
# Event Handling
DeepWall posts some various events depending on ....

- PAYWALL_OPENED
Paywall opened event
data as PaywallOpenedInfo 
Parameters : pageId (Int)

- CLOSED
Paywall closed event
data as PaywallClosedInfo
Parameters : pageId (Int)

- PAYWALL_RESPONSE_FAILURE
PAYWALL response failure event
data as PaywallFailureResponse
Parameters:
errorCode: String
reason: String

- DO_NOT_SHOW
Paywall action show disabled event
data as PaywallActionShowDisabledInfo
Parameters:
pageId : Int

- PAYWALL_PURCHASING_PRODUCT
Paywall purchasing product event
data as PaywallPurchasingProductInfo
Parameters:
productCode: String


- PAYWALL_PURCHASE_SUCCESS
Purchase success event. Fired after receipt validation if Ploutos service active.
```kotlin 
val subscriptionResponse = it.data as SubscriptionResponse
```
Parameters:
type : DeepWallReceiptValidationType
result : SubscriptionDetail
-subscriptions : Array of PurchaseSubscriptionItem
-products : Array of String


- PAYWALL_PURCHASE_FAILED
Purchase failed event
```kotlin 
val data = it.data as SubscriptionErrorResponse
```
Parameters:
type : DeepWallReceiptValidationType
result : String

- EXTRA_DATA
Extra data received event
Parameters:Any

- PAYWALL_REQUESTED
Fired after paywall requested. Useful for displaying loading indicator in your app.

- PAYWALL_RESPONSE_RECEIVED
Fired after paywall response received. Useful for hiding loading indicator in your app.

- PAYWALL_NOT_OPENED
Fired if your product list is empty.

- CONSUME_SUCCESS
Fired after consume product request success

- CONSUME_FAILURE
Fired after consume product request failure

# Usage Example

 ``` kotlin
EventBus.subscribe(Consumer { 
when(it.type){
    DeepWallEvent.EXTRA_DATA.value -> { 
    Toast.makeText(this, it.data.toString(), Toast.LENGTH_SHORT).show() } 

DeepWallEvent.CLOSED.value ->  {
val data = it.data as PaywallClosedInfo
Toast.makeText(this@MainActivity, "PAYWALL_CLOSED : ${data.pageId}", Toast.LENGTH_LONG).show() }


DeepWallEvent.PAYWALL_PURCHASE_SUCCESS.value -> {
val subscriptionResponse = it.data as SubscriptionResponse
subscriptionResponse.result.subscriptionList.forEach { subItem->
    if(subItem.isActive == 1){
        //Do something
    }
        }
DeepWall.closePaywall()
}

DeepWallEvent.PAYWALL_OPENED.value -> { 
val data = it.data as PaywallOpenedInfo
Toast.makeText(this@MainActivity, "PAYWALL OPENED : ${key.pageId}", Toast.LENGTH_LONG).show() } 
}})
 ``` 
 



