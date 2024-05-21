---
layout: page
title: DOME Issuer Technical Documentation
version: v0.1.1-draft1
date: 2024-05-21
editor: Oriol Canadés
authors: Alberto Rubio, Oriol Canadés
---

<h1>DOME Issuer Technical Documentation - Draft 1</h1>

<h2>Table of Contents</h2>
<!-- TOC -->
* [Introduction](#introduction)
* [CORE Concepts](#core-concepts)
* [Flow Overview](#flow-overview)
* [Detailed Flow](#detailed-flow)
  * [1. The HR Employee or Legal Representative accesses the Credential Issuer Portal.](#1-the-hr-employee-or-legal-representative-accesses-the-credential-issuer-portal)
  * [2. The HR Employee or Legal Representative logs in to the Credential Issuer Portal using their Digital Certificate.](#2-the-hr-employee-or-legal-representative-logs-in-to-the-credential-issuer-portal-using-their-digital-certificate)
  * [3. The HR Employee or Legal Representative registers a new LEAR Credential for a specific Employee of their organization using a form.](#3-the-hr-employee-or-legal-representative-registers-a-new-lear-credential-for-a-specific-employee-of-their-organization-using-a-form)
  * [4. The Credential Issuer notifies the Employee with the link needed to start the credential issuance process.](#4-the-credential-issuer-notifies-the-employee-with-the-link-needed-to-start-the-credential-issuance-process)
  * [5. The Employee accesses the Credential Issuer executing the link attached in the received email.](#5-the-employee-accesses-the-credential-issuer-executing-the-link-attached-in-the-received-email)
  * [6. The Credential Issuer makes a Credential Offer and updates the Deferred Credential Metadata.](#6-the-credential-issuer-makes-a-credential-offer-and-updates-the-deferred-credential-metadata)
  * [7. The Credential Issuer sends the tx_code (PIN) to the Employee via email.](#7-the-credential-issuer-sends-the-tx_code-pin-to-the-employee-via-email)
  * [8. The Credential Issuer displays a QR code.](#8-the-credential-issuer-displays-a-qr-code)
  * [9. The Employee accesses the Wallet with their credentials.](#9-the-employee-accesses-the-wallet-with-their-credentials)
  * [10. The Employee scans the QR code with the Wallet.](#10-the-employee-scans-the-qr-code-with-the-wallet)
  * [11. The Wallet fetches the Credential Offer Uri.](#11-the-wallet-fetches-the-credential-offer-uri)
  * [12. The Wallet fetches the Credential Issuer's Metadata and the Authorization Server's Metadata.](#12-the-wallet-fetches-the-credential-issuers-metadata-and-the-authorization-servers-metadata)
  * [13. The Employee interacts with the Wallet adding the tx_code received in the email.](#13-the-employee-interacts-with-the-wallet-adding-the-tx_code-received-in-the-email)
  * [14. The Wallet sends a Token Request to the Credential Issuer. The Token Request contains the Pre-Authorized Code obtained in the Credential Offer and the tx_code added by the Employee.](#14-the-wallet-sends-a-token-request-to-the-credential-issuer-the-token-request-contains-the-pre-authorized-code-obtained-in-the-credential-offer-and-the-tx_code-added-by-the-employee)
  * [15. The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint. It contains the Access Token and the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to.](#15-the-wallet-sends-a-credential-request-to-the-credential-issuers-credential-endpoint-it-contains-the-access-token-and-the-proof-of-possession-of-the-private-key-of-a-key-pair-to-which-the-credential-issuer-should-bind-the-issued-credential-to)
  * [16. The Credential Issuer sends and emails to the HR Employee or Legal Representative to notify they have a new credential pending to be signed.](#16-the-credential-issuer-sends-and-emails-to-the-hr-employee-or-legal-representative-to-notify-they-have-a-new-credential-pending-to-be-signed)
  * [17. The HR Employee or Legal Representative reads the notification email. (out of scope)](#17-the-hr-employee-or-legal-representative-reads-the-notification-email-out-of-scope)
  * [18. The HR Employee or Legal Representative logs in to the Local Signature using their Digital Certificate. (out of scope)](#18-the-hr-employee-or-legal-representative-logs-in-to-the-local-signature-using-their-digital-certificate-out-of-scope)
  * [19. The HR Employee or Legal Representative retrieves the pending credentials.](#19-the-hr-employee-or-legal-representative-retrieves-the-pending-credentials)
  * [20. The HR Employee or Legal Representative selects one or more credentials to sign. (out of scope)](#20-the-hr-employee-or-legal-representative-selects-one-or-more-credentials-to-sign-out-of-scope)
  * [21. The Local Signature posts the signed credentials to the Credential Issuer.](#21-the-local-signature-posts-the-signed-credentials-to-the-credential-issuer)
  * [22. The Credential Issuer sends an email to the Employee notifying that the Credential is ready to be retrieved.](#22-the-credential-issuer-sends-an-email-to-the-employee-notifying-that-the-credential-is-ready-to-be-retrieved)
  * [23. The Employee reads the email and accesses the Wallet.](#23-the-employee-reads-the-email-and-accesses-the-wallet)
  * [24. The Wallet retrieves the Credential using a Deferred Credential Request (access_token, transaction_id).](#24-the-wallet-retrieves-the-credential-using-a-deferred-credential-request-access_token-transaction_id)
* [Data Model](#data-model)
  * [LEAR Credential Employee](#lear-credential-employee)
  * [Credential Procedure](#credential-procedure)
  * [Deferred Credential Metadata](#deferred-credential-metadata)
* [Formats](#formats)
  * [Date Format](#date-format)
* [Credential Issuer - SaaS](#credential-issuer---saas)
  * [1. Credential Issuer: Home Page (landing page) - public - Front](#1-credential-issuer-home-page-landing-page---public---front)
  * [2. Credential Issuer: Log in or Sign up - public - Front, Auth Server](#2-credential-issuer-log-in-or-sign-up---public---front-auth-server)
  * [3. Credential Issuer: Credential Management - private - Front, Back](#3-credential-issuer-credential-management---private---front-back)
  * [4. Credential Issuer: Create Credential - private - Front, Back](#4-credential-issuer-create-credential---private---front-back)
  * [5. Credential Issuer: Credential Details - private - Front, Back](#5-credential-issuer-credential-details---private---front-back)
  * [6. Credential Issuer: Credential Offer - public - Front, Back](#6-credential-issuer-credential-offer---public---front-back)
  * [7. Credential Wallet: Credentials Page - private - Front, Back](#7-credential-wallet-credentials-page---private---front-back)
* [API and Contracts](#api-and-contracts)
  * [1. Credential Issuer: Credential Offer - public - Front, Back](#1-credential-issuer-credential-offer---public---front-back)
  * [2. Credential Issuer: Credential Management - private - Front, Back](#2-credential-issuer-credential-management---private---front-back)
  * [3. Credential Issuer: Create Credential - private - Front, Back](#3-credential-issuer-create-credential---private---front-back)
    * [GetAllCredentialsByOrganization()](#getallcredentialsbyorganization)
  * [4. Credential Issuer: Create Credential - private - Front, Back](#4-credential-issuer-create-credential---private---front-back-1)
    * [CreateLEARCredentialEmployee()](#createlearcredentialemployee)
  * [5. Credential Issuer: Credential Details - private - Front, Back](#5-credential-issuer-credential-details---private---front-back-1)
  * [5. Credential Issuer: Credential Offer - public - Front, Back](#5-credential-issuer-credential-offer---public---front-back)
    * [GetCredentialOfferByTransactionCode(transaction_code)](#getcredentialofferbytransactioncodetransaction_code)
* [Data Lifecycle](#data-lifecycle)
  * [Procedure Lifecycle](#procedure-lifecycle)
  * [Credential Lifecycle](#credential-lifecycle)
* [Security](#security)
<!-- TOC -->

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

## 1. The HR Employee or Legal Representative accesses the Credential Issuer Portal.

1. The HR Employee or Legal Representative accesses the Credential Issuer Portal using a web browser.
2. The Credential Issuer Portal shows the Home Page with the option to log in.
3. The HR Employee or Legal Representative clicks on the `Log in | Sing up` button.
4. The Credential Issuer redirects the HR Employee or Legal Representative to the Authorization Server.

## 2. The HR Employee or Legal Representative logs in to the Credential Issuer Portal using their Digital Certificate.

1. The Authorization Server request to the browser to attach a Digital Certificate which will be used to log in or sign up.
2. The HR Employee or Legal Representative selects a Digital Certificate from the browser.
3. The Authorization Server validates the Digital Certificate:
   1. If the Digital Certificate is valid and is associated with a valid registered user, the Authorization Server logs in and redirects the HR Employee or Legal Representative to the Credential Issuer Portal.
   2. If the Digital Certificate is not valid, the Authorization Server shows an error message to the HR Employee or Legal Representative.
   3. If the Digital Certificate is valid but is not associated with a valid registered user, the Authorization Server starts the registration process for the HR Employee or Legal Representative. The registration process must include the validation of the Digital Certificate and the verification of the HR Employee or Legal Representative's email address. 
      1. If the Digital Certificate does not include the email address, the Authorization Server must request the email address to the HR Employee or Legal Representative.
      2. If the email address is already registered, the Authorization Server must show an error message to the HR Employee or Legal Representative. 
      3. If the email address is verified, the Authorization Server updates the status of the user to a valid status.
      4. If the HR Employee or Legal Representative tries to log in but the email address is not verified, the Authorization Server must show an error message to the HR Employee or Legal Representative.
      5. If the HR Employee or Legal Representative tries to log in and the email address is verified, and the Digital Certificate is valid, the Authorization Server logs in and redirects the HR Employee or Legal Representative to the Credential Issuer Portal.

## 3. The HR Employee or Legal Representative registers a new LEAR Credential for a specific Employee of their organization using a form.

> NOTE: A non-normative example of the LEAR Credential Employee could be found in the [Data Model - LEAR Credential Employee](#lear-credential-employee) section.

1. The Credential Issuer shows a form to the HR Employee or Legal Representative to register a new LEAR Credential Employee. The data related to the `Mandator` (HR Employee or Legal Representative) is pre-filled using the data from the Digital Certificate.
2. The HR Employee or Legal Representative fills `Mandatee` section with the data of the Employee that will receive the LEAR Credential using the form.
3. The HR Employee or Legal Representative sets the powers of representation that the Employee will have, for now `Onboarding` and `Product Offering`, but many others could be added in the future.
   > NOTE: Not each LEAR Credential Employee needs to set all the powers of representation. The HR Employee or Legal Representative can set the powers of representation that the Employee will have and then, set the actions related to that power.
4. The HR Employee or Legal Representative clicks on the `Create Credential` button.
5. The Credential Issuer validates the data and creates a new LEAR Credential Employee. 
   > NOTE: This LEAR Credential Employee has the final format, but the Cryptographic Binding is not set and the credential is not signed yet.
6. The Credential Issuer creates a new `Credential Procedure` entity. The Credential Procedure is in the status `WITHDRAWN` and sets the attribute `credential_decoded` with the Credential received.
   > NOTE: The Credential Procedure is a way to manage the Credential Issuance process. It includes the Credential, the status of the Credential Issuance, and the organization identifier, etc. You can find more information about the Credential Procedure in the [Data Model - Credential Procedure](#credential-procedure) section.
   
   This is a non-normative example of the Credential Procedure:
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
7. The Credential Issuer creates a new `Deferred Credential Metadata`. It includes the Transaction Code, as `transaction_code`, and the id of the Credential Procedure, `procedure_id`. This Transaction Code is a nonce that will be used to bind the Credential Offer with the Credential Request and to create the URI that will be sent to the Employee via email.
   > NOTE: The Deferred Credential Metadata is a way to manage the metadata needed to manage the deferred process. It includes the Transaction Code, the Credential Procedure id, etc. You can find more information about the Deferred Credential Metadata in the [Data Model - Deferred Credential Metadata](#deferred-credential-metadata) section.

## 4. The Credential Issuer notifies the Employee with the link needed to start the credential issuance process.

1. When the Credential Issuer finishes the registration of the LEAR Credential Employee, it sends an email to the Employee with the link to start the credential issuance process. The link includes the Transaction Code as a query parameter. This is a non-normative example of the link that the Employee will receive:

   ```text
    https://issuer.dome-marketplace.eu/credentials?transaction_code=oaKazRN8I0IbtZ0C7JuMn5
   ```

   > NOTE: The Credential Issuer sends the email using an SMTP server. The email includes a template with the link to start the credential issuance process and short but descriptive documentation about the process that the employee will follow. The email is sent to the email address of the Employee that was set in the LEAR Credential Employee form.

## 5. The Employee accesses the Credential Issuer executing the link attached in the received email.

1. The Employee reads the email and clicks on the link to start the process of receiving the LEAR Credential.
2. The Employee is redirected to the **Credential Offer Page** in the Credential Issuer Portal.
   > NOTE: You can find a mockup of the Credential Offer Page in the [Credential Issuer: Credential Offer Page](#5-credential-issuer-credential-offer---public---front-back) section.
3. The Credential Issuer validates the `transaction_code` and retrieves the `Credential Procedure`.

## 6. The Credential Issuer makes a Credential Offer and updates the Deferred Credential Metadata.

1. The Credential Issuer makes a `Credential Offer`:
   1. The Credential Issuer fetches the Authorization Server to create a `pre-authorized_code`.
   2. The Credential Issuer creates a `tx_code`, which is a PIN that will be sent to the Employee via email.
2. The Credential Offer updates the `Deferred Credential Metadata`. The `pre-authorized_code` is added to the Credential Procedure as `auth_server_nonce` attribute.
   
## 7. The Credential Issuer sends the tx_code (PIN) to the Employee via email.

The `tx_code` created during the Credential Offer is sent to the Employee via email. We will use a template to send the email to the Employee.

## 8. The Credential Issuer displays a QR code.

1. The Credential Issuer builds a `Credential Offer Uri` ([section 4.1.3](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-sending-credential-offer-by-)). This URI is a link that points to the Credential Offer. This `credential_offer_uri` is displayed as a QR code.

   The Credential Offer Uri uses a `nonce` value to bind the Credential Offer with the Credential Offer Uri.

   The `nonce` is saved as a key value in memory cache, and the Credential Offer as the value. This is burnt after the Credential Offer is retrieved by executing the Credential Offer Uri by the Wallet.

   This is a non-normative example of the Credential Offer Uri:

   ```text
       credential_offer_uri=https%3A%2F%2Fserver%2Eexample%2Ecom%2F84dr684f51jfdbj
   ```

## 9. The Employee accesses the Wallet with their credentials.

1. The Employee opens the Wallet application on their device.
2. The Employee logs in to the Wallet using their credentials (username and password).

## 10. The Employee scans the QR code with the Wallet.

1. The Employee clicks on the button `Scan`.
2. The camera of the device is activated.
3. The Employee scans the QR code displayed by the Credential Issuer.
4. The Wallet reads the QR code content, interprets that it is a Credential Offer Uri, and executes the Credential Offer Uri.

## 11. The Wallet fetches the Credential Offer Uri.

1. The Wallet fetches the Credential Offer executing tha parsed `credential_offer_uri`.

   This is a non-normative example of the Credential Offer Uri:
   
   ```curl
      GET /credential_offer/84dr684f51jfdbj HTTP/1.1
      Host: issuer.dome-marketplace.eu
      https://server.example.com/84dr684f51jfdbj
   ```
   ```curl
      HTTP/1.1 200 OK
      Content-Type: application/json
      Accept-Language: en;q=0.8
      {
         "credential_issuer": "https://credential-issuer.example.com",
         "credential_configuration_ids": [
            "UniversityDegree_LDP_VC"
         ],
         "grants": {
         "urn:ietf:params:oauth:grant-type:pre-authorized_code": {
            "pre-authorized_code": "adhjhdjajkdkhjhdj",
            "tx_code": {}
         }
      }
   }
   ```

> NOTE: The Credential Offer Response includes the header `Accept-Language` to indicate the language preferred for display. The language(s) in HTTP Accept-Language and Content-Language Headers MUST use the values defined in [RFC3066]. 

> JUSTIFICATION: We do not implement the `Accept-Language` header in the Wallet's Credential Issuer Metadata request.

## 12. The Wallet fetches the Credential Issuer's Metadata and the Authorization Server's Metadata.

1. The Wallet fetches the Credential Issuer's Metadata ([section 11.2](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-credential-issuer-metadata)) creating a dynamic URL using the parameter `credential_issuer` and concatenating the path `/.well-known/openid-credential-issuer`.

   > NOTE: The communication with the Issuer Metadata Endpoint MUST use TLS.
   
   > NOTE: The request MUST be an HTTP request using GET method and the URL SHOULD NOT contain any query parameters.
   
   > NOTE: The Credential Issuer MUST return a JSON document compliant with this specification using the application/json media type and the HTTP Status Code 200.
   
   > NOTE: The Credential Issuer Metadata response includes the header `Accept-Language` to indicate the language preferred for display. The language(s) in HTTP Accept-Language and Content-Language Headers MUST use the values defined in [RFC3066].
   
   > JUSTIFICATION: We do not implement the `Accept-Language` header in the Wallet's Credential Issuer Metadata request.
   
   ```curl
   GET /.well-known/openid-credential-issuer HTTP/1.1
   Host: issuer.dome-marketplace.eu
   Accept-Language: fr-ch, fr;q=0.9, en;q=0.8, de;q=0.7, *;q=0.5
   ```
   
   This is a non-normative example of the Credential Issuer Metadata:
   
   ```json
   {
      "credential_issuer": "https://credential-issuer.example.com",
      "authorization_servers": [ "https://server.example.com" ],
      "credential_endpoint": "https://credential-issuer.example.com",
      "batch_credential_endpoint": "https://credential-issuer.example.com/batch_credential",
      "deferred_credential_endpoint": "https://credential-issuer.example.com/deferred_credential",
      "credential_response_encryption": {
         "alg_values_supported" : [
            "ECDH-ES"
         ],
         "enc_values_supported" : [
            "A128GCM"
         ],
         "encryption_required": false
      },
      "display": [
         {
            "name": "Example University",
            "locale": "en-US"
         },
         {
            "name": "Example Université",
            "locale": "fr-FR"
         }
      ],
      "credential_configurations_supported": {
         "UniversityDegreeCredential": {
            "format": "jwt_vc_json",
            "scope": "UniversityDegree",
            "cryptographic_binding_methods_supported": [
               "did:example"
            ],
            "credential_signing_alg_values_supported": [
               "ES256"
            ],
            "credential_definition":{
               "type": [
                  "VerifiableCredential",
                  "UniversityDegreeCredential"
               ],
               "credentialSubject": {
                  "given_name": {
                     "display": [
                        {
                           "name": "Given Name",
                           "locale": "en-US"
                        }
                     ]
                  },
                  "family_name": {
                     "display": [
                        {
                           "name": "Surname",
                           "locale": "en-US"
                        }
                     ]
                  },
                  "degree": {},
                  "gpa": {
                     "display": [
                        {
                           "name": "GPA"
                        }
                     ]
                  }
               }
            },
            "proof_types_supported": {
               "jwt": {
                  "proof_signing_alg_values_supported": [
                     "ES256"
                  ]
               }
            },
            "display": [
               {
                  "name": "University Credential",
                  "locale": "en-US",
                  "logo": {
                     "url": "https://university.example.edu/public/logo.png",
                     "alt_text": "a square logo of a university"
                  },
                  "background_color": "#12107c",
                  "text_color": "#FFFFFF"
               }
            ]
         }
      }
   }
   ```

2. The Wallet fetches the Authorization Server's Metadata ([section 11.3](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-oauth-20-authorization-serv)) creating a dynamic URL using the parameter `credential_issuer` and concatenating the path `/.well-known/openid-configuration`.

   > NOTE: The Credential Issuer Metadata is offered by the Keycloak Plugin and the Authorization Server Metadata is offered by the Keycloak. Both are part of the same system.

## 13. The Employee interacts with the Wallet adding the tx_code received in the email.

Previously to start the Token Request, the Employee must add the `tx_code` received in the email to the Wallet. 

This is a security measure to ensure that the Employee is the one that has received the email and is the one that is interacting with the Wallet.

1. The Wallet shows a modal to the Employee to add the `tx_code`.
2. The Employee adds the `tx_code` to the Wallet.

## 14. The Wallet sends a Token Request to the Credential Issuer. The Token Request contains the Pre-Authorized Code obtained in the Credential Offer and the tx_code added by the Employee.

1. The Wallet sends a Token Request to the Credential Issuer's Token Endpoint, retrieved from the Authorization Server Metadata info. The Token Request contains the Pre-Authorized Code obtained in the Credential Offer and the tx_code added by the Employee.

   ```curl
   POST /token HTTP/1.1
   Host: issuer.dome-marketplace.eu
   Content-Type: application/x-www-form-urlencoded
   
   grant_type=urn:ietf:params:oauth:grant-type:pre-authorized_code
   &pre-authorized_code=SplxlOBeZQQYbYS6WxSbIA
   &tx_code=123456
   ```

2. The Credential Issuer validates the Token Request.

3. The Credential Issuer sends the `pre-authorized_code` and the `access_token` to the Credential Issuer.

   > NOTE: We need to find a solution about secure communication between both components.

   [//]: # (todo: consider all the security options about M2M communication)

4. The Credential Issuer gets the `Deferred Credential Metadata` by the `pre-authorized_code` and updates the `auth_server_nonce` with the `access_token`.

5. The Authorization Server sends the `Token Response` ([section 6.2](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-successful-token-response)) to the Wallet.

This is a non-normative example of the `Successful Token Response` ([section 6.2](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-successful-token-response)):

```curl
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
   "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6Ikp..sHQ",
   "token_type": "bearer",
   "expires_in": 86400,
   "c_nonce": "tZignsnFbp",
   "c_nonce_expires_in": 86400,
   "authorization_details": [
      {
         "type": "openid_credential",
         "credential_configuration_id": "LEARCredentialEmployee",
         "credential_identifiers": [ 
            "", 
            "" 
         ]
      }
   ]
}
```
   
This is a non-normative example of the `Token Error Response` ([section 6.3](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-token-error-response)) with an error:

```curl
HTTP/1.1 400 Bad Request
Content-Type: application/json
Cache-Control: no-store

{
   "error": "invalid_request"
}
```

## 15. The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint. It contains the Access Token and the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to.

1. The Wallet generates a `Credential Resquest` ([section 7.2](https://dome-marketplace.github.io/OpenID4VCI-DOMEprofile/openid-4-verifiable-credential-issuance-wg-draft.html#name-credential-request)). To do that, the Wallet needs:


[//]: # (todo: add the detailed flow)
   1. The Wallet creates a `did:key` to the Employee. This means the creation of a key pair, generating a DID, and storing the private key in a secure enclave such as HashiCorp Vault. 
2. The Wallet creates a `proof` 
3. The Wallet sends a Credential Request to the Credential Issuer's Credential Endpoint with the Access Token and the proof. This proof is the proof of possession of the private key of a key pair to which the Credential Issuer should bind the issued Credential to.


## 16. The Credential Issuer sends and emails to the HR Employee or Legal Representative to notify they have a new credential pending to be signed.

1. The Credential Issuer sends an email to the HR Employee or Legal Representative to notify they have a new credential pending to be signed.

## 17. The HR Employee or Legal Representative reads the notification email. (out of scope)

## 18. The HR Employee or Legal Representative logs in to the Local Signature using their Digital Certificate. (out of scope)

## 19. The HR Employee or Legal Representative retrieves the pending credentials.

1. The Local Signature component gets the pending credentials from the Credential Issuer. This request requires attaching of an Authorization header with the Digital Certificate of the HR Employee or Legal Representative.

This is a non-normative example of the request:

[//]: # (todo: add the detailed flow)
```curl
GET /pending-credentials HTTP/1.1
Host: local-signature.dome-marketplace.eu
Authorization bearer access_token
```

The response is a list of pending credentials that the HR Employee or Legal Representative can sign.

This is a non-normative example of the response:

```curl
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
   "credentials": [
      {
         "credential": {
           "id": "1f33e8dc-bd3b-4395-8061-ebc6be7d06dd",
           "type": [
             "VerifiableCredential",
             "LEARCredentialEmployee"
           ],
           "credentialSubject": {
             "mandate": {...},
               "mandatee": {...},
               "mandator": {...},
               "power": [{...}, {...}]
             }
           },
           "expirationDate": "2025-04-02 09:23:22.637345122 +0000 UTC",
           "issuanceDate": "2024-04-02 09:23:22.637345122 +0000 UTC",
           "issuer": "did:web:in2.es",
           "validFrom": "2024-04-02 09:23:22.637345122 +0000 UTC"
         }
      },
      {
         "credential": {...}
      }
   ]
}
```

## 20. The HR Employee or Legal Representative selects one or more credentials to sign. (out of scope)

## 21. The Local Signature posts the signed credentials to the Credential Issuer.

1. The Local Signature component signs the Credential and sends it back to the Credential Issuer. This request requires attaching of an Authorization header with the Digital Certificate of the HR Employee or Legal Representative.

This is a non-normative example of the request:

```curl
POST /credentials HTTP/1.1
Host: issuer.dome-marketplace.eu
Authorization bearer access_token
Content-Type: application/json

{
   "credentials": [
      {
         "credential": "eyJraWQiOiJkaWQ6ZXh...C_aZKPxgihac0aW9EkL1nOzM"
      },
      {
         "credential": "YXNkZnNhZGZkamZqZGFza23....29tZTIzMjMyMzIzMjMy"
      }
   ]
}
```

## 22. The Credential Issuer sends an email to the Employee notifying that the Credential is ready to be retrieved.

1. The Credential Issuer receives the signed Credentials from the Local Signature Component.
2. The Credential Issuer validates the Authorization header.
3. The Credential Issuer updates the `credential_encoded` of the Credential Procedure with the signed Credential and sets a new status of the Credential Issuance (VALID).
4. The Credential Issuer updates the `vc` attribute of the Deferred Credential Metadata with the signed Credential.
5. The Credential Issuer sends an email to the Employee notifying that the Credential is ready to be retrieved.

## 23. The Employee reads the email and accesses the Wallet.

## 24. The Wallet retrieves the Credential using a Deferred Credential Request (access_token, transaction_id).

1. The Employee clicks on the button `Retrieve Credential` of the representation of the credential *not signed* of their wallet. 
2. The Wallet sends a Deferred Credential Request to the Credential Issuer's Deferred Credential Endpoint with the Access Token and the Transaction ID.

3. The Credential Issuer validates the Access Token and the Transaction ID
   1. If the Access Token and the Transaction ID are valid, but the Credential is not ready, the Credential Issuer: 
      1. burns the Transaction ID
      2. generates a new Transaction ID
      3. persists the Transaction ID in memory
      4. updates the Deferred Transaction Metadata
      5. returns a Deferred Response with the new Transaction ID 
   2. If the Access Token and the Transaction ID are valid and the Credential is ready, the Credential Issuer:
      1. retrieves the Credential from the Credential Procedure
      2. returns a Deferred Response with the Credential

4. The Wallet receives the Deferred Response and retrieves the Credential and stores it in the Wallet.



# Data Model

## LEAR Credential Employee

This is a non-normative example of the LEAR Credential Employee:

```json
{
   "id": "1f33e8dc-bd3b-4395-8061-ebc6be7d06dd",
   "type": [
      "VerifiableCredential",
      "LEARCredentialEmployee"
   ],
   "credentialSubject": {
      "mandate": {
         "id": "4e3c02b8-5c57-4679-8aa5-502d62484af5",
         "life_span": {
            "end_date_time": "2025-04-02 09:23:22.637345122 +0000 UTC",
            "start_date_time": "2024-04-02 09:23:22.637345122 +0000 UTC"
         },
         "mandator": {
            "commonName": "IN2",
            "country": "ES",
            "emailAddress": "rrhh@in2.es",
            "organization": "IN2, Ingeniería de la Información, S.L.",
            "organizationIdentifier": "VATES-B60645900",
            "serialNumber": "B60645900"
         },
         "mandatee": {
            "id": "did:key:zDnaeei6HxVe7ibR3mZmXa9SZgWs8UBj1FiTuwEKwmnChdUAu",
            "email": "oriol.canades@in2.es",
            "first_name": "Oriol",
            "gender": "M",
            "last_name": "Canadés",
            "mobile_phone": "+34666336699"
         },
         "power": [
            {
               "id": "6b8f3137-a57a-46a5-97e7-1117a20142fb",
               "tmf_action": "Execute",
               "tmf_domain": "DOME",
               "tmf_function": "Onboarding",
               "tmf_type": "Domain"
            },
            {
               "id": "ad9b1509-60ea-47d4-9878-18b581d8e19b",
               "tmf_action": [
                  "Create",
                  "Update"
               ],
               "tmf_domain": "DOME",
               "tmf_function": "ProductOffering",
               "tmf_type": "Domain"
            }
         ]
      }
   },
   "expirationDate": "2025-04-02 09:23:22.637345122 +0000 UTC",
   "issuanceDate": "2024-04-02 09:23:22.637345122 +0000 UTC",
   "issuer": "did:web:in2.es",
   "validFrom": "2024-04-02 09:23:22.637345122 +0000 UTC"
}
```

## Credential Procedure

| Field                   | Type   | Required | Description                                         |
|-------------------------|--------|----------|-----------------------------------------------------|
| procedure_id            | UUID   | true     | The id of the Credential Procedure.                 |
| credential_id           | UUID   | true     | The id of the Credential.                           |
| credential_format       | String | true     | The format of the Credential.                       |
| credential_status       | String | true     | The status of the Credential.                       |
| credential_decoded      | JSON   | true     | The Credential in decoded format.                   |
| credential_encoded      | String | true     | The Credential in encoded format.                   |
| organization_identifier | String | true     | The identifier of the organization.                 |
| updated_at              | Date   | true     | The date when the Credential Procedure was updated. |


> NOTE: The `credential_status` can be one of the following values: `WITHDRAWN`, `ISSUED`, `VALID`, `REVOKED`, and `EXPIRED`.


## Deferred Credential Metadata

| Field             | Type   | Required | Description                                 |
|-------------------|--------|----------|---------------------------------------------|
| id                | UUID   | true     | The id of the Deferred Credential Metadata. |
| transaction_code  | String | true     | The Transaction Code.                       |
| auth_server_nonce | String | true     | The Pre-Authorized Code.                    |
| transaction_id    | String | true     | The id of the Transaction.                  |
| procedure_id      | UUID   | true     | The id of the Credential Procedure.         |
| vc                | String | true     | The Credential in encoded format.           |
| vc_format         | String | true     | The format of the Credential.               |


# Formats

## Date Format

[//]: # (todo: add the standard date format decided by the team)


# Credential Issuer - SaaS

## 1. Credential Issuer: Home Page (landing page) - public - Front

![Credential Issuer Mockup](./images/mockup/mockup-home-page.png)

## 2. Credential Issuer: Log in or Sign up - public - Front, Auth Server

## 3. Credential Issuer: Credential Management - private - Front, Back

![Credential Management Mockup](./images/mockup/mockup-credential-management-page.png)

## 4. Credential Issuer: Create Credential - private - Front, Back

![Create Credential Mockup](./images/mockup/mockup-create-credential-page.png)

## 5. Credential Issuer: Credential Details - private - Front, Back

![Create Credential Mockup](./images/mockup/mockup-create-credential-page-2.png)

## 6. Credential Issuer: Credential Offer - public - Front, Back

![Credential Offer Mockup](./images/mockup/mockup-credential-offer-page.png)

## 7. Credential Wallet: Credentials Page - private - Front, Back

![Wallet Credentials Page Mockup](./images/mockup/mockup-wallet-credential-page.png)

# API and Contracts

## 1. Credential Issuer: Credential Offer - public - Front, Back

> NOTE: The `Log in or Sign up` button starts an OIDC protocol which uses the client configuration parameters to start the communication with the Keycloak instance. For further information, please refer to the [Keycloak documentation](https://www.keycloak.org/docs/latest/server_admin/index.html#_client-registration) and [Angular Auth OIDC Client](https://www.npmjs.com/package/angular-auth-oidc-client).

## 2. Credential Issuer: Credential Management - private - Front, Back

## 3. Credential Issuer: Create Credential - private - Front, Back

### GetAllCredentialsByOrganization()

[//]: # (todo: look for page and sorting parameters communication between front and back)

This is a non-normative example of the request:

```curl
GET /api/v1/credentials HTTP/1.1
Host: issuer.dome-marketplace.eu
Authorization bearer eyJhbGciOiJSUzI1...NiIsInR5cCI6IkpsHQ
```

This is a non-normative example of the response:

```curl
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
   "credential_procedures": [
      {
         "credential_procedure": {
            "procedure_id": "82ea95d1-4522-47bd-8e05-a1f699cab3a7",
            "full_name": "Matt Smith",
            "status": "withdrawn",
            "updated": "2021-09-01T12:00:00Z"
         }  
      },
      {
         "credential_procedure": {...}
      }
   ]
}
```

## 4. Credential Issuer: Create Credential - private - Front, Back

> NOTE: The `power_onboarding` has a single `tmf_action`, `execute`. On the other hand, the `power_product_offering` has multiple `tmf_action`, `create`, `update`, and `delete`.

### CreateLEARCredentialEmployee()

This is a non-normative example of the request:

```curl
POST /api/v1/credentials?type="credential_type" HTTP/1.1
Host: issuer.dome-marketplace.eu
Authorization bearer eyJhbGciOiJSUzI1...NiIsInR5cCI6IkpsHQ
Content-Type: application/json

{
   "credential": {
      "mandator": {
            "commonName": "IN2",
            "country": "ES",
            "emailAddress": "rrhh@in2.es",
            "organization": "IN2, Ingeniería de la Información, S.L.",
            "organizationIdentifier": "VATES-B60645900",
            "serialNumber": "B60645900"
      },
      "mandatee": {
         "email": "oriol.canades@in2.es",
         "first_name": "Oriol",
         "gender": "M",
         "last_name": "Canadés",
         "mobile_phone": "+34666336699"
      },
      "power": [
         {
            "tmf_action": "Execute",
            "tmf_domain": "DOME",
            "tmf_function": "Onboarding",
            "tmf_type": "Domain"
         },
         {
            "tmf_action": [
               "Create",
               "Update"
            ],
            "tmf_domain": "DOME",
            "tmf_function": "ProductOffering",
            "tmf_type": "Domain"
         }
      ]
   }
}
```

## 5. Credential Issuer: Credential Details - private - Front, Back

This is a non-normative example of the request:

```curl
GET /api/v1/procedure/{procedure_id}/credentials HTTP/1.1
Host: issuer.dome-marketplace.eu
Authorization bearer eyJhbGciOiJSUzI1...NiIsInR5cCI6IkpsHQ
```

This is a non-normative example of the response:

```curl
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
   "procedure_id: "82ea95d1-4522-47bd-8e05-a1f699cab3a7",
   "credential_status": "withdrawn",
   "credential": {...}
}
```

> NOTE: The `credential` attribute is the Credential in decoded JSON format.

> NOTE: The mockup has `Sing Credential` and `Revoke` buttons, but these buttons are not implemented in the DOME Profile.

> NOTE: The `Send reminder` button will be activated when the `credential_status` attribute is set as `withdrawn` and `pend_donwload`. 

## 5. Credential Issuer: Credential Offer - public - Front, Back

> NOTE: Although the mockup shows the credential details, we do not implement it in our solution because it could incur a security risk just because we are exposing the sensitive data in a public context.

### GetCredentialOfferByTransactionCode(transaction_code)

This is a non-normative example of the request:

```curl
GET /api/v1/credential-offer/{transaction_code} HTTP/1.1
Host: issuer.dome-marketplace.eu
```

This is a non-normative example of the response:

```curl
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
   "credential_offer_uri: "https://issuer.dome-marketplace.eu/credentials/1234865"
}
```

# Data Lifecycle

## Procedure Lifecycle

```mermaid
graph TD
    A[Withdrawn] --> B[Issued]
    B --> C[Pend. Download]
    C --> D[Valid]
    D --> E[Revoked / Expired]
```

## Credential Lifecycle

```mermaid
graph TD
    A[Issued] --> B[Valid]
    B --> C[Revoked / Expired]
```

# Security

> NOTE: All the information managed by the frontend application MUST be stored using browser cookies. We MUST NOT store any information in the browser's local storage or session storage.

[//]: # (todo: we need to find some way to verify that any library used in the frontend must not use local storage or session storage to manage application data)

