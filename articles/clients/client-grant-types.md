---
description: Using the Grant Types property on Clients
toc: true
---
# Client Grant Types

Auth0 provides many authentication and authorization flows that suit your needs, and depending on your use case, you may wish to limit the use of certain grant types for a particular Client. Auth0 includes a `grant_types` property on each Client for this purpose.

## Grant Types

::: note
Please refer to [Which OAuth 2.0 flow should I use?](https://auth0.com/docs/api-auth/which-oauth-flow-to-use) for help selecting the appropriate non-legacy grant type.
:::

The following is a list of grant types valid for Auth0 Clients. There are three possible types of authorization flows:

* Auth0 extension;
* Auth0 legacy;
* Specification-conformant (such as OpenID Connect);

The following is a list of Auth0 extension/OIDC specification-conformant `grant_types` that currently exist for use in Auth0:

* [Implicit Grant](/api-auth/grant/implicit): `implicit`
* [Authorization Code Grant](/api-auth/grant/authorization-code): `authorization_code`
* [Client Credentials Grant](/api-auth/grant/client-credentials): `client_credentials`
* [Resource Owner Password Grant](/api-auth/grant/password): `password`
* [Use a refresh token](/tokens/refresh-token/current#use-a-refresh-token): `refresh_token`
* [Use an extension grant similar to the Resource Owner Password Grant that includes the ability to indicate a specific realm](/api-auth/grant/password#realm-support): `http://auth0.com/oauth/grant-type/password-realm`
* [Multifactor Authentication OOB Grant Request](/api-auth/tutorials/multifactor-resource-owner-password#mfa-oob-grant-request): `http://auth0.com/oauth/grant-type/mfa-oob`
* [Multifactor Authentication OTP Grant Request](/api-auth/tutorials/multifactor-resource-owner-password#mfa-otp-grant-request): `http://auth0.com/oauth/grant-type/mfa-otp`
* [Multifactor Authentication Recovery Grant Request](/api-auth/tutorials/multifactor-resource-owner-password#mfa-recovery-grant-request): `http://auth0.com/oauth/grant-type/mfa-recovery-code`

The following is a list of legacy `grant_types`:

* `http://auth0.com/oauth/legacy/grant-type/ro`
* `http://auth0.com/oauth/legacy/grant-type/ro/jwt-bearer`
* `http://auth0.com/oauth/legacy/grant-type/delegation/refresh_token`
* `http://auth0.com/oauth/legacy/grant-type/delegation/id_token`
* `http://auth0.com/oauth/legacy/grant-type/access_token`

## Edit the `grant_types` Property

To edit the `grant_types` property for your Auth0 Client, you'll need to make the appropriate call to the [Update a Client endpoint](/api/management/v2#!/Clients/patch_clients_by_id) of the [Management API](/api/management/v2).

::: warning Legacy Grant Types
As of 8 June 2017, new Auth0 customers **cannot** add *any* of the legacy grant types to their Clients. Only customers as of 8 June 2017 can add legacy grant types to their existing Clients.
:::

Auth0 requires the `grant_types` property to use the various flows. Attempting to use *any* flow with a Client lacking the appropriate `grant_types` for that flow (or with the field empty) will result in the following error:

```text
Grant type `grant_type` not allowed for the client.
```

### Existing Clients

To avoid changes in functionality for current Auth0 customers, we will populate the `grant_types` property for all existing Clients as of 8 June 2017 with **all** Auth0 legacy, Auth0 extension, and specification-conformant grant types.

### New Clients

Depending on whether a newly-created Client is **public** or **confidential**, the Client will have varying access to grant types.

### Public Clients

Public Clients, indicated by the `token_endpoint_auth_method` flag set to `none`, are those created in the Dashboard for Native and Single Page Applications. 

::: panel Token Endpoint Authentication Method
You can update the `token_endpoint_auth_method` flag using the Management API's [Update a Client endpoint](/api/management/v2#!/Clients/patch_clients_by_id).

The `Token Endpoint Authentication Method` defines the token endpoint's requested authentication method, and the accepted values are:

* `None`, for a public client without a client secret
* `Post`, for a client using HTTP POST parameters
* `Basic`, for a client using HTTP Basic parameters 
:::

By default, Public Clients are created with the following `grant_types`:

* `implicit`;
* `authorization_code`;
* `refresh_token`.

::: note
Public clients **cannot** utilize the `client_credentials` grant type. To add this grant type to a Client, set the `token_endpoint_auth_method` to `client_secret_post` or `client_secret_basic`. Either of these will indicate the Client is confidential, not public.
:::

### Confidential Clients

Confidential Clients, indicated by the `token_endpoint_auth_method` flag set to anything *except* `none`, are those created in the Dashboard for Regular Web Applications or Non-Interactive Clients. Additionally, any Client where `token_endpoint_auth_method` is unspecified is confidential. By default, Confidential Clients are created with the following `grant_types`:


* `implicit`;
* `authorization_code`;
* `refresh_token`;
* `client_credentials`.

### Trusted First-Party Clients

Trusted first-party clients can additionally use the following `grant_types`:

* `password`
* `refresh_token`
* `http://auth0.com/oauth/grant-type/password-realm`
* `http://auth0.com/oauth/grant-type/mfa-oob`
* `http://auth0.com/oauth/grant-type/mfa-otp`
* `http://auth0.com/oauth/grant-type/mfa-recovery-code`

## Secure Alternatives to the Legacy Grant Types

<!-- markdownlint-disable MD033 -->

<table class="table">
  <tr>
    <th>Legacy Grant Type</th>
    <th>Alternative</th>
  </tr>
  <tr>
    <td>Resource Owner Password Credentials flow (http://auth0.com/oauth/legacy/grant-type/ro)</td>
    <td>Use the <a href="/api/authentication#authorization-code">`/oauth/token` endpoint</a> with a grant type of `password`. See <a href="/api-auth/tutorials/adoption/password">Resource Owner Password Credentials Exchange</a> and <a href="/api-auth/tutorials/password-grant">Executing the Resource Owner Password Grant</a> for additional information.</td>
  </tr>
  <tr>
    <td>http://auth0.com/oauth/legacy/grant-type/ro/jwt-bearer</td>
    <td>This feature is disabled by default. If you would like this feature enabled, please contact support to discuss your use case and prevent the possibility of introducing security vulnerabilities.</td>
  </tr>
  <tr>
    <td>http://auth0.com/oauth/legacy/grant-type/delegation/refresh_token</td>
    <td>Use the `oauth/token` endpoint to <a href="/api-auth/tutorials/adoption/refresh-tokens">obtain refresh tokens</a>. See <a href="/api-auth/tutorials/adoption/refresh-tokens">OIDC-conformant refresh tokens</a> for additional information.</td>
  </tr>
  <tr>
    <td>http://auth0.com/oauth/legacy/grant-type/delegation/id_token</td>
    <td>This feature is disabled by default. If you would like this feature enabled, please contact support to discuss your use case and prevent the possibility of introducing security vulnerabilities.</td>
  </tr>
  <tr>
    <td>http://auth0.com/oauth/legacy/grant-type/access_token</td>
    <td>Use browser-based social authentication.</td>
  </tr>
</table>

<!-- markdownlint-enable MD033 -->

::: note
Those implementing Passwordless Authentication should use hosted login pages instead of the `oauth/ro` endpoint.
:::

## Enable a Legacy Grant Type

::: warning
Only Auth0 customers as of 8 June 2017 may enable a legacy grant type for existing Clients.
:::

To enable a legacy grant type, you will need to make the appropriate `PATCH` call to the [Update a Client endpoint](/api/management/v2#!/Clients/patch_clients_by_id) of the [Management API](/api/management/v2).

::: note
If you are a new customer and you are interested in using a legacy flow, please [contact Support for assistance](https://support.auth0.com/).
:::
