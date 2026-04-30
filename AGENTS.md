# AGENTS.md

Conviva JavaScript Script App Analytics SDK — AI Agent Integration Specification

Repository: `Conviva/conviva-js-script-appanalytics`
SDK Type: Script-based (CDN)
Language: JavaScript
Platform: Web

---

# Purpose

This file is the authoritative operating contract for AI agents integrating the Conviva script-based App Analytics SDK. It takes precedence over all other documentation.

Agents help developers integrate the SDK correctly, with minimal diffs, using only documented APIs.

---

# Hard Rules

## Never invent SDK APIs

Agents must only use the script-based API via `window.apptracker(...)`. Do NOT generate npm/yarn imports, ES module usage, undocumented methods, undocumented config fields, or automatic tracking assumptions.

If a requested feature cannot be implemented using the allowed API surface, the agent must say so explicitly.

## Never fabricate required values

Agents must never invent customer keys, appId, appVersion, or userId. Missing values must be placeholders: `REPLACE_ME`.

## Minimize codebase modifications

Modify as few files as possible. Prefer existing bootstrap points. **Smallest safe diff.**

## Initialize exactly once

SDK must initialize once, early in page load. Never inside loops, components, or repeated event handlers.

## Never implement optional APIs without explicit request

`trackCustomEvent` must not be added to any integration unless the developer explicitly asks for it.

The manual form-tracking APIs (`trackFormView`, `trackFormSubmitSuccess`, `trackFormSubmitError`, `trackFormValidationError`) must not be added unless the developer explicitly asks for them. Form tracking is primarily an **auto-collected** feature controlled by Conviva remote configuration — no code is required to enable it.

## Never hardcode CDN versions

Agents must not hardcode the SDK CDN version unless the developer explicitly requests a specific version.

- Verify the latest version from the repository's GitHub Tags or Releases before generating any CDN URL.
- If the latest version cannot be verified, use a placeholder: `vREPLACE_ME_VERSION`
- This applies to both the DPI script and the Replay script.

## Always wrap integration code using try-catch

All the Conviva integration code generated into application must be wrapped inside try catch blocks. They must not cause unhandled exceptions or crashes.

---

# Required Inputs Before Integration

| Field              | Required | Purpose / Example                     |
| ------------------ | -------- | ------------------------------------- |
| convivaCustomerKey | Yes      | `"abc123"`                            |
| appId              | Yes      | `"WebApp"`                            |
| appVersion         | Yes      | `"1.0.0"`                             |
| appType            | Yes      | `"SPA"` or `"MPA"`                    |
| HTML entry point   | Often    | where to add the bootstrap snippet    |
| user identity      | Often    | for setUserId; verify not PII         |
| session replay     | Ask      | optional feature — must ask developer |

---

# Allowed API Surface

```js
window.apptracker('convivaAppTracker', { appId, convivaCustomerKey, appVersion });
window.apptracker('setUserId', userId);
window.apptracker('trackPageView');
window.apptracker('trackPageView', { title: 'Custom Page Title' }); // optional title
window.apptracker('trackCustomEvent', { name: 'event_name', data: {} }); // [OPTIONAL]

// [OPTIONAL] Manual form-tracking APIs — only add when the developer explicitly asks.
window.apptracker('trackFormView', formId);
window.apptracker('trackFormSubmitSuccess', formId);
window.apptracker('trackFormSubmitError', formId, errorType);
window.apptracker('trackFormValidationError', formId, fieldName, errorType);
```

---

# Agent Integration Workflow

### Step 1 — Identify app type

Identify SPA or MPA. If unclear, ask the developer.

### Step 2 — Identify HTML integration point

Ask the developer where to add the Conviva script (HTML `<head>` or template file). Do not assume file names or structure.

### Step 3 — Add bootstrap snippet

Place in `<head>` before the CDN script:

