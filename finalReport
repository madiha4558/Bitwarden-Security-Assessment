# Authentication and Session Management Security Review of Bitwarden

**Project:** Security Assessment of Authentication and Credential Protection in Bitwarden Password Manager  
**Reviewer:** Krishma Kuinkel  
**Course:** CSCE 4565 – Secure Software Development  
**Repository:** [Bitwarden Server](https://github.com/bitwarden/server)

## 1. Introduction

As part of the security assessment of Bitwarden, I reviewed selected server-side authentication and session-management components. My analysis focused on how Bitwarden handles user authentication, refresh-token expiration, session lifetime configuration, and logout-related settings.

The purpose of this review was to understand how these controls help protect user accounts and reduce the risk of unauthorized access. I used source-code review and examined existing unit tests in the official Bitwarden Server GitHub repository.

## 2. Authentication Review: Prelogin Process

**File:** `src/Identity/Controllers/AccountsController.cs`  
**Method:** `MakePasswordPreloginCall`  
**Lines:** 237–245  
**Source commit:** `4e4adf99e96127e3955bbd7a1e733af75106ed21`

In my midterm review, I examined how Bitwarden handles key-derivation information during the prelogin process.

When a user submits an email address, the server attempts to retrieve the user's key-derivation information using:

`_userRepository.GetKdfInformationByEmailAsync(model.Email)`

If the user exists, the server retrieves the associated key-derivation information. If the information is not found, the code uses `GetDefaultKdf(model.Email)` to generate default information.

This fallback mechanism is relevant to account security because it may reduce the risk of user enumeration. User enumeration occurs when an attacker attempts to determine which email addresses are registered with an application.

**Result:** I identified a fallback mechanism that may make it more difficult to identify registered accounts through prelogin responses. However, I did not perform response-comparison or timing tests, so I cannot confirm that user enumeration is completely prevented.

## 3. Refresh-Token Expiration Configuration

**File:** `src/Core/Settings/GlobalSettings.cs`

I examined the refresh-token lifetime settings in Bitwarden's global configuration.

The code contains the following important configuration properties:

- `SlidingRefreshTokenLifetimeSeconds`
- `AbsoluteRefreshTokenLifetimeSeconds`
- `ApplyAbsoluteExpirationOnRefreshToken`

The code comments explain two expiration approaches.

**Sliding expiration:** When a refresh token is used before its sliding expiration window ends, its lifetime can be extended by another configured interval.

**Absolute expiration:** A refresh token has a maximum lifetime and cannot be extended beyond its absolute expiration time.

The configuration also indicates that setting the sliding refresh-token lifetime to zero makes sliding mode invalid.

These settings are important because they limit how long refresh tokens can remain usable. A maximum lifetime can help reduce the risk of long-term unauthorized access if a refresh token is compromised.

**Result:** I confirmed that Bitwarden's source code includes configurable sliding and absolute refresh-token lifetime settings. These provide different ways to control how long refresh tokens remain valid.

## 4. Refresh-Token Configuration in the Identity Server

**File:** `src/Identity/IdentityServer/ApiClientConfiguration.cs`  
**Relevant properties:** Lines 7–11

I also reviewed the API client configuration, which contains the following properties:

- `AccessTokenLifetimeSeconds`
- `ApplyAbsoluteExpirationOnRefreshToken`
- `SlidingRefreshTokenLifetimeSecondsOverride`
- `AbsoluteRefreshTokenLifetimeSeconds`

These properties allow the identity server configuration to receive access-token and refresh-token lifetime settings, including optional client-specific overrides.

**File:** `src/Identity/IdentityServer/ApiClient.cs`  
**Relevant lines:** 13–21

The `ApiClient` constructor uses these configuration values to determine refresh-token expiration behavior.

The code sets `RefreshTokenExpiration` based on `ApplyAbsoluteExpirationOnRefreshToken`. When the setting is enabled, the expiration mode is absolute; otherwise, it uses sliding expiration.

It also assigns the sliding refresh-token lifetime using the configured override when available, or a default value based on the configured refresh-token lifetime.

The constructor also assigns the access-token lifetime from the configuration.

**Result:** My review showed that Bitwarden connects its global and client-specific lifetime settings to the identity server's client configuration. This demonstrates how the expiration policy is applied when identity-server clients are configured.

## 5. Verification Through Unit Tests

**File:** `test/Identity.Test/IdentityServer/StaticClientStoreTests.cs`

I examined existing unit tests to understand how Bitwarden verifies its refresh-token expiration configuration.

One relevant test is:

`RefreshTokenExpiration_DerivedFromApplyAbsoluteFlag`

**Lines:** 116–129

This test checks two different configurations:

1. When `ApplyAbsoluteExpirationOnRefreshToken` is false, the expected expiration mode is `Sliding`.
2. When the setting is true, the expected expiration mode is `Absolute`.

The test then verifies that the expected expiration policy is applied to the interactive clients.

I also examined:

`AbsoluteRefreshTokenLifetime_UsesGlobalOverride_WhenSet`

**Lines:** 106–114

This test sets the global absolute refresh-token lifetime to `99999` and verifies that the configured client receives that value.

**Result:** These unit tests provide evidence that Bitwarden checks the relationship between its configuration settings and the resulting refresh-token expiration policies. They verify configuration behavior, rather than testing every aspect of token expiration during actual user sessions.

## 6. Logout-Related Configuration

**File:** `test/Identity.Test/IdentityServer/StaticClientStoreTests.cs`  
**Relevant methods:**
- `WebAndBrowser_RedirectAndCorsUris_UseVaultUri`
- `Desktop_RedirectUris_IncludeSchemeCallbackAndLocalhostPorts`

I also examined the `PostLogoutRedirectUris` property.

The tests verify that web and browser clients use the configured vault URI as their post-logout redirect destination. Another test verifies that desktop clients include the `bitwarden://logged-out` redirect destination.

This setting controls where a client can redirect after logout. Restricting permitted redirect destinations is relevant to security because it helps prevent clients from redirecting users to arbitrary destinations.

**Result:** I found unit-test coverage for post-logout redirect configuration. However, these tests do not establish whether refresh tokens are invalidated when a user logs out.

## 7. Token Revocation Endpoint Configuration

**File:** `src/Identity/Utilities/ServiceCollectionExtensions.cs`  
**Relevant line:** 45

During my investigation, I searched the Bitwarden Server repository for `RevocationEndpoint`.

I found the following configuration:

`options.Endpoints.EnableTokenRevocationEndpoint = false;`

This indicates that the IdentityServer token revocation endpoint is disabled in the configuration I inspected.

This is an important observation because token revocation endpoints can provide a mechanism for clients to request that tokens be invalidated before their normal expiration.

However, this finding does not prove that Bitwarden has no other token invalidation mechanisms. Additional investigation would be necessary to understand how refresh tokens are stored, removed, and invalidated during logout or other security events.

**Result:** I identified a disabled token revocation endpoint in the reviewed configuration. I did not establish whether other internal mechanisms provide token revocation or session termination.

## 8. Overall Security Analysis and Findings

Based on my source-code review, I identified several security-related controls in Bitwarden's authentication and session-management implementation.

| Area reviewed | Observation | Result |
|---|---|---|
| Prelogin authentication | Uses default KDF information when user information is not found | Potential protection against user enumeration |
| Sliding expiration | Refresh-token lifetime can be extended when used | Configurable session lifetime |
| Absolute expiration | Refresh tokens have a maximum lifetime | Limits token lifetime |
| Client configuration | Applies expiration settings to identity-server clients | Configuration behavior identified |
| Unit tests | Tests sliding and absolute expiration settings | Configuration verification found |
| Logout redirects | Tests permitted post-logout destinations | Redirect configuration verified |
| Token revocation | Revocation endpoint is disabled in reviewed configuration | Further investigation required |

These observations show that Bitwarden includes configurable refresh-token expiration mechanisms and unit tests that verify the selected expiration policies.

One important limitation is that source-code configuration and unit tests alone cannot establish how every session behaves during actual use. Additional runtime testing would be needed to verify expired-token rejection, refresh-token invalidation, and logout behavior.

No confirmed security vulnerability was identified during my review.

## 9. Limitations

My assessment was limited to selected authentication and identity-server source files. I did not examine the complete Bitwarden authentication architecture or perform penetration testing.

The review also did not include:

- Runtime testing of expired refresh tokens.
- Testing whether expired tokens are rejected by the server.
- Testing whether logout invalidates existing refresh tokens.
- Comparing prelogin responses and timing for existing and nonexistent accounts.
- Verifying all possible token revocation and session termination mechanisms.

Therefore, the observations in this report should be considered source-code review findings rather than confirmed security vulnerabilities or a complete assessment of Bitwarden's session-management security.

## 10. Conclusion

Through this assessment, I learned how authentication and session-management controls contribute to protecting password-manager accounts. My review identified a prelogin fallback mechanism, configurable refresh-token expiration policies, identity-server client configuration, and unit tests covering sliding and absolute expiration.

I also examined logout-related redirect settings and identified that the token revocation endpoint is disabled in the reviewed configuration. This helped me understand the difference between controlling how long a token remains valid and explicitly revoking a token.

Overall, the source-code review provided useful evidence of Bitwarden's authentication and session-management design. It also showed me the importance of examining related configuration, implementation, and unit tests together rather than drawing conclusions from a single method or property.

No confirmed vulnerability was found in the selected code. Further runtime testing and examination of token invalidation mechanisms would be necessary to reach stronger conclusions about the complete session-management process.
