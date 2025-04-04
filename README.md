# Conviva JavaScript ECO SDK

Use Conviva JavaScript ECO SDK to auto-collect events and track application-specific events and state changes.

**Table of Contents**
- [Quick Start](#quick-start)
- [More Features](#more-features)
- [Auto-collected Events](#auto-collected-events)
- [FAQ](#faq)

## Quick Start

### 1. Installation
#### Add the following script to the HTML head section of your website:
Below 2 arguments are passed to function one is the global scope in which your app is loaded and other is the namespace by which you want to referr the app tracker.
```js
<script type="text/javascript">

(function(p, i) {
    if (!p[i]) {
        p.GlobalConvivaNamespace = p.GlobalConvivaNamespace || [];
        p.GlobalConvivaNamespace.push(i);
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
### 2. Initialization

 Initialize the tracker as early as possible during the DOM load sequence.

Init the SDK with appId, customerKey and optional parameters. 
- appId: set this to your understandable application name, reflecting the platform and brand of your app. As an example: "WEB App", "LGTV Web App".
- convivaCustomerKey: the unique string identifier for your Conviva account. Provided by Conviva / can be obtained in the Conviva Pulse dashboard. 
- appVersion: set app version in string format.

```js
window.apptracker('convivaAppTracker',  {
    appId: 'YOUR_APP_NAME_AS_STRING',
    convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
    appVersion: "1.1.0"
});
```
**appId** - A string value that uniquely identifies your app across platforms. For example: `"WEB App"`, `"LGTV App"`.

**convivaCustomerKey** - A string to identify a specific customer account. Use different keys for dev and prod. Find them in [Pulse](https://pulse.conviva.com/app/profile/applications) under My Profile (_Conviva login required_). 

**appVersion** - Set app version in string format.

#### Set the user id (viewer id)
```js
// Set this after initializing the tracker and before autocollection
window.apptracker('setUserId','replace_me_by_the_userId');
```

### 4. Report Page View

By default document.title is set as page title but you can also pass custom page title details in trackPageView.
```js
window.apptracker('trackPageView'); // default page title is document.title
window.apptracker('trackPageView', {"title": "Custom Page Title"});
```


## More Features

<details>
<summary><b>Track Custom Event</b></summary>
    
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
</details>

<details>
<summary><b>Set Custom Tags</b></summary>

Custom Tags are global tags applied to all events and persist throughout the application lifespan, or until they are removed.
Use setCustomTags() API to set all kinds of tags (key value pairs). This API provides 1 argument that accepts data in JSON Format to describe the tags.

The following example shows the implementation of setting custom tags.
In this example we have 3 different tags tagKey1, tagKey2, tagKey3 with 3 different values.

```js
let customTagsToSet = {"tagKey1": "tagValue1","tagKey2": 1,"tagKey3":true};
window.apptracker('setCustomTags', customTagsToSet);

```

**Clear previously set custom tags:**
Use unsetCustomTags() API to unset or remove that were already set. This API provides 1 argument to describe an array of tag keys to unset.

The following example shows the implementation of unset or remove custom tags.
```js
let customTagsToUnset = ['tagKey2', 'tagKey3'];
window.apptracker('unsetCustomTags' customTagsToUnset);
```

</details>

<details>
    <summary><b>Error Reporting</b></summary>
    
Uncaught exceptions and unhandled rejections are automatically collected and enabled by default. To report caught exceptions or other errors, use the following API:

```js
window.apptracker("trackError", { "message": "Test error" });

```
</details>

<details>
<summary><b>Client ID Synchronization</b></summary>

When integrating multiple Conviva ECO Sensor instances across different environments (e.g., subdomains of a single customer, mobile apps opening webviews), clientId may not be shared automatically. To maintain consistency, the sensor provides getClientId and setClientId APIs to manually synchronize clientId between instances.

Use Cases:
- Mobile App to WebView Synchronization
- Cross-Subdomain Client ID Synchronization

**Note**: The Conviva JavaScript ECO SDK utilizes **local storage** to cache some data.

- `getClientId()` – Retrieves the current Client ID
- `setClientId(clientId)` – Sets a specific Client ID


**Retrieve the Client ID**

Retrieves the current clientId from an already initialized sensor instance.
```js
// Always call getClientId() after convivaAppTracker()
window.apptracker('convivaAppTracker',  {
    appId: 'YOUR_APP_NAME_AS_STRING',
    convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
    appVersion: "1.1.0"
});
clientId = window.apptracker('getClientId');
```


**Set the Client ID**

Sets a specific clientId before initializing the SDK.

```js
// Always call setClientId() before convivaAppTracker()
window.apptracker('setClientId', clientId);
window.apptracker('convivaAppTracker',  {
    appId: 'YOUR_APP_NAME_AS_STRING',
    convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
    appVersion: "1.1.0"
});
```

</details>

<details>
<summary><b>Meta Tags Collection </b></summary>
This feature enables tracking of meta tags from the `<head>` section of an HTML page based on the provided configuration.

Example meta tags in an HTML Page:
```js
<html>
    <head>
        <meta name="keywords" content="HTML, CSS, JavaScript">
        <meta name="description" content="Free Web tutorials for HTML and CSS">
        <meta name="author" content="John Doe">
        <meta http-equiv="refresh" content="30">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta property="site_name" content="goole.com">
        <meta property="title" content="Sample app">
        <meta property="description" content="TV series content">
        <meta property="locale" content="es_ES">
        <meta property="type" content="video">
    </head>
</html>
```
**Configure Meta Tags Tracking**

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

</details>

<details>
<summary><b>Set Device Metadata</b></summary>


`deviceMetadata` is an object containing key-value pairs for predefined values, such as DeviceType and DeviceCategory, as well as additional properties like DeviceBrand, DeviceManufacturer, and DeviceModel.

Conviva automatically collects deviceMetadata for Web apps and mobile browsers. However, for devices like set-top boxes, smart TVs, gaming consoles, and others, you will need to manually set the `deviceMetadata`.

**Example of setting deviceMetadata:**

```js
    const deviceMetadata = {};
    deviceMetadata['DeviceBrand'] = 'Apple';
    deviceMetadata['DeviceManufacturer'] = 'Apple';
    deviceMetadata['DeviceModel'] = 'MacBookPro';
    deviceMetadata['DeviceType'] = 'Vehicle';
    deviceMetadata['DeviceVersion'] = 'NAForMac';
    deviceMetadata['OperatingSystemName'] = 'MAC';
    deviceMetadata['OperatingSystemVersion'] = '10.13.6';
    deviceMetadata['DeviceCategory'] = 'LNX';
    deviceMetadata['FrameworkName'] = 'Web';
    deviceMetadata['FrameworkVersion'] = '1.0.0';

  
    // Initialize app tracker by passing appId, customerKey and tracker configuration URL. By default xhrtracking is disabled. To enable, configure it to true in configuration argument
    tracker('convivaAppTracker', {
      appId: 'JS-App-Sensor-Test-App-v1',
      appVersion:'19.2.0-rc',
      convivaCustomerKey: '4d2f03dddf417990f520f09d79b11ab014c39dab',
      deviceMetadata: deviceMetadata,
      contexts: {
        performanceTiming: true,
        session: true
      }
    });
```

<details>
    <summary><b>The table of predefined metadata keys for deviceMetadata</b></summary>
    

| Key                       | Type                           | Description                                                                        | Example Values                                  |
|---------------------------|--------------------------------|------------------------------------------------------------------------------------|------------------------------------------------|
| DeviceBrand               | string                         | Brand of the device                                                                | `"Comcast"`, `"LG"`, `"Google"`, `"Vizio"`          |
| DeviceManufacturer        | string                         | Manufacturer of the device                                                         | `"Sony"`, `"Comcast"`, `"Google"`, `"Microsoft"`              |
| DeviceModel               | string                         | Model of the device                                                                | `"Comcast Flex"`, `"UTU7000_KA"`, `"Xbox One"`            |
| DeviceType                | Prescribed values of DeviceType | Type of the device. Only allows the DeviceType values and discards any other string values | DESKTOP, Console, Mobile (see [table below](#devicecategory-pre-defined-string-values))     |
| DeviceVersion             | string                         | Device firmware version                                                            | `"10"`, `"9"`                                       |
| OperatingSystemName       | string                         | Name of the operating system used by the device, in uppercase                      | `"Tizen"`, `"webOS"`, `"Vizio`", `"Linux`", `"Xbox OS"`, `"Chrome OS"` |
| OperatingSystemVersion    | string                         | Version of the operating system used by the device                                 | `"10.10.1"`, `"8.1"`, `"T-INFOLINK2012-1012"`, `"1.56.500000"` |
| DeviceCategory            | Prescribed values of DeviceCategory | Device category to which the used device belongs. Only allows DeviceCategory values and discards any other string values | WEB, AND, PS (see [table below](#devicetype-pre-defined-string-values))                  |
| FrameworkName             | string                         | Application framework name                                                         | `"React TV"`, `"LightningJS"`, `"Angular"`                                            |
| FrameworkVersion          | string                         | Application framework version                                                      | `"1.2.3"`                                            |                                          |

#### DeviceCategory Pre-defined String Values:

| Value       | Description                                                                                                                                                                                                                                       |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AND         | The device is an Android device like Samsung Galaxy, Amazon Fire TV, Android TV, or Android Tablet.                                                                                                       |
| APL         | The device is an Apple device like iPhone or Apple TV.                                                                                                                                                     |
| CHR         | The device is a Google Chromecast STB or Android TV with built-in Chromecast.                                                                                                                             |
| DSKAPP      | The device is a desktop computer (including notebooks) where video is played in an installed app, as opposed to a browser.                                                                                |
| SIMULATOR   | The device is a simulated video session used for testing.                                                                                                                                                  |
| KAIOS       | The device is a phone or other device based on KaiOS OS, such as the Lyf Jio F30C.                                                                                                                         |
| LGTV        | The device is an LG smart TV, including NetCast and webOS.                                                                                                                                                 |
| LNX         | This mostly covers various Set-Top Boxes and Smart TVs that use custom Linux-based SDKs.                                                                                                                   |
| NINTENDO    | The device is a Nintendo games console, including Wii and Switch.                                                                                                                                          |
| PS          | The device is a PlayStation console, including PS3 and PS4.                                                                                                                                                |
| RK          | The device is a Roku device.                                                                                                                                                                               |
| SAMSUNGTV   | The device is a Samsung Smart TV, including Orsay and Tizen.                                                                                                                                               |
| VIDAA       | Vidaa-based devices, using an operating system developed by Hisense.                                                                                                                                       |
| VIZIOTV     | Category for native app integrations on Vizio TVs using the SmartCast platform (from 2016 onwards).                                                                                                        |
| WEB         | The device can be any device with an in-browser HTML5-based player. Video is played in the browser using HTML5 technology, in browsers like Chrome, Edge, Firefox, Internet Explorer, Opera, or Safari.     |
| WIN         | The device is a Windows OS-based handheld device, like a Windows Phone or Windows Tablet.                                                                                                                 |
| XB          | The device is an Xbox console, including Xbox 360 and Xbox One.                                                                                                                                            |

#### DeviceType Pre-defined String Values:

| Value     | Description                                                   |
|-----------|---------------------------------------------------------------|
| DESKTOP   | The device is a desktop or laptop computer.                   |
| Console   | The device is a gaming console.                               |
| Settop    | The device is a set-top box.                                  |
| Mobile    | The device is a mobile phone.                                 |
| Tablet    | The device is a tablet.                                       |
| SmartTV   | The device is a smart TV.                                     |
| Vehicle   | The device is a vehicle infotainment system.                  |
| Other     | Other device types.                                           |


</details>

</details>

## Auto-collected Events

Conviva automatically collects rich set of app performance metrics through app events after completing the [Quick Start](#quick-start).

<details>
  <summary><b>Auto-collected events table</b></summary>


Event | Occurrence | Notes |
------|------------|-------|
network_request | after receiving the network request response | only supports xmlHttpRequest/fetch|
page_ping | Max X and Y scroll positions difference comparing to the last event|
application_error | when an error occurrs in the application|
button_click | on the button click callback| only if element is type button or button tag \n preventDefault and stopPropagation prevents to auto collect these events|
link_click | on the link click callback|only if element is anchor tag \n preventDefault and stopPropagation prevents to auto collect these events|
application_background | when visibility state change to `hidden`|
application_foreground | when visibility state change to `visible`|
Largest Contentful Paint| timing information about the largest image or text paint before user input on a web page| Context|
First App Launch| First time launch in the browser|Custom Tag Context|
page_loaded | On "load" event listener | Used to compute Page Loads, Avg Document Load Time, Avg DNS Lookup Time, Avg Document Response Time metrics.

To learn about the default metrics for analyzing the native and web applications performance, such as App Crashes, Avg Screen Load Time, and Page Loads, refer to the [ECO Metrics](https://pulse.conviva.com/learning-center/content/eco/eco_metrics.html) page in the Learning Center.

</details>

### Limitations

<details>
  <summary><b>Clicks</b></summary>

The collection of all types of clicks is automatically supported, including those from standard HTML elements as well as elements created using React, Angular, and Vue frameworks. We also offer an experimental remote configuration specifically for click events, aiming to dynamically add support for non-standard or unsupported frameworks. For further assistance, please contact the Conviva support team. 
**Note:** `preventDefault` and `stopPropagation` will prevent the auto-collection of button and link click events.

**Migration of Pulse dimensions for clicks**

Starting with version [v1.1.2](https://github.com/Conviva/conviva-js-appanalytics/releases/tag/v1.1.2) of the SDK, the attribute keys for click events have been updated.
If you are using v1.1.1 or earlier and currently mapping `elementText`, you must update your configuration when upgrading to v1.1.2 or later. Specifically, update the mapping in [ECO Activation](https://pulse.conviva.com/app/activation/home) by mapping `elementText` to `text`, then redeploy to apply the changes.

To ensure metrics reflect the updates, please review and update your event/metric mappings in [ECO Activation](https://pulse.conviva.com/app/activation/home) if you are using any of the following attributes:
| <=v1.1.1                       | >=v1.1.2                       |
|--------------------------------|--------------------------------|
| elementType                    | elementType                    |
| elementText                    | text                           |
| elementName                    | elementName                    |
| elementValue                   | value                          |
| elementId                      | id                             |
| elementClasses                 | class                          |

</details>

<details>
  <summary><b>network_request</b></summary>

This feature supports tracking network requests triggered within the application using `XMLHttpRequest` and the Fetch API.

**Request and Response Body Collection:**

  Collected only when:
  - Size is < 10KB and content-length is available.
  - Response body is type JSON.
  - Content-type is `"json"`, `"text/plain"`, `"text/javascript"` or `"application/javascript"`.

 **Request and Response Header Collection:**

 Collected only when:
  - The server is provisioned with `"Access-Control-Expose-Headers:"`.

</details>

<details>
  <summary>Conviva Video Events to App Insight</summary>

We need minimum of the Video Sensor Core SDK Version of 4.5.13 to be in a stage to delegate the events:

[v4.5.13](https://github.com/Conviva/conviva-js-coresdk/releases/tag/v4.5.13)

4.5.13 (27/DEC/2022)
    - Supports broadcasting video events to Conviva Eco SDKs to consume. For non App Insights users, there is no impact.
    - We only send following fields from video events into Eco events (name, sid, iid, clid, st, cen, ced, an).

[v4.7.11](https://github.com/Conviva/conviva-js-coresdk/releases/tag/v4.7.11)

4.7.11 (30/SEP/2024)
    - Added required attributes in Video events broadcasted to Conviva ECO Sensor SDKs to consume. For non ECO users, there is no impact.
    - We send following fields from video events into Eco events (name, sid, iid, clid, st, cen, ced, an, pn, cl, lv, tags, vid, url, sst, sid, fw, fwv, mv, mn, old, new).

</details>

<details>
  <summary>Auto Collection</summary>
  
#### Autocollection of Errors
Auto collection for Errors / exceptions is enabled by default.

Alternatively, you could report exceptions manually using the following API:

```js
window.apptracker('trackError', {
    message: 'Cannot get user object',
    filename: 'shop.js',
    error: exceptionObj //Exception object containing properties describing the exception.
});
```
#### Autocollection of Clicks
Auto collection Link clicks and button clicks is available till version v1.1.1
Additionally, v1.1.2 enables collection of all kinds of clicks automatically and currently supports standard html elements, elements created using reactjs, angular and vue frameworks.

We additionally have an experimental remote config specifically for clicks to attempt adding support of any non-standard/unsupported frameworks dynamically. Kindly reach out to Conviva support team.

#### AutoCollection of Meta tags from HEAD section of HTML page
This feature supports to track the Meta tags from HEAD section of HTML page based on the config provided.

#### Auto Ingestion of "traceparent" header to network requests
This feature supports to ingest "traceparent" header into network requests based on the config provided. 
Note: This ingestion is disabled by default, reach out to Conviva Team enabling the tracking.

#### AutoCollection of Network Request made using XMLHttpRequest and fetch api
This feature supports to track the Network Requests triggerred with in application using XMLHttpRequest and fetch api
*Note: This collection is disabled by default, reach out to Conviva Team enabling the tracking.* <br>

<br> *Here are some of the granular details/limitations of the feature:*
* *Response and Request Body atributes are collected only when the:*
    * *size is < 10kb and the content-length is available* 
    * *response body is type json and content-type is "json", "text/plain", "text/javascript" or "application/javascript"*
* *Response and Request Headers are collected only when the:*
    * *server is provisioned with "Access-Control-Expose-Headers:*"* 


</details>

#### Note:- To integrate Conviva App Experience with web applications having multiple HTML pages, add the aforementioned instructions to each HTML page. 


## FAQ

[ECO Integration FAQ](https://pulse.conviva.com/learning-center/content/sensor_developer_center/tools/eco_integration/eco_integration_faq.htm)