```html
<script>
(function(p, i) {
  if (!p[i]) {
    p.GlobalConvivaNamespace = p.GlobalConvivaNamespace || [];
    p.GlobalConvivaNamespace.push(i);
    p[i] = function() { (p[i].q = p[i].q || []).push(arguments) };
    p[i].q = p[i].q || [];
  }
}(window, "apptracker"));
</script>
```

### Step 4 — Add CDN script

Verify the latest version from GitHub Tags/Releases before generating the URL:

```html
<script src="https://sensor.conviva.com/dpi/releases/vREPLACE_ME_VERSION/convivaAppTracker.js"></script>
```

### Step 5 — Initialize SDK

```js
window.apptracker('convivaAppTracker', {
  appId: 'REPLACE_ME_APP_ID',
  convivaCustomerKey: 'REPLACE_ME_CUSTOMER_KEY',
  appVersion: 'REPLACE_ME_APP_VERSION'
});
```

### Step 6 — Set user identity

```js
window.apptracker('setUserId', 'REPLACE_ME_USER_ID');
```

Update on: login, logout, account switch. Ask developer if anonymous/guest users have no ID. Ask developer to verify the value is not PII.

### Step 7 — Track page views

Page views are **not automatically captured**. Call explicitly:

```js
window.apptracker('trackPageView');
// optional custom title:
window.apptracker('trackPageView', { title: 'Page Title' });
```

---

# Page View Tracking Rules

Call `trackPageView` only after the page is visible and usable.

## SPA

SPA page view tracking decision:

1. Framework router present → call on route change
2. History/hash API used → call on navigation event
3. No navigation mechanism → call once after initial render

Do not assume a router exists. Never fire before UI is visible. Every logical screen change must trigger a page view.

### React SPA Without Router — fire once on mount

```js
useEffect(() => {
  window.apptracker('trackPageView');
}, []);
```

### React SPA With Router — fire on every route change

```js
useEffect(() => {
  window.apptracker('trackPageView');
}, [location]);
```

## MPA

```js
window.addEventListener('load', () => {
  window.apptracker('trackPageView');
});
```

---

# Custom Event Tracking — Optional

**Do not implement unless the developer explicitly requests it.**

```js
window.apptracker('trackCustomEvent', {
  name: 'checkout_started',
  data: { step: 1 }
});
```

Rules: stable event names, small payloads, no sensitive data.

---

# Form Tracking — Optional

**Form tracking is primarily an auto-collected feature controlled by Conviva remote configuration.** When enabled remotely, the SDK automatically captures the following events with no code changes:

- `conviva_form_start` — first user interaction with a form
- `conviva_form_field_blur` — field loses focus (sensitive/denylisted fields are redacted)
- `conviva_form_submit_attempt` — form `submit` event fires
- `conviva_form_validation_error` — field fails the browser's constraint validation

The `form_id` is derived from the form's `id` attribute, falling back to its `data-form-id` attribute when present.

## Manual form APIs — only when explicitly requested

**Do not add the manual form-tracking APIs unless the developer explicitly asks for them.** They are intended to report lifecycle outcomes the SDK cannot infer from the DOM (e.g. server-side submission outcomes, custom validation logic).

```js
// Form became visible to the user.
window.apptracker('trackFormView', 'REPLACE_ME_FORM_ID');

// Server-side submission accepted.
window.apptracker('trackFormSubmitSuccess', 'REPLACE_ME_FORM_ID');

// Server-side submission rejected.
window.apptracker('trackFormSubmitError', 'REPLACE_ME_FORM_ID', 'REPLACE_ME_ERROR_TYPE');

// Custom client-side validation failure (not detected by HTML5 constraint validation).
window.apptracker('trackFormValidationError', 'REPLACE_ME_FORM_ID', 'REPLACE_ME_FIELD_NAME', 'REPLACE_ME_ERROR_TYPE');
```

## Rules for manual form APIs

