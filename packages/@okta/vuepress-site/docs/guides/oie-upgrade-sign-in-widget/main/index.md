---
title: Upgrade the Okta Sign-In Widget
---

<ApiLifecycle access="ie" />

This guide explains how to upgrade the Okta Sign-In Widget. The upgrade depends on whether your deployment model is redirect authentication or embedded authentication.

---

**Learning outcome**

Update your Sign-In Widget based on your user deployment model.

#### What you need

* An [Identity Engine-upgraded Okta org](/docs/guides/oie-upgrade-overview/)
* An existing app that uses the Sign-In Widget
* [Latest available Sign-In Widget release](https://github.com/okta/okta-signin-widget/releases)

---

## About the Okta Sign-In Widget

The Sign-In Widget is a JavaScript library that provides a full-featured and customizable sign-in experience. You can use the Sign-In Widget to authenticate web and mobile app users.

Use the Sign-In Widget on the default sign-in page to do the following:

* Start an Okta SSO session
* Set the Okta [session cookie](/docs/guides/session-cookie/) in the web browser
* Perform a complete [OpenID Connect (OIDC)](/docs/concepts/oauth-openid/) flow
* Integrate with [external Identity Providers](/docs/concepts/identity-providers/)

>**Note:** Some browsers block third-party cookies by default, which disrupts Okta functionality in certain flows. See [Mitigate the impact of third-party cookie deprecation](https://help.okta.com/okta_help.htm?type=oie&id=ext-third-party-cookies).

This article teaches you how to upgrade the Sign-In Widget when it’s used in any of the following ways:

* Redirect authentication (default): Okta provides a sign-in page that's available at your [org's URL](/docs/concepts/okta-organizations/). By default, a user who signs in on this page is redirected to the Okta End-User Dashboard.
* Redirect authentication (customizable): Okta provides a sign-in page that you can customize. You can then make it available as a custom domain under your company's top-level domain.
* Embedded authentication (self-hosted): You can embed the Sign-In Widget directly into your app.

After you've completed the Sign-In Widget upgrade, review the [Okta Identity Engine overview](/docs/concepts/oie-intro/) to take advantage of the new features in Identity Engine.

## Best practice for Sign-In Widget maintenance

For best practices, keep your [Sign-In Widget](https://github.com/okta/okta-signin-widget/releases) up to date. This maintenance is essential so that you can use the latest Identity Engine features and benefit from ongoing improvements to the codebase.

The specific steps to upgrade your Sign-In Widget depend on your [user authentication deployment model](/docs/concepts/redirect-vs-embedded/), which can be one of the following:

* [Redirect authentication](#upgrade-process-for-a-redirect-sign-in-flow): Okta-hosted with no custom URL domain
* [Redirect authentication](#upgrade-process-for-a-redirect-sign-in-flow): Okta-hosted with custom URL domain
* [Embedded authentication](#upgrade-process-for-an-embedded-widget): Self-hosted. The embedded Sign-In Widget is able to perform the OIDC flow and return OAuth tokens directly within the app.

## Upgrade process for a redirect sign-in flow

The Sign-In Widget upgrade for a redirect sign-in flow depends on whether you configured a custom URL domain:

* [Custom domain](/docs/guides/custom-url-domain/) and customizations outside of simple branding styles aren't configured: the Sign-In Widget is automatically upgraded to the latest version when it's loaded from the content delivery network (CDN).

* [Custom domain](/docs/guides/custom-url-domain/) and other customizations are configured: Admins must update the Sign-In Widget version in the Admin Console.

To update the Sign-In Widget:

1. In the Admin Console, go to **Customizations** > **Brands**, and then select the brand you want.

2. On the **Pages** tab, click **Configure** in the **Sign-in page** section. Go to the **Settings** tab.

    - In the **Sign-In Widget Version** section, check that the **Version** is the highest version available. The Sign-In Widget is always the latest version if you're not using a custom URL domain.
    - If you're using the [custom domain feature](/docs/guides/custom-url-domain/) and the version isn't correct, you can pin the Sign-In Widget's version. Click **Edit** in the **Sign-In Widget Version** section, and then select the **Version** field.
    - If you enable multibrand customization, you can't customize the Sign-In Widget for the Okta default brand. See [Branding](https://help.okta.com/okta_help.htm?type=oie&id=csh-branding).
    - If you enable the third generation of the Sign-In Widget, you can pin the version. See [Pin a specific third generation Sign-In Widget](https://help.okta.com/okta_help.htm?type=oie&id=ext-pin-siw3-ver).

3. Click **Save to draft**.

4. Go to the **Page Design** tab to preview the draft of your sign-in page.

5. Verify the CSS and localization changes that you made are reflected in the new version.

  > **Note:** The third generation of the Okta Sign-In Widget doesn’t guarantee the stability of CSS selectors. Instead, customization in the third generation gets better support through branding. See [Style the Sign-In Widget (third generation)](/docs/guides/custom-widget-gen3/).

## Upgrade process for an embedded Sign-In Widget

Upgrade your embedded Sign-In Widget by referencing the Okta CDN in your sign-in page. Use the [latest version](https://github.com/okta/okta-signin-widget/releases/) of the Sign-In Widget: -=OKTA_REPLACE_WITH_WIDGET_VERSION=-

```html
<script src="https://global.oktacdn.com/okta-signin-widget/-=OKTA_REPLACE_WITH_WIDGET_VERSION=-/js/okta-sign-in.min.js" type="text/javascript"></script>
<link href="https://global.oktacdn.com/okta-signin-widget/-=OKTA_REPLACE_WITH_WIDGET_VERSION=-/css/okta-sign-in.min.css" type="text/css" rel="stylesheet"/>
```

See also [Using the Okta CDN](https://github.com/okta/okta-signin-widget#using-the-okta-cdn).

In addition to version upgrade, you need to adjust your Sign-In Widget configuration for new or deprecated settings. See the next section.

> **Note:** The third generation of the Okta Sign-In Widget doesn’t support embedded authentication. See [Sign-In Widget, third generation](https://help.okta.com/okta_help.htm?type=oie&id=ext-compare-siw).

> **Note:** If you're currently using the Sign-In Widget major version 4 or earlier, consult the [Okta Sign-In Widget migration guide](https://github.com/okta/okta-signin-widget/blob/master/MIGRATING.md).

## Changes to Sign-In Widget configuration for Identity Engine

For Identity Engine, the Sign-In Widget is configured differently. You can remove some specific objects that were previously in the Sign-In Widget configuration from the JavaScript, as described in the following sections.

### Interaction Code flow

Identity Engine uses the [Interaction Code grant type](/docs/concepts/interaction-code) to manage user interactions, such as registration or multifactor authentication. For embedded Sign-In Widget (self-hosted) deployments, the Interaction Code flow is the only supported authentication flow with Identity Engine.

In Okta Sign-In Widget version 7+, Identity Engine is enabled by default. If you’re using an earlier version than 7, you must explicitly enable Identity Engine features by setting `useInteractionCodeFlow: true` in the `config` object:

```JavaScript
var config = {
  issuer: '{{authServerUri}}',
  clientId: '{{oidcAppClientId}}',
  redirectUri: '{{oidcAppRedirectUri}}',
  useInteractionCodeFlow: true
}
```

If you’re using version 7+ and you want to use Okta Classic Engine rather than Identity Engine, specify `useClassicEngine: true` in your `config` object:

```JavaScript
var config = {
  issuer: '{{authServerUri}}',
  clientId: '{{oidcAppClientId}}',
  redirectUri: '{{oidcAppRedirectUri}}',
  useClassicEngine: true
}
```

### Registration

You no longer need the [registration](https://github.com/okta/okta-signin-widget#registration) JavaScript objects in the Sign-In Widget. You can add registration into your app by configuring your Okta admin settings for [profile enrollment](https://help.okta.com/okta_help.htm?type=oie&id=ext-create-profile-enrollment). This process allows users to self-register into your app.

Remove the `registration` object and `features.registration` property that are shown in the following snippet:

```JavaScript
var signIn = new OktaSignIn({
  baseUrl: 'https://{yourOktaDomain}',
  registration: {
    preSubmit: (postData, onSuccess, onFailure) => {
        // handle preSubmit callback
        onSuccess(postData);
    },
    postSubmit: (response, onSuccess, onFailure) => {
        // handle postsubmit callback
        onSuccess(response);
    }
  },
  features: {
    // Used to enable registration feature on the Sign-In Widget
    // https://github.com/okta/okta-signin-widget#feature-flags
    registration: true
  }
})
```

> **Note**: The `parseSchema` method isn't included in the above `registration` object because the ability to include more schemas is no longer supported.

### IdP Discovery

IdP Discovery enables you to route users to different third-party IdPs that are connected to your Okta org. Users can federate back into the primary org after authenticating at the IdP. This feature still functions, but you no longer need to enable the link for users to initialize the route. Instead, you can configure a routing rule with the app context.

Remove the `idpDiscovery` property:

```JavaScript
features: {
  idpDiscovery: true
}
```

### OpenID Connect/social authentication

You no longer require the `idps` JavaScript object in the Sign-In Widget and can remove it.

```JavaScript
idps: [
  { type: 'GOOGLE', id: '0oagjkh' },
  { type: 'FACEBOOK', id: '0oagjkh' },
    ...
]
```

This is now optional as the Sign-In Widget automatically includes IdPs based on Identity Engine routing rules.

### Bootstrap from a recovery token

If you're initializing Sign-In Widget with a recovery token, the `recoveryToken` setting appears:

```JavaScript
recoveryToken: 'x0whAcR02i0leKtWMZVc'
```

The recovery token is dynamic and is automatically passed into the initialization of the Sign-In Widget. A value in the `recoveryToken` setting currently doesn't impact Sign-In Widget function, though the setting takes effect in the future.

### Okta dashboard or custom dashboard sign-in flow

For an Okta dashboard sign-in flow, you no longer need to do the following:

- Configure a redirect to the Okta Identity Cloud
- Create an Okta session
- Open a URL specified in the Sign-In Widget

Remove the redirect configuration (`setCookieAndRedirect()`) line shown in the following snippet:

```JavaScript
function success(res) {
  if (res.status === 'SUCCESS') {
    res.session.setCookieAndRedirect('https://{yourOktaDomain}/app/UserHome');
  }
};
```

### Feature flags

The only feature that is supported when you upgrade Sign-In Widget is `features.hideSignOutLinkInMFA`, which hides the sign-out link for an MFA challenge.

The following specific features are no longer supported, and you can't configure them in the Sign-In Widget. Remove them from `features` in the JSON code:

* `features.rememberMe`: Displayed the "Remember me" function when a user signs in. See the [Organization Settings](https://help.okta.com/okta_help.htm?id=ext_Security_General) section in the Okta product documentation to enable this feature.

* `features.autoPush`: Displayed a checkbox to enable the "Send push automatically" function in the MFA challenge flow. It may be added to the policy configuration in a future release.

* `features.smsRecovery`: Recovered the password for users with a configured mobile phone number by using an SMS message. See [password recovery policy](https://help.okta.com/okta_help.htm?id=ext-add-self-service-password-reset) to enable and configure a possession (for example, a phone) authenticator.

* `features.callRecovery`: Recovered the password for users with a configured mobile phone number by using a voice call. See [password recovery policy](https://help.okta.com/okta_help.htm?id=ext-add-self-service-password-reset) to enable and configure a possession (for example, a phone) authenticator.

* `features.webauthn`: Prevented Sign-In Widget from invoking the legacy Windows Hello factor. See [sign-on policy](https://help.okta.com/okta_help.htm?type=oie&id=ext-about-osop) to enable and configure a possession authenticator.

* `features.selfServiceUnlock`: Displayed the "Unlock Account" link so that users could unlock their accounts. See [self-service account recovery](https://help.okta.com/okta_help.htm?type=oie&id=ext-config-sspr) to enable this feature.

* `features.multiOptionalFactorEnroll`: Allowed users to enroll in multiple optional factors before they finished the authentication flow. The [MFA enrollment and rules](https://help.okta.com/okta_help.htm?type=oie&id=ext-about-mfa-enrol-policies) are evaluated by default when applicable.

* `features.registration`: Displayed the registration section in the primary auth page. The [user profile policies](https://help.okta.com/okta_help.htm?type=oie&id=ext-create-profile-enrollment) are evaluated by default when applicable.

* `features.idpDiscovery`: Enabled IdP Discovery when the user signed in. The [Identity Provider routing rules](https://help.okta.com/okta_help.htm?id=ext_Identity_Provider_Discovery) are evaluated by default when applicable.

* `features.showPasswordToggleOnSignInPage`: Provided end users with the ability to view their password on the Okta sign-in page. This allows users to check their password entry before clicking **Sign In**. This feature also prevented an account lockout due to exceeding their org's permitted number of failed sign-in attempts.

* `features.scrollOnError`: Scrolled errors into view. Errors appear inside the Sign-In Widget.

* `features.skipIdpFactorVerificationBtn`: Provided an automatic redirect to the selected Identity Provider when selected from the list of factors. It's no longer needed since the optional authenticators can skip by default.

### i18n properties

After you upgrade your org to Identity Engine, you can override existing text strings in the interface. Use Identity Engine i18n strings so that you can create localized Sign-In Widgets. See [Updates to Sign-In Widget i18n properties](/docs/guides/oie-upgrade-sign-in-widget-i18n).

## Changes to Sign-In Widget customization for Identity Engine

### Help title link and Need help signing in string

The following customizations aren't supported:

* **Okta-hosted widget:** The **Help title** link in the **Customized Help Links** section of the Customization page has been removed and isn't supported.
* **Self-hosted widget:** The **Need help signing in** string has been removed and isn't supported.

See [Okta Sign-In Widget: Help links](https://github.com/okta/okta-signin-widget/#help-links).

### The `processCreds` hook

Developers can't subscribe to the `processCreds` hook in the Sign-In Widget.

### Registration inline hooks

Existing registration inline hooks may experience compatibility issues after migrating to Identity Engine due to changes in the Okta registration inline hook request. Your app may require code updates to consume the new request format properly.

In the Admin Console, where you enable a registration inline hook has changed. Enable the hook from the Profile Enrollment Rules page (**Security** > **User Profile Policies**) instead of the Self-Service Registration page (**Self-service Directory** > **Self-Service Registration**). The creation of the registration inline hook remains the same. You can use either the Admin Console or Inline Hook Management APIs.

See [Registration hooks API reference](https://developer.okta.com/docs/api/openapi/okta-management/management/tag/InlineHook/#tag/InlineHook/operation/create-registration-hook) and [Configure user profile policies](https://help.okta.com/okta_help.htm?type=oie&id=ext-create-profile-enrollment).

### Security image

The ability for end users to specify a security image when they first register for an account isn't supported with Identity Engine. Also, existing users who may have already registered a security image can't see that image when they sign in.

## See also

[Deprecated JavaScript methods in the Sign-In Widget](/docs/guides/oie-upgrade-sign-in-widget-deprecated-methods/main/)
