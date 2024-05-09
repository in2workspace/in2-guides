---
layout: page
title: DOME Issuer Technical Documentation
version: v0.1.0
date: 2024-05-08
editor: Oriol Canadés Díez
---

<h1>DOME Issuer Technical Documentation - Draft 1</h1>

<h2>Table of Contents</h2>

# Introduction

This document explains the technical specification for the DOME Issuer. The DOME Issuer is a real world implementation 
of two different technical specifications: the [OpenID for Verifiable Credential Issuance - DOME profile](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html) and the [Authentication and authorization of entities acting on behalf of legal persons with Verifiable Credentials under eIDAS framework](https://dome-marketplace.github.io/powers-of-representation/index.html).

# CORE Concepts

The DOME Issuer implements the following core features from the technical specification documents. We know there are 
many possibilities to implement the Issuer, but we have decided the following features:

- Implements **Issuer Initiated** *(vs. Wallet Initiated)*.
- Implements **Pre-Authorized Code Flow** *(vs. Authorization Code Flow)*.
- Implements **Same-Device** and **Cross-Device** Credential Offer.
- Implements **Immediate** and **Deferred** Credential Issuance.

> JUSTIFICATION: The Credential Issuer only supports the Pre-Authorized Code Flow because the Credential Offer is 
> pre-created by the Human Resources department of the organization issuing the LEAR Credential to one of its employees.

> JUSTIFICATION: The flow will be Issuer initiated (using a QR code), because the organization offers the LEAR Credential 
> to an appointed employee after it has been decided after conversation between the HR department, the appointed employee 
> and the legal representative of the organization that has to sign the Credential.


# Flow Overview

The DOME Issuer implements the following flow for the issuance of a LEAR Credential to an employee of an organization:

![Credential Issuance Flow](./images/dome-issuer-flow-overview.png)

1. The HR Employee or Legal Representative accesses the Credential Issuer Portal.
2. The HR Employee or Legal Representative logs in to the Credential Issuer Portal using their Digital Certificate.
3. The HR Employee or Legal Representative registers a new LEAR Credential for a specific Employee of their organization using a form.
4. The Credential Issuer notifies the Employee with the link needed to start the credential issuance process.
5. The Employee accesses the Credential Issuer executing the link attached in the received email.
6. The Credential Issuer makes a Credential Offer and updates the Credential Procedure.
7. The Credential Issuer sends the tx_code (PIN) to the Employee via email.
8. The Credential Issuer displays a QR code.
9. The Employee accesses the Wallet with their credentials.
10. The Employee scans the QR code with the Wallet.
11. The Wallet fetches the Credential Offer Uri.
12. The Wallet fetches the Credential Issuer's Metadata and the Authorization Server's Metadata.
13. The Employee interacts with the Wallet adding the tx_code received in the email.
14. The Wallet sends a Token Request to the Credential Issuer. The Token Request contains the Pre-Authorized Code obtained in the Credential Offer and the tx_code added by the Employee.
15. The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint. It contains the Access Token and the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to.
16. The Credential Issuer sends and emails to the HR Employee or Legal Representative to notify they have a new credential pending to be signed.
17. The HR Employee or Legal Representative reads the notification email. (out of scope)
18. The HR Employee or Legal Representative logs in to the Local Signature using their Digital Certificate. (out of scope)
19. The HR Employee or Legal Representative retrieves the pending credentials.
20. The HR Employee or Legal Representative selects one or more credentials to sign. (out of scope)
21. The Local Signature posts the signed credentials to the Credential Issuer.
22. The Credential Issuer sends an email to the Employee notifying that the Credential is ready to be retrieved.
23. The Employee reads the email and accesses the Wallet.
24. The Wallet retrieves the Credential using a Deferred Credential Request (access_token, transaction_id).

This is an overview of the flow. The following sections will explain in detail each step of the flow.

# Detailed Flow

[//]: # (todo: add the detailed flow)

## 1. The HR Employee or Legal Representative accesses the Credential Issuer Portal.

## 2. The HR Employee or Legal Representative logs in to the Credential Issuer Portal using their Digital Certificate.

1. The HR Employee or Legal Representative clicks on the button "Log in".
2. The Credential Issuer redirects the HR Employee or Legal Representative to the Authorization Server (*a Keycloak implementation*) which request to the browser to attach a Digital Certificate which will be used to log in.
3. The HR Employee or Legal Representative selects the Digital Certificate.
4. The Authorization Server validates the Digital Certificate.
   1. If the Digital Certificate is valid and is associated with a valid registered user, the Authorization Server redirects the HR Employee or Legal Representative to the Credential Issuer Portal.
   2. If the Digital Certificate is not valid, the Authorization Server shows an error message to the HR Employee or Legal Representative.
   3. If the Digital Certificate is valid but is not associated with a valid registered user, the Authorization Server starts the registration process for the HR Employee or Legal Representative. The registration process must include the validation of the Digital Certificate and the email address of the HR Employee or Legal Representative. 
      1. If the Digital Certificate does not include the email address, the Authorization Server must request the email address to the HR Employee or Legal Representative.
      2. If the email address is already registered, the Authorization Server must show an error message to the HR Employee or Legal Representative. 
      3. If the email address is verified, the Authorization Server creates a new user and redirects the HR Employee or Legal Representative to the Credential Issuer Portal.

## 3. The HR Employee or Legal Representative registers a new LEAR Credential for a specific Employee of their organization using a form.

1. The Credential Issuer shows a form to the HR Employee or Legal Representative to register a new LEAR Credential Employee. The data related to the `Mandator` (HR Employee or Legal Representative) is pre-filled using the data from the Digital Certificate.
2. The HR Employee or Legal Representative fills the form with the data of the Employee - `Mandatee` - that will receive the LEAR Credential.
3. The HR Employee or Legal Representative sets the powers of representation that the Employee will have, for now `Onboarding` and `Product Offering`.
4. The HR Employee or Legal Representative clicks on the button *Register LEAR Credential*.
5. The Credential Issuer validates the data and creates a new LEAR Credential Employee. This LEAR Credential Employee has the final format, but the Cryptographic Binding is not set and the credential is not signed yet. 
6. The Credential Issuer creates a new `Credential Procedure`. The Credential Procedure is in the status "WITHDRAWN" and has the Credential (*decoded - json format*).
7. The Credential Issuer creates a new `Deferred Credential Metadata`. It includes the Transaction Code, as `transaction_code`, and the id of the Credential Procedure, `procedure_id`. This Transaction Code is a nonce that will be used to bind the Credential Offer with the Credential Request and to create the URI that will be sent to the Employee via email.

## 4. The Credential Issuer notifies the Employee with the link needed to start the credential issuance process.

1. When the Credential Issuer finishes the registration of the LEAR Credential Employee, it sends an email to the Employee with the link to start the credential issuance process. The link includes the Transaction Code as a query parameter.

This is a non-normative example of the link that the Employee will receive:

```text
 https://issuer.dome-marketplace.eu/credentials?transaction_code=oaKazRN8I0IbtZ0C7JuMn5
```

## 5. The Employee accesses the Credential Issuer executing the link attached in the received email.

1. The Employee reads the email and clicks on the link to start the process of receiving the LEAR Credential.
2. The Employee is redirected to the Credential Issuer Portal.
3. The Credential Issuer validates the `transaction_code` and retrieves the `Credential Procedure`.

## 6. The Credential Issuer makes a Credential Offer and updates the Deferred Credential Metadata.

1. The Credential Issuer makes a `Credential Offer`:
   1. The Credential Issuer fetches the Authorization Server to create a `pre-authorized_code`.
   2. The Credential Issuer creates a `tx_code`, which is a PIN that will be sent to the Employee via email.
2. The Credential Offer updates the `Deferred Credential Metadata`. The `pre-authorized_code` is added to the Credential Procedure as `auth_server_nonce` attribute.
3. The Credential Issuer builds a `Credential Offer Uri` ([section 4.1.3](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-sending-credential-offer-by-)). This URI is a link that points to the Credential Offer. This `credential_offer_uri` is displayed as a QR code.

   This is a non-normative example of the Credential Offer Uri:

   ```text
   credential_offer_uri=https%3A%2F%2Fserver%2Eexample%2Ecom%2Fcredential-offer.json
   ```

## 7. The Credential Issuer sends the tx_code (PIN) to the Employee via email.

## 8. The Credential Issuer displays a QR code.

## 9. The Employee accesses the Wallet with their credentials.

## 10. The Employee scans the QR code with the Wallet.

## 11. The Wallet fetches the Credential Offer Uri.

## 12. The Wallet fetches the Credential Issuer's Metadata and the Authorization Server's Metadata.

## 13. The Employee interacts with the Wallet adding the tx_code received in the email.

## 14. The Wallet sends a Token Request to the Credential Issuer. The Token Request contains the Pre-Authorized Code obtained in the Credential Offer and the tx_code added by the Employee.

## 15. The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint. It contains the Access Token and the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to.

## 16. The Credential Issuer sends and emails to the HR Employee or Legal Representative to notify they have a new credential pending to be signed.

## 17. The HR Employee or Legal Representative reads the notification email. (out of scope)

## 18. The HR Employee or Legal Representative logs in to the Local Signature using their Digital Certificate. (out of scope)

## 19. The HR Employee or Legal Representative retrieves the pending credentials.

## 20. The HR Employee or Legal Representative selects one or more credentials to sign. (out of scope)

## 21. The Local Signature posts the signed credentials to the Credential Issuer.

## 22. The Credential Issuer sends an email to the Employee notifying that the Credential is ready to be retrieved.

## 23. The Employee reads the email and accesses the Wallet.

## 24. The Wallet retrieves the Credential using a Deferred Credential Request (access_token, transaction_id).





























1. The HR Employee or Legal Representative accesses the Credential Issuer Portal.
2. The HR Employee or Legal Representative logs in to the Credential Issuer Portal using their Digital Certificate.
3. The HR Employee or Legal Representative accesses the Credential Management page and clicks on "Create new credential".
4. The HR Employee or Legal Representative registers a new LEAR Credential for a specific Employee of their organization using a form.
5. The Credential Issuer generates a Transaction Code and binds it to the LEAR Credential through a Credential Procedure.
6. The Credential Issuer notifies the Employee with the link needed to start the Pre-Authorized Code Flow.
7. The Employee accesses the Credential Issuer executing the link attached in the received email.
8. The Credential Issuer makes a Credential Offer and updates the Credential Procedure. 
9. The Credential Issuer sends the tx_code to the Employee via email. 
10. The Credential Issuer makes a Credential Offer Uri which is displayed as a QR code. 
11. The Employee accesses the Wallet with their credentials and scans the QR code. 
12. The Wallet reads the QR code content, executes the Credential Offer Uri, and retrieves the Credential Offer. 
13. The Wallet fetches the Credential Issuer's metadata. 
14. The Wallet fetches the Authorization Server's metadata. 
15. The Wallet sends a Token Request to the Credential Issuer. Toke Request contains the Pre-Authorized Code obtained in the Credential Offer and the tx_code obtained in the email.
16. The Credential Issuer validates the Token Request and sends the Token Response to the Wallet.
17. The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint with the Access Token and the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to.
18. The Credential Issuer validates the Access Token and the proof of possession. 
19. The Credential Issuer updates the LEARCredentialEmployee with the DID of the Employee.
20. The Credential Issuer updates the Credential Procedure with the Credential and sets a new status of the Credential Issuance (ISSUED).
21. The Credential Issuer sends an email to the Employee with the information about the next steps to follow.
22. The Credential Issuer generates the Transaction ID and stores it in memory.
23. The Credential Issuer sends and emails to the HR Employee or Legal Representative to notify they have a new credential pending to be signed.
24. The Credential Issuer sends the Credential (unsigned) and the Transaction ID to the Wallet.
25. The Wallet stores the Credential and required data to retrieve it in the future (Access Token and Transaction ID).
26. The HR Employee or Legal Representative accesses the Local Signatory. (out of scope)
27. The HR Employee or Legal Representative logs in to the Local Signatory using their Digital Certificate. (out of scope)
28. The HR Employee or Legal Representative retrieves the pending credentials.
29. The HR Employee or Legal Representative selects one or more credentials to sign. (out of scope)
30. The Local Signatory signs the Credential and sends it back to the Credential Issuer. (out of scope)
31. The Credential Issuer updates the Credential Procedure with the signed Credential and sets a new status of the Credential Issuance (VALID).
32. The Credential Issuer sends an email to the Employee notifying that the Credential is ready to be retrieved.
33. The Employee accesses the Wallet and retrieves the Credential using a Credential Request (Access Token, Transaction ID).
34. The Credential Issuer validates the Access Token and the Transaction ID and sends the Verifiable Credential to the Wallet.
35. The Wallet stores the Verifiable Credential in the Wallet.


# DOME Profile



## Issuer

- Exposes Credential Endpoint (section 7)
- Exposes Deferred Credential Endpoint (section9)
- Mechanism to make a Credential Offer (section 4)
- Mechanism to publish Issuer Metadata

> NOTE: Batch Endpoint is not implemented in the DOME Profile

> NOTE: Deferred Credential Endpoint (Option) is implemented in DOME Profile (Required) because the LEAR Credential must be signed by the legal representative (a natural person) of the organization, and this requires the manual intervention of the legal representative to authorize the signature.

> NOTE: The Credential Offer Mechanism makes a Credential Offer, but exposes credential_offer_uri as a QR code that has to be scanned by the Wallet of the End-User. This solution permits reducing the size of the QR code.

> NOTE: Wallet Notification Mechanism is not implemented in the DOME Profile.

> NOTE: DOME Profile requires that the LEAR Credential must implement the cryptographic binding of the DID of the employee in the credential.

> NOTE: We only support issuance of a single Credential of a specific type (the LEAR Credential for a given employee) with a given Access Token.

> NOTE: The unique format supported for the Credential is JWT.

## Authorization Server

> NOTE: The reference implementation of the issuer for LEAR Credentials has only one Authorization Server, and in addition, it is implemented in the Credential Issuer.

> NOTE: We use the "Pre-Authorized Code" Grant Type. The reasons for this are:
>   - The claims in the LEAR Credential are essentially based on employee data coming from the HR database, and typically the issuance process is initiated by the company. The employee to act as LEAR is pre-selected and then credential offer is prepared in advance using that employee data. The employee is notified in advance via some off-line mechanism and also when the credential offer is ready. The employee then will request the actual issuance to her wallet using the mechanisms described in this document.
>   - With these considerations, the flows for issuance are simpler while maintaining the required level of security.
> 
> We do not use Client metadata, as we do not require the Issuer to know in advance the wallet that will be used in the process. The issuance process will use a QR code scanned by the wallet, so the Issuer does not use the credential_offer_endpoint of the wallet.
> 
> The LEAR Credential Issuer does not have to support the Authorization Endpoint because is uses the "Pre-Authorized Code" Grant Type.

# Credential Issuance - DOME Profile (Tech. Spec from OIDC4VCI Draft 13)

This document explains the technical specification for the Credential Issuance in the DOME Profile. The DOME Profile is a profile for the OIDC4VCI Draft 13 Protocol.

DOME Profile Technical Decisions:

- Implements Issuer Initiated
- Implements Pre-Authorized Code Flow
- Implements Same-Device or Cross-Device Credential Offer
- Implements Immediate and Deferred



## Credential Issuance - Pre-Authorized Code Flow

In this section, we want to expose the flow for the Credential Issuance using the Pre-Authorized Code Flow.

To understand the technical specification and how it could be implemented in the real world, we will use the following structure:

* **Protocol explanation**: is the explanation that is part of the DOME Profile original specification document.
* **Step**: The step that is how we implement the technical specification in the real world.
* **Justification**: The justification for the technical decision made by the DOME Profile to the real world.

![Credential Issuance - Pre-Authorized Code Flow](./images/dome-profile-pre-auth-code.png)

> Protocol explanation: (1) The Credential Issuer successfully obtains consent and End-User data required for the issuance of a requested Credential from the End-User using an Issuer-specific business process.

1. The End-User accesses the Credential Issuer Portal. (DOME + LEAR) --> Landing Page

2. The End-User logs in to the Credential Issuer Portal. (DOME + LEAR)

3. The End-User accesses the Credential Management page and clicks on "Create New Credential" 

4. The End-User registers a new LEARCredentialEmployee (without cryptographic binding) for a specific Employee of their organization using a form. (LEAR)

5. The Credential Issuer generates a Transaction Code and binds to the LEARCredentialEmployee though a LearCredentialProcedure. (LEAR)

```json
{
  "LearnCredentialProcedure": {
    "id": "82ea95d1-4522-47bd-8e05-a1f699cab3a7",
    "transaction_code": "oaKazRN8I0IbtZ0C7JuMn5",
    "pre-auth_code": "",
    "lear_credential_employee_json": { },
    "status": "WITHDRAWN",
    "updated": "2021-09-01T12:00:00Z"
  }
}
```

5. The Credential Issuer notifies the Employee with the Credential Issuer URI needed to start the Pre-Authorized Code Flow. (LEAR)

https://issuer.dome-marketplace.eu/credentials?transaction_code=transaction_code

6. The Employee accesses the Credential Issuer executing the link attached in the received email. (LEAR)

> Protocol explanation: (2) The flow defined in this specification begins as the Credential Issuer generates a Credential Offer for certain Credential(s) and communicates it to the Wallet, for example, as a QR code or as a URI. The Credential Offer contains the Credential Issuer's URL, the information about the Credential(s) being offered, and the Pre-Authorized Code. This step is defined in Section 4.1.

7. The Credential Issuer makes a Credential Offer and persists in memory.

> NOTE: The Credential Offer 

````json
    {
       "credential_issuer": "https://issuer.dome-marketplace.eu",
       "credential_configuration_ids": [
          "LEARCredentialEmployee"
       ],
       "grants": {
          "urn:ietf:params:oauth:grant-type:pre-authorized_code": {
             "pre-authorized_code": "oaKazRN8I0IbtZ0C7JuMn5",
             "tx_code": {
                "length": 4,
                "input_mode": "numeric",
                "description": "Please provide the one-time code that was sent via e-mail"
             }
          }
       }
    }
````

> PROBLEM: ¿Cómo vinculamos la LEARCredentialEmployeeData+TransactionCode con la CredentialOffer?


5. The Credential Issuer creates a credential_offer_uri, which points to the Credential Offer made, and it is displayed as a QR code.


6. The End-User scans the QR code with the Wallet.


7. The Wallet reads the QR code content, executes the credential_offer_uri, and retrieves the Credential Offer.

> Protocol explanation: (3) The Wallet uses the Credential Issuer's URL to fetch its metadata, as described in Section 11.2. The Wallet needs the metadata to learn the Credential types and formats that the Credential Issuer supports, and to determine the Token Endpoint (at the OAuth 2.0 Authorization Server) as well as the Credential Endpoint required to start the request.

8. The Wallet fetches the Credential Issuer's metadata.
9. The Wallet fetched the Authorization Server's metadata.

> Protocol explanation: (5-6) The Wallet sends the Pre-Authorized Code obtained in Step (2) in the Token Request to the Token Endpoint. The Wallet will additionally send a Transaction Code provided by the End-User, if it was required by the Credential Issuer. This step is defined in Section 6.

> Protocol explanation: (7-8) This step is the same as Step (5) in the Authorization Code Flow. 
> It is important to note that anyone who possesses a valid Pre-Authorized Code, without further security measures, would be able to receive a VC from the Credential Issuer. Implementers MUST implement mitigations most suitable to the use case. 
> One such mechanism defined in this specification is the usage of Transaction Codes. The Credential Issuer indicates the usage of Transaction Codes in the Credential Offer and sends the Transaction Code to the End-User via a second channel different than the issuance flow. After the End-User provides the Transaction Code, the Wallet sends the Transaction Code within the Token Request, and the Authorization Server verifies the Transaction Code.
> (7-8) The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint with the Access Token and (optionally) the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to. Upon successfully validating Access Token and proof, the Credential Issuer returns a Credential in the Credential Response. This step is defined in Section 7.

