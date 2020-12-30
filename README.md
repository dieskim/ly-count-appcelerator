# Count.ly Titanium Test App

## easy Test app you can download, build and run to test all the functions in this modul [Countly Appcelerator Module Test App](https://github.com/dieskim/ly-count-appcelerator)
https://github.com/dieskim/ly-count-appcelerator

### Any pull requests and suggestions welcome!
### Author: Dieskim of (dieskim.me)](https://www.dieskim.me)

## Installation

1. Import this Git as an new Project in your Appcelerator Studio
2. Build and run
3. Edit as needed to test variables


## Usage

**Require the Count.ly Module**
```
var Countly = require('count.ly.messaging');
```

### SETUP Count.ly WITHOUT Messaging - Push

**Start Count.ly on own server without Messaging**
```
Countly.start('COUNTLY_APPKEY','http://YOUR_HOST.com');
```

**Start Count.ly on cloud without Messaging**
```
Countly.startOnCloud('COUNTLY_APPKEY');
```

### SETUP Count.ly WITH Messaging - Push

**Set Push Setup functions**
```
// START IF - RECEIVE PUSH
if (OS_ANDROID){

// ADD EVENTLISTENTER AND FUNCTION TO MODULE
Countly.addEventListener('receivePush',function(pushMessageData){

// *** IN ANDROID THERE IS NO NEED TO RUN recordPushOpen as it happens Automatically on the Native Module side **//

// Ti.API.info Raw pushMessage
Ti.API.info("Received Push");  
Ti.API.info(JSON.stringify(pushMessageData));  

var pushID = pushMessageData.id;
var pushAlertMessage = pushMessageData.message;
var pushType = pushMessageData.type || 'unknownType';
var pushLink = pushMessageData.link || '';
var pushSound = pushMessageData.sound || '';
var pushData = pushMessageData.data;                        // all message data if needed

Ti.API.info("pushID: " + pushID + " pushAlertMessage: " + pushAlertMessage + "pushType: " + pushType + " pushData: " + pushData + " pushSound: " + pushSound);

if (pushType == "hasLink"){

///////////////////////////////////////////////////////////
//              SHOW AN LINK ALERT HERE                 //
// 1. Use info  - pushType
//              - pushLink
//              - pushAlertMessage
// 2. Once user Takes action log action with recordPushAction using pushID


}else if (pushType == "hasReview"){

// SHOW AN REVIEW ALERT HERE 

}else if (pushType == "hasMessage"){

// SHOW NORMAL ALERT HERE

};

});

}else{


// START FUNCTION - registerForPush
function registerForPush() {
Ti.Network.registerForPushNotifications({
success: deviceTokenSuccess,
error: deviceTokenError,
callback: receivePush
});

// Remove event listener once registered for push notifications
Ti.App.iOS.removeEventListener('usernotificationsettings', registerForPush); 
};
// END FUNCTION - registerForPush

// addEventListener to Wait for user settings to be registered before registering for push notifications
Ti.App.iOS.addEventListener('usernotificationsettings', registerForPush);

// Start Function - deviceTokenSuccess
function deviceTokenSuccess(e) {

// get Ti.App.Properties - pushSubscribed - to check if already subscribed or not
var pushSubscribed = Ti.App.Properties.getString('pushSubscribed',false);
Ti.API.log('pushSubscribed Value: ' + pushSubscribed);  

// START IF - not subscribed then subscribe
if (pushSubscribed != true){

Ti.API.log("Not Subscribed to Count.ly Push - Subscribe with deviceToken: " + e.deviceToken);

// run Count.ly Register Device for Push
Countly.registerDeviceSuccess(e.deviceToken);    

// Set Ti.App.Properties push_channels
Ti.App.Properties.setString('pushSubscribed',true); 

}else{

Ti.API.log('Already Subscribed to Count.ly Push, wont subscribe again!');

};
// END IF - not subscribed then subscribe      

};
// End Function - deviceTokenSuccess

// Start Function - deviceTokenError
function deviceTokenError(e) {

Ti.API.log("Failed to Find Token" + e.error);

// run Count.ly Register registerDeviceError
Countly.registerDeviceError();

};
// End Function - deviceTokenError

// START FUNCTION - receivePush for iOS
function receivePush(pushMessage) {         

// Ti.API.info Raw pushMessage
Ti.API.info("Received Push:" + JSON.stringify(pushMessage));  

//////////////////////////////////////////////////////////////////////////////////////////////////////////
//                                                  pushMessage EXAMPLE                                 //
//                                                                                                      //
//          {"code":0,                                                                                  //
//          "data":{    "alert":"Test Message",                                                         //
//                      "category":"[CLY]_url",                                                         //
//                      "c":{"l":"http://count.ly","i":"551b9d03f593a55e11ea62c0"},                     //
//                      "sound":"default",                                                              //
//                      "aps":{"category":"[CLY]_url","sound":"default","alert":"test5"}                //
//                  },                                                                                  //
//          "type":"remote",                                                                            //
//          "source":{},                                                                                //
//          "inBackground":true,                                                                        //
//          "success":true};                                                                            //
//                                                                                                      //
//////////////////////////////////////////////////////////////////////////////////////////////////////////


// pushData
var pushData = pushMessage.data;

// set pushUserInfoDictionary
var pushUserInfoDictionary = {userInfoDictionary: pushMessage.data.c};  
// run Count.ly record Push Opened  
Countly.recordPushOpen(pushUserInfoDictionary); 


// START IF - Set pushAlertMessage
if (pushMessage.data.alert){

var pushAlertMessage = pushMessage.data.alert;

};
// END IF -  Set pushAlertMessage 


// START IF - Set PUSH CATEGORY TYPE - check more here - http://resources.count.ly/v1.0/docs/countly-push-for-ios
if (pushMessage.data.c.l) {

var pushType = "hasLink";
var pushLink = pushMessage.data.c.l;

///////////////////////////////////////////////////////////
//              SHOW AN LINK ALERT HERE                 //
// 1. Use info  - pushType
//              - pushLink
//              - pushAlertMessage
// 2. Once user Takes action log action with

// Count.ly record Push Action  
// Countly.recordPushAction(pushUserInfoDictionary);    

//////////////////////////////////////////////////////////

} else if (pushMessage.data.c.r) {

var pushType = "hasReview";

// SHOW AN REVIEW ALERT HERE 

} else if (pushMessage.data.c.u) {

var pushType = "hasUpdate";

// SHOW AN UPDATE ALERT HERE

} else {

var pushType = "hasMessage";

// SHOW NORMAL ALERT HERE

};
// END IF - Set PUSH CATEGORY TYPE

};
// END FUNCTION - receivePush for iOS

};
// START IF - RECEIVE PUSH

```

