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
- appVersion: set app version in string format.

```js
window.apptracker('convivaAppTracker',  {
    appId: 'YOUR_APP_NAME_AS_STRING',
    convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
    appVersion: "1.1.0",
    contexts: {
        performanceTiming: true
    },
});
```    
#### Set the user id (viewer id)
```js
// Set this after initializing the tracker and before autocollection
window.apptracker('setUserId','replace_me_by_the_userId');
```

#### Report Page View for tracking in-app page navigations.
By default document.title is set as page title but you can also pass custom page title details in trackPageView.
```js
window.apptracker('trackPageView'); // default page title is document.title
window.apptracker('trackPageView', {"title": "Custom Page Title"});
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

data - Any type of data in json format.

The following example shows the data in JSON format.
```js
let custom_data = {
                    "identifier1": "test",
                    "identifier2": 1,
                    "identifier3":true
                  };

window.apptracker('trackCustomEvent', {
    name: "custom_event_name",
    data: custom_data
});
```

#### Conviva Video Events to App Insight
We need minimum of the Video Sensor Core SDK Version of 4.5.13 to be in a stage to delegate the events:

[v4.5.13](https://github.com/Conviva/conviva-js-coresdk/releases/tag/v4.5.13)

4.5.13 (27/DEC/2022)
    - Supports broadcasting video events to Conviva App Insights SDKs to consume. For non App Insights users, there is no impact.

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

## AutoCollection of Network Request made using XMLHttpRequest and fetch api
This feature supports to track the Network Requests triggerred with in application using XMLHttpRequest and fetch api
*Note: This collection is disabled by default, reach out to Conviva Team enabling the tracking.* <br>

<br> *Here are some of the granular details/limitations of the feature:*
* *Response and Request Body atributes are collected only when the:*
    * *size is < 10kb and the content-length is available* 
    * *response body is type json and content-type is "json", "text/plain", "text/javascript" or "application/javascript"*
* *Response and Request Headers are collected only when the:*
    * *server is provisioned with "Access-Control-Expose-Headers:*"* 



## AutoCollection of Meta tags from HEAD section of HTML page
This feature supports to track the Meta tags from HEAD section of HTML page based on the config provided.
#### metaTagsTracking is the config to collect Meta tags and can be provided as part of tracker Initialization under configs field.

Structure of metaTagsTracking config 
```js
//for below meta tags
<HTML>
    <HEAD>
    <meta name="keywords" content="HTML, CSS, JavaScript">
    <meta name="description" content="Free Web tutorials for HTML and CSS">
    <meta name="author" content="John Doe">
    <meta http-equiv="refresh" content="30">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta property="site_name" content="goole.com">
    <meta property="title" content="Sample app">
    <meta property="description" content="Tv series content">
    <meta property="locale" content="es_ES">
    <meta property="type" content="video">
    </HEAD>
</HTML>

//Example config to collect all name attributes and it's value and few certain property attributes and it's value.
 window.apptracker('convivaAppTracker',  {
    appId: 'YOUR_APP_NAME_AS_STRING',
    convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
    appVersion: "1.1.0",
    contexts: {
        performanceTiming: true
    },
    configs:{
        metaTagsTracking: {
          "tags":
            [
              {
                "key": "name", //mandatory //here key sepcifies what attributes tag to collect
                "value": "content", //mandatory //value specifies the value .
              },
              {
                "key": "property",
                "value": "content",
                "condition": ["title", "locale"] // optional //value of attributes placed in key to collect 
              },
              ...
            ]
        }
    }
});

```



#### Note:- To integrate Conviva App Experience with web applications having multiple HTML pages, add the aforementioned instructions to each HTML page. 
