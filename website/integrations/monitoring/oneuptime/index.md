---
title: Integrate with OneUptime
sidebar_label: OneUptime
support_level: community
---

## What is OneUptime

> OneUptime is an open-source observability and incident management platform.
> It provides infrastructure monitoring, incident management, status pages,
> and APM — unified and self-hostable.
>
> -- https://oneuptime.com/

## Preparation

The following placeholders are used in this guide:

- `authentik.company` is the FQDN of the authentik installation.
- `oneuptime.company` is the FQDN of your OneUptime installation. If you are
  using the OneUptime cloud service, this will be `oneuptime.com`.

:::info
This documentation lists only the settings that you need to change from their
default values. Be aware that any changes other than those explicitly mentioned
in this guide could cause issues accessing your application.
:::

:::warning
Configuring SSO with OneUptime requires a Scale plan or higher on the OneUptime
cloud service. If you are self-hosting OneUptime, SSO is available on all
instances at no cost.
:::

## authentik configuration

### Step 1: Download the public certificate from authentik

1. Log in to authentik as an administrator and open the authentik Admin
   interface.
2. Navigate to **System** > **Certificates**.
3. Select the certificate you wish to use for signing.
4. Click **Download** to save the public certificate to your machine.


## OneUptime configuration

### Step 2: Create the SSO configuration in OneUptime

1. Log in to OneUptime as an administrator.
2. Navigate to **Settings** > **SSO / Single Sign-On**.
3. Click **Add SSO Provider** and configure the following required settings:
    - **Issuer**: `https://authentik/`
    - **Identity Provider SSO URL**:
      `https://authentik.company/application/saml/<application_slug>/sso/binding/redirect/`
    - **Identity Provider x509 Certificate**: paste the certificate text noted
      in Step 1 
4. Click **Save** to create the SSO configuration.

### Step 3: Retrieve the ACS URL from OneUptime

1. After saving, locate the SSO configuration you just created in the
   **SSO / Single Sign-On** settings page.
2. Click **View SSO Config** on the configuration.
3. Take note of the **ACS URL** displayed. You will need this value in the
   next step.

## authentik configuration

### Step 4: Create an application and provider in authentik

1. Log in to authentik as an administrator and open the authentik Admin
   interface.
2. Navigate to **Applications** > **Applications** and click **Create with
   Provider** to create an application and provider pair. (Alternatively you
   can first create a provider separately, then create the application and
   connect it with the provider.)
    - **Application**: provide a descriptive name, an optional group for the
      type of application, the policy engine mode, and optional UI settings.
    - **Choose a Provider type**: select **SAML Provider** as the provider
      type.
    - **Configure the Provider**: provide a name (or accept the auto-provided
      name), the authorization flow to use for this provider, and the following
      required configurations:
        - Set the **ACS URL** to the value retrieved from OneUptime in Step 3.
        - Set the **Issuer** to `https://authentik/`.
        - Under **Advanced protocol settings**, set the **Signing Certificate**
          to the same certificate you downloaded in Step 1.
    - **Configure Bindings** _(optional)_: you can create a
      [binding](/docs/add-secure-apps/bindings-overview/) (policy, group, or
      user) to manage the listing and access to applications on a user's
      **My applications** page.
3. Click **Submit** to save the new application and provider.

## Configuration verification

To verify that authentik is configured correctly with OneUptime, open a new
private or incognito browser window and navigate to your OneUptime login page.
Click **Sign in with your Identity Provider**, enter your email address, and
confirm that you are redirected to authentik for authentication and then
redirected back into OneUptime.

## Resources

- [OneUptime Documentation](https://oneuptime.com/docs)
- [authentik SAML Provider Documentation](https://docs.goauthentik.io/docs/add-secure-apps/providers/saml/)