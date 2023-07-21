# conviva-js-script-appanalytics
## Application Analytics for Conviva JavaScript Sensor
Use Application Analytics to autocollect events, track application specific events and state changes, and track users anonymously.

## Usage
To use the video sensor, download the Core JavaScript SDK file (version 4.5.6 or above) from Conviva JavaScript Sensor Integration and unzip the downloaded file.

### Download
To use the application sensor, use convivaAppTracker.js file from this repository

### Initialization

#### Add the following script to the HTML head section of your website:
Below 2 arguments are passed to function one is the global scope in which your app is loaded and other is the namespace by which you want to referr the app tracker.
```js
<script type="text/javascript">

(function(p, i) {
    if (!p[i]) {
        p.GlobalSnowplowNamespace = p.GlobalSnowplowNamespace || [];
        p.GlobalSnowplowNamespace.push(i);
        p[i] = function() {
            (p[i].q = p[i].q || []).push(arguments)
        };
        p[i].q = p[i].q || [];
    }
}(window, "apptracker"));
</script>

#### include convivaAppTracker.js as script tag.
<script src= "<<URL / Path to convivaAppTracker.js>>"></script>
```
#### Initialize the tracker
Init the SDK with appId, customerKey and optional parameters. 
- appId: set this to your understandable application name, reflecting the platform and brand of your app. As an example: "WEB App", "LGTV Web App".
- convivaCustomerKey: the unique string identifier for your Conviva account. Provided by Conviva / can be obtained in the Conviva Pulse dashboard. 

```js
window.apptracker('convivaAppTracker',  {
    appId: 'YOUR_APP_NAME_AS_STRING',
    convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
    contexts: {
        performanceTiming: true,
    },
});
```    
#### Set the user id (viewer id)
```js
// Set this after initializing the tracker and before autocollection
window.apptracker('setUserId','replace_me_by_the_userId');
```

#### Report Page View for tracking in-app page navigations.
```js
window.apptracker('trackPageView');
```

#### Enable autocollection
```js
window.apptracker('enableLinkClickTracking');
window.apptracker('enableButtonClickTracking');
```
#### Enable Error Tracking
To enable automatic tracking of run-time exceptions in the application, use the following API:
```js
window.apptracker('enableErrorTracking');
```
Alternatively, you could report exceptions manually using the following API:

```js
window.apptracker('trackError', {
    message: 'Cannot get user object',
    filename: 'shop.js',
    error: exceptionObj //Exception object containing properties describing the exception.
});
```

#### Custom event tracking to track your application specific events and state changes
Use trackCustomEvent() API to track all kinds of events. This API provides 2 fields to describe the tracked events.

name - Name of the custom event. (Mandatory)

data - Any type of data in string format.

The following example shows the implementation of the 'onClick' event listener to any element.
```js
let custom_data = "{\"identifier1\": \"test\",\"identifier2\": 1,\"identifier3\":true}"

window.apptracker('trackCustomEvent', {
    name: "custom_event_name",
    data: custom_data
});
```

## Setting / Unsetting Custom tags to report your application specific data.
Use setCustomTags() API to set all kinds of tags (key value pairs). This API provides 1 argument that accepts data in JSON Format to describe the tags.

The following example shows the implementation of setting custom tags.
In this example we have 3 different tags tagKey1, tagKey2, tagKey3 with 3 different values.

```js
let customTagsToSet = {"tagKey1": "tagValue1","tagKey2": 1,"tagKey3":true};
window.apptracker('setCustomTags', customTagsToSet);

```

Use unsetCustomTags() API to unset or remove that were already set. This API provides 1 argument to describe an array of tag keys to unset.

The following example shows the implementation of unset or remove custom tags.
```js
let customTagsToUnset = ['tagKey2', 'tagKey3'];
window.apptracker('unsetCustomTags' customTagsToUnset);

```
#### Note:- If your web applicationn has multiple html pages than above complete instructions have to follow for each html page.
