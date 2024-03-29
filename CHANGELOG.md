
# Changelog
## 0.5.3 (07/FEB/2024)
* Added capability to generate "traceparent" header for network requests based on config.
* Fixes issue of missing "assetName" from conviva video events.

## 0.4.8 (25/JAN/2024)
* Added capability of autotracking meta tags present inside HEAD section of HTML page based on keys provided as config. Please refer to [Meta Tags feature](https://github.com/Conviva/conviva-js-script-appanalytics?tab=readme-ov-file#autocollection-of-meta-tags-from-head-section-of-html-page) for more details.
* Added functionality for app to report appVersion as part of init config. Please refer to [Initialize the tracker](https://github.com/Conviva/conviva-js-script-appanalytics?tab=readme-ov-file#initialize-the-tracker) for more details.
* Enhances network requests & response collection feature to support collection of response and request body where content-type is `text/javascript`, `application/javascript` along with already supported `text/plain` and `application/json`.
* Enhances trackPageView api to take custom page title as input. Please refer to [trackPageView Info](https://github.com/Conviva/conviva-js-script-appanalytics?tab=readme-ov-file#report-page-view-for-tracking-in-app-page-navigations) for more details.

## 0.4.6 (05/JAN/2024)
* Added remote-config control in button and link click tracking custom api.

## 0.4.5 (02/JAN/2024)
* Fixes issue of reporting incorrect app video bounce rate metric because conviva video event being dropped when video session id is negative.

## 0.4.4 (15/DEC/2023)
* Fixes issue of handling input to fetch api when input is instance of Request object instead of url string during network request tracking.

## 0.4.3 (27/OCT/2023)
* Enhances network requests & response collection feature to capture limited (json only, size limit 10kb) and controlled set of information from headers and body.

## 0.3.34 (19/OCT/2023)
* Fixes issue in linkClickTracking & buttonClickTracking generating lots of console errors due to internal initialization sequence.
* Fixes and stops reporting exceptions / errors containing no information, status code and stacktrace. Such as browser errors caused due to extensions.
* Removed the dependency on "Request()" API while tracking network requests to avoid side effects of API compatibility to affect xhr or fetch API calls in application.

## 0.3.32 (06/OCT/2023)
* Supports the trackCustomEvent() with JSON Object as an argument
* Enhances default blocklist for Network Request Tracking
* Fixes issue of not reporting network request when the fetch request is failed
* Fixes the issue of target url being reported with only relative path in case of xmlHttpRequest Request is made with relative path by appending `window.location.domain` to the relative path.

## 0.3.29 (06/SEP/2023)
* Optimised performance timing context.

## 0.3.27 (27/JUL/2023)
* Fixes issue of elemntId not reported in button click event.

## 0.3.26 (17/JUL/2023)
* Supports collection of network request(http/https) performance metrics using the xmlhttpRequest or fetch Interceptor(which is disabled by default)

## 0.3.25 (28/JUN/2023)
* Fixes issue of duplicate eventIndex when app is opened in two or more tabs.

## 0.3.20 (01/JUN/2023)
* Removed unused fields from request payload.
* Fixes issue with default time of 'cacheRefreshInterval' when its fetch failed from remote config.

## 0.3.17 (24/APR/2023))
* Implements feature of the client id based event sequence number.

## 0.3.16 (14/APR/2023)
* Fixed Issue with remote config not applied after refresh interval.

## 0.3.7 (23/DEC/2022)
* Detects Conviva Video Sensor events and metadata.

## 0.3.4 (12/DEC/2022)
* Exposes setCustomTags & unsetCustomTags APIs to report custom application data.

## 0.3.2 (28/OCT/2022)
* Added event_index and previousEventTimestamp into every HB.

## 0.2.11 (08/SEP/2022)
* Remote Config feature supported
* Improved periodic ping request (page_ping) feature to ping unconditionally regardless of user activity on the page.
* Fixed issue with button click detection on DOM change & avoided duplicate events being sent.
* Fixed issue with Page Title not being updated correctly

