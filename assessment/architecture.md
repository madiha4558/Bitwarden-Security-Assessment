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
[Explain local vault encryption, encrypted storage,
TLS communication, and authentication.]

## 5. Data Flow
[Explain how a client communicates with the server
and stores or retrieves encrypted vault data.]

## 6. Limitations
This is a source-code and documentation review.
No runtime testing has been performed at this stage.

## 7. References
- https://github.com/bitwarden/server
- https://bitwarden.com/help/bitwarden-security-white-paper/
