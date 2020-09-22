# DeepWall Android SDK Usage

## Implementation

You must add this maven repo to project’s build.gradle

```gradle
allprojects {
   repositories {
       google()
       jcenter()
       maven { url https://raw.githubusercontent.com/Teknasyon-Teknoloji/deepwall-android-sdk/master/' }
  }
} 
``` 

You must add this implementation to app’s build.gradle

implementation 'deepwall:deepwall-core:1.0.0'

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
| Environment Style: DeepWallEnvironmentStyle | Environment style for landing page appearance. Values: AUTOMATIC: Uses system appearance (Default) , LIGHT: Light appearance, DARK: Dark appearance|





# Important:
You must call setUserProperties method before requesting any landing page.

```kotlin
DeepWall.setUserProperties( 
deviceId = androidId, 
languageCode = DeepWallLanguage.TURKISH, 
countryCode = DeepWallCountry.TURKEY,
environmentStyle = DeepWallEnvironmentStyle.DARK )
 ``` 
 
# Updating User Properties

You could update the following user properties with DeepWall.updateUserProperties method.
| Parameters | |
| ----- | ----- |
| Country : String | Two-letter country code; Usage: DeepWallCountry.TURKEY // TURKEY|
| Language: String | Two-letter language code; Usage: DeepWallLanguage.TURKISH // TURKISH  |
| Environment Style: DeepWallEnvironmentStyle | Environment style for landing page appearance. Values: AUTOMATIC: Uses system appearance (Default) , LIGHT: Light appearance, DARK: Dark appearance|

 ```kotlin
DeepWall.updateUserProperties(
languageCode = DeepWallLanguage.ARABIC, 
countryCode = DeepWallCountry.SAUDI_ARABIA,
environmentStyle = DeepWallEnvironmentStyle.DARK  
)
 ``` 

# Requesting Landing Page

 ``` kotlin
DeepWall.showLanding(
   activity = this,
   action = “ACTION_KEY”
)
 ``` 

| Parameters | |
| ----- | ----- |
| activity | Instance of the activity where the showLanding method is called |
| action | Action to be used for page display it is String value|

 
# Sending Extra Data
You could also use extraData parameter for sending extra data to landing pages via Bundle. 

For example Example model

 ``` kotlin
val extraDataBundle = Bundle()
extraDataBundle.putString(“Name”, example.name)
extraDataBundle.putInt(“Id”, example.id)
 ``` 

 ``` kotlin 
DeepWall.showLanding(
activity = this,
action = “ACTION”,
extraData = bundle
)
 ``` 
 
# Closing Landing Page
You could use closeLanding method to close landing pages.
 ``` kotlin
DeepWall.closeLanding()
 ``` 
 
# Event Handling
DeepWall posts some various events depending on ....

- LANDING_OPENED
Landing opened event
 
- CLOSED
Landing closed event

- LANDING_RESPONSE_FAILURE
Landing response failure event
Parameters:
errorCode: String
reason: String

- DO_NOT_SHOW
Landing action show disabled event

- LANDING_PURCHASING_PRODUCT
Landing purchasing product event
Parameters:
productCode: String


- LANDING_PURCHASE_SUCCESS
Purchase success event. Fired after receipt validation if Ploutos service active.
Parameters:
subscriptions: Array of SubscriptionItem
products: Array of ProductItem


- LANDING_PURCHASE_FAILED
Purchase failed event
Parameters:Error String

- EXTRA_DATA
Extra data received event
Parameters:Any

# Usage Example

 ``` kotlin
EventBus.subscribe(Consumer { 
when(it.type){
DeepWallEvent.EXTRA_DATA.value -> 
{ Toast.makeText(this, it.data.toString(), Toast.LENGTH_SHORT).show() } 

DeepWallEvent.CLOSED.value -> 
{ Toast.makeText(this, "Closed", Toast.LENGTH_SHORT).show() } 

DeepWallEvent.LANDING_OPENED.value -> 
{ Log.d("LANDING_STATUS", "LandingOpened") } 
}})
 ``` 
 



