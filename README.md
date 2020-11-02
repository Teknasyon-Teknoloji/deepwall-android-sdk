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

implementation 'deepwall:deepwall-core:2.0.0'

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
 
# Event Handling
DeepWall posts some various events depending on ....

- PAYWALL_OPENED
Paywall opened event
 
- CLOSED
Paywall closed event

- PAYWALL_RESPONSE_FAILURE
PAYWALL response failure event
Parameters:
errorCode: String
reason: String

- DO_NOT_SHOW
Paywall action show disabled event

- PAYWALL_PURCHASING_PRODUCT
Paywall purchasing product event
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
-subscriptionList : Array of PurchaseSubscriptionItem
-productList : Array of String


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

# Usage Example

 ``` kotlin
EventBus.subscribe(Consumer { 
when(it.type){
DeepWallEvent.EXTRA_DATA.value -> 
{ Toast.makeText(this, it.data.toString(), Toast.LENGTH_SHORT).show() } 

DeepWallEvent.CLOSED.value -> 
{ Toast.makeText(this, "Closed", Toast.LENGTH_SHORT).show() } 

DeepWallEvent.PAYWALL_PURCHASE_SUCCESS.value -> 
val subscriptionResponse = it.data as SubscriptionResponse
subscriptionResponse.result.subscriptionList.forEach { subItem->
    if(subItem.isActive == 1){
        //Do something
    }
        }
DeepWall.closePaywall()

DeepWallEvent.PAYWALL_OPENED.value -> 
{ Log.d("PAYWALL_STATUS", "Paywall Opened") } 
}})
 ``` 
 



