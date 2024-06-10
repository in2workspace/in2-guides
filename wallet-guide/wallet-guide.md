![IN2, Ingeniería de la Información](../assets/images/in2-logo.png)

# deprecated --> new guide: https://github.com/DOME-Marketplace/onboarding/tree/onboarding-guide

[//]: # (# Wallet Guide)

[//]: # ()
[//]: # (Wallet Guide is a documentation which implements all the needed info to implement our wallet as a solution.)

[//]: # ()
[//]: # (## Table of Contents)

[//]: # (<!-- TOC -->)

[//]: # (- [Wallet Guide]&#40;#wallet-guide&#41;)

[//]: # (  - [Table of Contents]&#40;#table-of-contents&#41;)

[//]: # (  - [Introduction]&#40;#introduction&#41;)

[//]: # (  - [Wallet]&#40;#wallet&#41;)

[//]: # (  - [Wallet API]&#40;#wallet-api&#41;)

[//]: # (  - [Wallet Demo]&#40;#wallet-demo&#41;)

[//]: # (  - [Wallet UI]&#40;#wallet-ui&#41;)

[//]: # (  - [Wallet Security]&#40;#wallet-security&#41;)

[//]: # (  - [Wallet Testing]&#40;#wallet-testing&#41;)

[//]: # (  - [Wallet Deployment]&#40;#wallet-deployment&#41;)

[//]: # (  - [Wallet Maintenance]&#40;#wallet-maintenance&#41;)

[//]: # (  - [Wallet Troubleshooting]&#40;#wallet-troubleshooting&#41;)

[//]: # (  - [Wallet FAQ]&#40;#wallet-faq&#41;)

[//]: # (  - [Wallet Glossary]&#40;#wallet-glossary&#41;)

[//]: # (<!-- /TOC -->)

[//]: # ()
[//]: # (## Introduction)

[//]: # ()
[//]: # (This guide is intended to provide a comprehensive overview of the IN2 Wallet and its components. It is designed to be a reference for developers, testers, and other stakeholders who are involved in the development, testing, deployment, and maintenance of the wallet.)

[//]: # ()
[//]: # (## Wallet)

[//]: # ()
[//]: # (The IN2 Wallet is a digital wallet that allows users to store, send, and receive digital assets such as verifiable credentials and presentations. It is designed to be a secure, user-friendly, and scalable solution that can be used by individuals, businesses, and other organizations.)

[//]: # ()
[//]: # (The wallet enterprise solution id designed to be a modular and extensible platform that can be customized to meet the specific needs of different users and use cases. It is built on top of a set of open standards and protocols, and it is designed to be interoperable with other issuer and verifier solutions.)

[//]: # ()
[//]: # (Our Wallet solution implements two principal components that can be integrated with other systems making it a versatile solution for different use cases. The current overview of the wallet components is as follows:)

[//]: # ()
[//]: # (![Wallet Overview]&#40;./assets/wallet-solution-context.png&#41;)

[//]: # ()
[//]: # (For now, we only support the following components:)

[//]: # (* **IAM &#40;Identity and Access Management&#41;** )

[//]: # (  * Keycloak)

[//]: # (* **Context Broker**)

[//]: # (  * Orion-LD )

[//]: # (  * Scorpio )

[//]: # (  * Other NGSI-LD compliant context broker)

[//]: # (* **Vault**)

[//]: # (  * HashiCorp Vault)

[//]: # (  * Azure Key Vault)

[//]: # (* **Application Configuration**)

[//]: # (  * YAML)

[//]: # (  * Azure App Configuration)

[//]: # ()
[//]: # (## Wallet DEMO)

[//]: # ()
[//]: # (This section will guide you through the steps to access the Dome Marketplace using the VC Login option, utilizing a demo user account for demonstration purposes.)

[//]: # ()
[//]: # (### Step 1: Access DOME Marketplace)

[//]: # ()
[//]: # (Visit the Dome Marketplace by clicking on the following link: [DOME Marketplace]&#40;https://dome-marketplace.org&#41;.)

[//]: # ()
[//]: # (![DOME Marketplace Access]&#40;./assets/DOME_Marketplace_Access.png&#41;)

[//]: # ()
[//]: # (### Step 2: Sign In)

[//]: # ()
[//]: # (1. Click on the **Sign In** button.)

[//]: # (2. Select **VC Login** from the options available.)

[//]: # ()
[//]: # (![Sign In and Select VC Login]&#40;./assets/Sign_In_And_Select_VC_Login.png&#41;)

[//]: # ()
[//]: # (A QR code will be displayed on the screen. This QR code is unique to your login session and should be scanned using the DOME Wallet app.)

[//]: # ()
[//]: # (> **Note**: Do not attempt to scan the example QR code below, as it is only for demonstration purposes. Each QR code is unique and must be scanned during the current session to ensure security.)

[//]: # ()
[//]: # (![QR Code Display]&#40;./assets/QR_Popup.png&#41;)

[//]: # ()
[//]: # (### Step 3: Log into DOME Wallet)

[//]: # ()
[//]: # (1. Go to the DOME Wallet website: [DOME Wallet]&#40;https://dome-wallet.eu&#41;.)

[//]: # (2. Log in using the demo user credentials:)

[//]: # (   - Username: `userWallet`)

[//]: # (   - Password: `userPass`)

[//]: # ()
[//]: # (![Wallet Login]&#40;./assets/Wallet_Login.png&#41;)

[//]: # ()
[//]: # (Upon logging in, navigate to the **Credentials** tab to view the VC that will be used for this demo.)

[//]: # ()
[//]: # (![Credentials Tab with VC]&#40;./assets/DOME_Wallet_Credentials_Page.png&#41;)

[//]: # ()
[//]: # (### Step 4: Scan QR Code)

[//]: # ()
[//]: # (Go back to the **Home** screen and press the **Scan QR** button to scan the QR code displayed on the Dome Marketplace login page.)

[//]: # ()
[//]: # (![Scan QR Button]&#40;./assets/DOME_Wallet_Home_Page.png&#41;)

[//]: # ()
[//]: # (### Step 5: Complete Attestation Exchange)

[//]: # ()
[//]: # (After scanning the QR code, the Wallet and Verifier initiate the AttestationExchange process and in the wallet, you will be asked to select the credential you wish to present to enter the portal. In this example case, as we only have one, we will use that. Once selected, a pop-up will appear to confirm if we want to use that credential.)

[//]: # ()
[//]: # (![Verifiable Credential Selection]&#40;./assets/DOME_Wallet_Select_Credential.png&#41;)

[//]: # ()
[//]: # (Upon successful completion, you will be redirected and logged into the marketplace.)

[//]: # ()
[//]: # (![Attestation Exchange Success]&#40;./assets/VC_Login_Success.png&#41;)

[//]: # ()
[//]: # (The AttestationExchange process has concluded successfully, and you are now logged into the Dome Marketplace. You have full access to explore its functionalities and features available for demo users.)

[//]: # ()
[//]: # (### Troubleshooting: Camera Not Activating for QR Scan)

[//]: # ()
[//]: # (If the camera does not activate after pressing the **Scan QR** button, follow these steps:)

[//]: # ()
[//]: # (#### Navigate to Settings)

[//]: # ()
[//]: # (Within the DOME Wallet app, go to the **Settings** section.)

[//]: # ()
[//]: # (#### Access Camera Settings)

[//]: # ()
[//]: # (Find and select the **Camera** option to configure your camera settings.)

[//]: # ()
[//]: # (![Navigate to Settings]&#40;./assets/DOME_Wallet_Settings_Page.png&#41;)

[//]: # ()
[//]: # (#### Select Your Camera)

[//]: # ()
[//]: # (Choose your preferred camera for QR scanning. If you have more than one camera, you may need to select which one to use.)

[//]: # ()
[//]: # (![Camera Settings]&#40;./assets/Dome_Wallet_Camera_Selector.png&#41;)

[//]: # ()
[//]: # (#### Retry QR Scan)

[//]: # ()
[//]: # (Return to the home screen and attempt to scan the QR code again.)

[//]: # ()
[//]: # (By following these steps, you should be able to resolve the issue with the camera not activating for QR code scanning.)

[//]: # ()
[//]: # ()
[//]: # (## Wallet API)

[//]: # ()
[//]: # (This section will provide an overview of the wallet API and its components.)

[//]: # ()
[//]: # (## Wallet UI)

[//]: # ()
[//]: # (This section will provide an overview of the wallet UI and its components.)

[//]: # ()
[//]: # (## Wallet Security)

[//]: # ()
[//]: # (This section will provide an overview of the wallet security and its components.)

[//]: # ()
[//]: # (## Wallet Testing)

[//]: # ()
[//]: # (This section will provide an overview of the wallet testing and its components.)

[//]: # ()
[//]: # (## Wallet Deployment)

[//]: # ()
[//]: # (This section will provide an overview of the wallet deployment and its components.)

[//]: # ()
[//]: # (### Docker Deployment)

[//]: # ()
[//]: # (We have an IaC &#40;Infrastructure as Code&#41; repository that contains the necessary files to deploy the wallet solution using Docker. The repository is located at the following URL: https://github.com/in2workspace/in2-dome-iac/blob/main/wallet/test/README.md)

[//]: # ()
[//]: # (### Kubernetes Deployment)

[//]: # ()
[//]: # (We are currently working on a Kubernetes deployment guide for the wallet solution. You can check our Helm repository at the following URL: https://in2workspace.github.io/helm-charts/)

[//]: # ()
[//]: # ()
[//]: # (## Wallet Maintenance)

[//]: # ()
[//]: # (This section will provide an overview of the wallet maintenance and its components.)

[//]: # ()
[//]: # (## Wallet Troubleshooting)

[//]: # ()
[//]: # (This section will provide an overview of the wallet troubleshooting and its components.)

[//]: # ()
[//]: # (## Wallet FAQ)

[//]: # ()
[//]: # (This section will provide an overview of the wallet FAQ and its components.)

[//]: # ()
[//]: # (## Wallet Glossary)

[//]: # ()
[//]: # (This section will provide an overview of the wallet glossary and its components.)

[//]: # ()
[//]: # ()
[//]: # (<!-- Contributors: Please do not add anything below this line -->)