- The `formId` passed to manual APIs must match the form's DOM `id` (or `data-form-id`) so manual events correlate with auto-collected `conviva_form_*` events.
- `errorType` and `fieldName` must be short, stable, non-PII strings. Never pass user input, raw error messages, email addresses, names, tokens, or any sensitive data.
- Manual form APIs are no-ops when form tracking is disabled in remote configuration — do not rely on them as a substitute for remote enablement.
- Do not invent new form APIs. Only the four APIs listed above exist.

---

# Replay Integration

Verify the latest Replay version from GitHub Tags/Releases before generating the URL.

```html
<script src="https://sensor.conviva.com/replay/releases/vREPLACE_ME_VERSION/conviva-replay.umd.min.js"></script>
<script>
  ConvivaReplay.init('REPLACE_ME_CUSTOMER_KEY');
</script>
```

Replay must initialize **before** `convivaAppTracker`. Incorrect order will break replay.

---

# Client ID Synchronization

**Conviva Client ID is automatically stored in cookies during initialization**
Check with developer if he/she wants to disable the use of cookie named "Conviva_sdkConfig" by SDK. To disable, modify step 4: Initialize SDK to following:

```js
window.apptracker('convivaAppTracker', {
	appId: 'YOUR_APP_NAME_AS_STRING',
	convivaCustomerKey: 'CONVIVA_ACCOUNT_CUSTOMER_KEY',
	appVersion: '1.1.0',
	configs: {
		enableClIdInCookies: false
	},
});
```

## *Optional*
**Agents must NOT implement `getClientId` or `setClientId` unless the developer explicitly requests it.**

These APIs are for special use cases only, such as synchronizing a Conviva client ID across multiple domains or subdomains. These APIs must be used only when enableClidInCookies is set to false during initialization.
Do not add them to a standard single-domain integration.

When requested:

```js
window.apptracker('getClientId');          // call after initialization
window.apptracker('setClientId', clientId);  // call before initialization
```

---

# Content Security Policy

```
connect-src https://rc.conviva.com/ http://appgw.conviva.com/ https://rcg.conviva.com/
```

Replay may also require: `worker-src 'self' blob:;`

---

# Troubleshooting

- **No data** → check: script loaded, SDK initialized, placeholders replaced, CSP not blocking
- **Page views missing** → check: `trackPageView` called after UI is visible, route-change hook present
- **User ID missing** → check: `setUserId` called after login
- **Replay broken** → check: Replay script loaded and `ConvivaReplay.init()` called BEFORE `convivaAppTracker`

---

# Anti-Hallucination Rules

Do NOT use npm/ES module imports — this is a script-only SDK:

```js
// WRONG
import { convivaAppTracker } from "@convivainc/conviva-js-appanalytics";
```

Do NOT assume page tracking is automatic — always call `trackPageView` explicitly.

Do NOT invent config flags:

```js
// WRONG
window.apptracker('convivaAppTracker', { autoTrackPages: true });
```

Do NOT assume replay is built-in — it requires a separate CDN script.

Do NOT hardcode CDN versions — always verify from GitHub or use `vREPLACE_ME_VERSION`.

Do NOT add manual form-tracking APIs unless explicitly requested. Form tracking is primarily auto-collected via Conviva remote configuration:

```js
// WRONG — do not add proactively. Only the four documented form APIs exist:
// trackFormView, trackFormSubmitSuccess, trackFormSubmitError, trackFormValidationError.
window.apptracker('trackFormSubmit', 'signup-form');     // invented API
window.apptracker('trackFormFieldFocus', 'signup-form'); // invented API
```

Do NOT pass PII or raw error messages to `trackFormSubmitError` / `trackFormValidationError` — only short, stable, non-PII identifiers.

---

# Mandatory Prompt Template for AI Agents

Before generating final integration code, agents must request:

```
To integrate Conviva App Analytics I need:

1. Conviva Customer Key
2. appId
3. appVersion
4. Where should the scripts be added (HTML file or template)?
5. Where does user identity come from?
6. Should session replay be enabled?
```