**START Countly with Messaging - DEVELOPMENT TEST**
```
// START IF - Android or iOS
if (OS_ANDROID){

    // START Countly with Messaging - DEVELOPMENT TEST
    Countly.startMessagingTest('COUNTLY_APP_KEY','http://yourserver.com','GCM_PROJECT_NUMBER');

}else{

    // START Countly with Messaging - DEVELOPMENT TEST
    Countly.setMessagingDeveloperMode();    // setMessagingDeveloperMode
    Countly.startMessagingTest('COUNTLY_APP_KEY','http://yourserver.com');

};
// END IF - Android or iOS

```

**START Countly with Messaging - PRODUCTION**
```
// START IF - Android or iOS
if (OS_ANDROID){

    // START Countly with Messaging - PRODUCTION
    //Countly.startMessaging('COUNTLY_APP_KEY','http://yourserver.com','GCM_PROJECT_NUMBER');

}else{

    //START Countly with Messaging - PRODUCTION
    //Countly.startMessaging('COUNTLY_APP_KEY','http://yourserver.com');

};
// END IF - Android or iOS

```

### User Locations

```
// Countly Set user location
var latitudeString = 12;
var longitudeString = 10;
Countly.setLocation(latitudeString,longitudeString);
```
- Takes two strings: latitudeString and longitudeString of 2 digit lengths

### Events

**Set any of the following Fields in an Object**

```
var segmentation = {device:"iPhone 4S", country:"USA"};
var eventData = {name: "keySegmentationCountSum", segmentation:segmentation, count: 1, sum: 0.99};
```
- name (required) : Name of the event to track  
- _(example - Track clicks on the help button "clickedHelp" )_
- count (required) : Number to increment the event in the db
- _(example - User purchases item increment by 1 )_
- sum : If the event is tied to an overall numerical data, such as a purchase, we can use sum to keep track of that
- _(example - 0.99)_
- segmentation : Categorization of the event
- _(example - User is from USA and uses an iPhone 4S so the segmentation will be {device:"iPhone 4S", country:"USA"} )_

**Track Events Examples**

```
var segmentation = {device:"iPhone 4S", country:"USA"};

Ti.API.log("Send keyCount Event");
var eventData = {name: "keyCount", count: 1};
Countly.event(eventData);

Ti.API.log("Send keyCountSum Event");
var eventData = {name: "keyCountSum", count: 1, sum: 0.99};
Countly.event(eventData);

Ti.API.log("Send keySegmentationCount Event");
var eventData = {name: "keySegmentationCount", segmentation:segmentation, count: 1};
Countly.event(eventData);

Ti.API.log("Send keySegmentationCountSum Event");
var eventData = {name: "keySegmentationCountSum", segmentation:segmentation, count: 1, sum: 0.99};
Countly.event(eventData);
```

