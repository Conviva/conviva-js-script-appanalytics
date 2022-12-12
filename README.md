# conviva-js-script-appanalytics
## Application Analytics for Conviva JavaScript Sensor
Use Application Analytics to autocollect events, track application specific events and state changes, and track users anonymously.

## Usage
To use the video sensor, download the Core JavaScript SDK file (version 4.5.6 or above) from Conviva JavaScript Sensor Integration and unzip the downloaded file.

### Download
To use the application sensor, use convivaAppTracker.js file from this repository

### Initialization

#### Add the following script to the HTML head section of your website:
```js
<script type="text/javascript" async=1>
;
(function(p, l, o, w, i, n, g) {
    if (!p[i]) {
        p.GlobalSnowplowNamespace = p.GlobalSnowplowNamespace || [];
        p.GlobalSnowplowNamespace.push(i);
        p[i] = function() {
            (p[i].q = p[i].q || []).push(arguments)
        };
        p[i].q = p[i].q || [];
        n = l.createElement(o);
        g = l.getElementsByTagName(o)[0];
        n.async = 1;
        n.src = w;
        g.parentNode.insertBefore(n, g)
    }
}(window, document, "script", "<<URL / Path to at_0.2.11.js>>", "apptracker"));
</script>
```
#### Initialize the tracker
```js
window.apptracker('convivaAppTracker',  {
    appId: '{{YOUR_APP_ID_ADVISED_BY_Conviva}}',
    convivaCustomerKey: '{{YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva}}',
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
Use setCustomTags() API to set all kinds of tags (key value pairs). This API provides 1 argument to describe the tags.

data - Any type of data in JSON format.

The following example shows the implementation of the 'onClick' event listener to any element.

```js
let customTagsToSet = {"tagKey1": "tagValue1","tagKey2": 1,"tagKey3":true};
window.apptracker('setCustomTags', customTagsToSet);

```

Use unsetCustomTags() API to unset or remove that were already set. This API provides 1 argument to describe an array of tag keys to unset.

keys - Array of strings representing tag keys.

The following example shows the implementation of the 'onClick' event listener to any element.
```js
let customTagsToUnset = ['tagKey2', 'tagKey3'];
window.apptracker('unsetCustomTags' customTagsToUnset);

```
