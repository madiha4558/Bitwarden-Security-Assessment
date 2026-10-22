# Bitwarden Security Architecture
Author: Madiha Masood Syeda
Course: CSCE 4565

## 1. Assessment Scope
This assessment examines the Bitwarden server repository.
The review focuses on backend architecture and authentication.
Client-side encryption is discussed using official documentation.

## 2. Source Information
Original repository: https://github.com/bitwarden/server
Reviewed commit ID: 4e4adf99e96127e3955bbd7a1e733af75106ed21
Review date:  October 22, 2026

## 3. Main Components
Bitwarden uses client applications and backend services to manage
and synchronize password vaults.

- Client applications: The web, desktop, mobile, and browser
  applications let users access their vaults. Vault encryption
  and decryption happen on the user's device.

- Authentication service: Checks login information and handles
  authentication before allowing access to protected services.

- Backend API: Handles requests from client applications,
  including requests to retrieve and synchronize encrypted
  vault information.

- Database: Stores encrypted vault information and account-related
  records. Encrypted vault contents are different from administrative
  information, such as account email addresses.

The server repository contains backend components. Client-side
encryption is described here using official documentation rather
than verified through this server-only review.

Sources:
- https://github.com/bitwarden/server
- https://bitwarden.com/help/bitwarden-security-white-paper/

## 4. Security Architecture
Bitwarden documents several layers of protection:

- Local vault encryption: Vault contents are encrypted on the
  user's device before being uploaded. Decryption also happens
  on the client.

- Zero knowledge design: Bitwarden's documented design prevents
  the service from accessing plaintext vault contents. This does
  not mean all account information is encrypted in the same way.

- Key derivation: PBKDF2 SHA-256 or Argon2id helps derive
  cryptographic keys from the master password.

- Secure communication: TLS protects communication between
  client applications and the server.

These protections address different risks. Vault encryption
protects stored contents, while TLS protects network traffic.
Authentication controls access to backend services.

These statements describe the documented architecture.
Our server code review has not independently verified all
client-side cryptographic protections.

Source:
https://bitwarden.com/help/bitwarden-security-white-paper/

## 5. Data Flow
The following describes a simplified vault synchronization flow:

1. The user signs in through a Bitwarden client application.
   The backend checks authentication information.

2. The client encrypts new or updated vault contents locally.

3. The client sends an authenticated request containing encrypted
   vault data to the backend API over HTTPS/TLS.

4. The backend checks access permissions and stores the encrypted
   vault data.

5. An authorized client retrieves encrypted vault data over
   HTTPS/TLS and decrypts it locally using the appropriate keys.

Authentication and vault decryption have different purposes:
authentication controls access to server services, while local
decryption makes encrypted vault contents readable to the user.

This flow summarizes the documented architecture. It is not
a record of runtime testing performed by our team.

Source:
https://bitwarden.com/help/bitwarden-security-white-paper/

## 6. Limitations
This architecture summary is based on official documentation
and the selected Bitwarden server source version.

No application deployment, runtime testing, or penetration
testing has been performed for this section.

Client-side encryption has not been independently verified
because this review focuses on the server repository.

The authentication code review, threat model, and security
control analysis will be added through separate team contributions.This is a source-code and documentation review.
No runtime testing has been performed at this stage.

## 7. References
1. Bitwarden server source:
   https://github.com/bitwarden/server

2. Reviewed source version:
   https://github.com/bitwarden/server/tree/4e4adf99e96127e3955bbd7a1e733af75106ed21

3. Bitwarden Security Whitepaper:
   https://bitwarden.com/help/bitwarden-security-white-paper/