### UserData

**Set any of the following Fields in an Object**

**Set userData{} as information about user
**Possible keys are:

- name - (String) providing user's full name
- username - (String) providing user's nickname
- email - (String) providing user's email address
- organization - (String) providing user's organization's name where user works
- phone - (String) providing user's phone number
- picture - (String) providing WWW URL to user's avatar or profile picture
- picturePath - (String) providing local path to user's avatar or profile picture
- gender - (String) providing user's gender as M for male and F for female
- byear - (int) providing user's year of birth as integer
```
var userData = {	name: "testName",
username: "testUsername",
email: "testemail@gmail.com",
organization: "testOrg",
phone: "testPhone",
picture: "https://count.ly/wp-content/uploads/2014/10/logo.png",
picturePath: "/images/appicon.png",
gender: "M",
byear: "1980",
};
```

**Set customUserData{} as information about user with custom properties
**In customUserData you can provide any string key values to be stored with user

```
var customUserData = {	key1: "value1",
key2:"value2",
};
```

**Set Userdata as set in userData and customData
**Can contain both userData and customData - or just userdata

```
Ti.API.log("Set UserData");
var args = {	userData:userData,
customUserData:customUserData,
};

Countly.userData(args);
```

### Crash Reporting
** There are 3 types of Crashes that can be logged:
- Fatal Native Exception/Crash - Automatically logged via Count.ly SDK
- Fatal Javascript Exception/Crash - Automatically logged via Module after one function added (Titanium SDK > 4.1 only)
- Non-Fatal Javascript Exception/Crash - Manually Logged by user in App code as needed via- Countly.recordHandledException
** The user can also add entries to Crash logs in app code via - Countly.addCrashLog
** 4 CrashTest are built in to help test crash reporting


**Start Crash Reporting - WITH or WITHOUT Segments**
- Segments can be added as key values - these are sent with every crash
- When Crash Reporting is started the Countly SDK will automatically catch and log Fatal Native Exception/Crash

```
// Start Crash Reporting - WITHOUT Segments
Countly.startCrashReporting();
```

```
// Start Crash Reporting - WITH Segments
// Add Keypairs to be added to every Crash that is logged for this app
// Example: FacebookSDK: "4.0"
var segments = { 	FacebookSDK: "4.0",
key2: "value2",
};

Countly.startCrashReportingWithSegments(segments);
```

**Add Fatal Javascript Exception/Crash Support**
- Automatically logged via Module after one function added (Titanium SDK > 4.1 only)
- This crash type will NOT QUIT out of app in Development (shows red error box), but will QUIT out of app in Production.
- Error will be logged on Javascript side and sent to native module to send to Countly
- For this to work you for need to add the uncaughtException listener to the app
- Then run the Countly.recordUncaughtException inside the 

```
Ti.App.addEventListener('uncaughtException', function(exception) {

Ti.API.log("Javascript Fatal Exception");   // remove if you want

// send exception to Countly
Countly.recordUncaughtException(exception);	

});

```

**Add Non-Fatal Javascript Exception/Crash Support**
- This is a Crash Report you can Define and send Manually very much like an error log
- run the Countly.recordHandledException function to manually log a non-fatal crash report

```
// Non-Fatal Javascript Exception/Crash Manually via  to Countly
Countly.recordHandledException(exception);
```

**Add Crash Log Entry**
- add Keypairs to a crash log as needed throughout your app
- will be sent with crash log when/if app crashes
- Example: Button Clicked, Audio Downloaded etc

```
Ti.API.log("addCrashLog");

var crashLog = {    yourMessage: "Error Message",
key1: "value1",
};

Countly.addCrashLog(crashLog);
```

**Crash Test**
- There are 4 types of Crash Test Built in
- crashTest 3 will cause a native error and exit out of app - On Android Create a button to Run the Function and run it a few times quickly to make it exit the app
```
\\ NATIVE CRASH TESTS - Exits out of app and logs to Countly
Countly.crashTest(3);

\\ Other Crash Tests - Do not exit out of app - get logged via Ti.App.addEventListener uncaughtException
Countly.crashTest(1);	
Countly.crashTest(2);
Countly.crashTest(4);
```

## Author

Author: Dieskim of (dieskim.me)](https://www.dieskim.me)

## License

MIT as in License.txt
