# ONDC SDK LOGISTICS APP

A logistics application refers to an application that enables retail seller applications deliver orders placed by retail buyers . This application includes capturing orders from sellers, giving quotes of delivery, assigning delivery agents, tracking and managing the deliveries through the delivery agents and handling returns.

# Release Notes

| **Version** | **Date** | **Changes** |
|---|---|---|
| **v1.1.0** |October 12, 2023  | - Order Assignment to driver on the basis of their location <br>- Real-time Location tracking  and updating frequently (5 sec) and accurately <br>- Sharing Live location via tracking link <br>- Real-time map view of the driver’s location <br>- Delivery location to be shown as the given address of the buyer <br>- Hubs Module for P2H2P delivery <br>- Serviceability for Hyperlocal and Intercity delivery <br>- Price calculation for Hyperlocal delivery <br>- Backward compatibility with both the versions v1.1.0 and v.1.2.0 <br>
| **v1.0.0** | 15th Sep 2023 | - Added P2P support<br>- Added PWA support<br>- Added support for Logistics v1.2.0 contract (/search, /init, /confirm, /update, /status, /track) and related callbacks<br>- Added support for IGM APIs (/issue, /on_issue)<br>- Added tasks view for viewing active, ongoing and completed tasks<br>- Added driver and admin view for managing status of invited and onboarded drivers<br>- Added issues view for managing issues raised in post order journey<br>- Added inviting view for adding administrators and delivery agents to the application<br>- Added role based login to application<br>- Added support for return to origin deliveries<br>- Added cancellation flow for triggering RTO/disposal<br>- Added track URL for post order tracking for buyer applications |

## Documentation

[White Label Document](https://docs.google.com/document/d/1NH-G4-C8h7yztbMT0Lncr0fN8G6T4dcQV6jxz2hpHcs)


## Links

[Staging](https://ref-logistics-app-stage.ondc.org/) || [Pre Prod](https://ref-logistics-app-preprod.ondc.org/)

## Login Credientials

1. Admin Login: [link](https://ref-logistics-app-preprod.ondc.org/login)
    - email:`refappsupport1@gmail.com | refappsupport2@gmail.com | refappsupport3@gmail.com | refappsupport4@gmail.com | refappsupport5@gmail.com`
    - password: Ondc@1234!

2. Driver login (PWA): [link](https://ref-logistics-app-preprod.ondc.org/driver/login)
    - default number: 8755515001 | 8755515002 | 8755515003 | 8755515004 | 8755515005
    - default OTP: 0701 (default for all)



## Tech Stack

| Type         | Technologies                                                                            |
|--------------|-----------------------------------------------------------------------------------------|
| **Client**   | ReactJS, Typescript, React Query, Axios |
| **Server**   | NodeJS(ExpressTS), Python(Flask), Nginx, Docker Microservices, RabbitMQ, S3, GoogleSMTP |
| **Database** | MongoDB(ORM Mongoose), Postgres(for Protocol Layer)|

ONDC's logistics app uses a modern technical stack for web, mobile and server-side development. The client layer is served as two applications.
The first serves the Admin console for monitoring and managing the LSP web application.
The second serves the Driver application which is monitored as a PWA web application accessible on any web enabled device screen.

The consoles are built on ReactJS v17 for management of the application layer. The backend of the application is built on the NodeJS v16 runtime, hosted on various docker containers.

The database used for application data storage is MongoDB with Mongoose ORM for querying and mutations.

A RabbitMQ Service container is used as the system bus for passing events and messages between various services.

This repo is ONDC logistics App is developed with microservice architecture
which contains

- protocol layer(python)
- client API layer (node js)
- front app(react) being served via nginx
- ancillary API (python) - flask apis
composed together with docker-compose.yaml

## For Whom

- wants to refer the logistics app
- wants the same setup to be available in their infra
- pick any component of it and use separately
- know the bare minimum structure to add and modify their own application

## Installation

1. Clone the git Repository

```bash
    git clone <url>
```

2. Make sure you've pulled all sub-directories .

```bash
    git submodule init
    git submodule update --remote --recursive
```

3. Copy environment variables from env.example file to env.local

```bash
    cp .env.example .env.local
```

4. Run docker command to start the logistics application

```bash
    docker-compose -f docker-compose-for-local.yaml --env-file .env.local up -d 
```

## Environment Variables

To get Environment Variables, Registration is required to access certain services.

### Server-side environment

**A. Protocol Web Server**
The protocol web server is the service communicating with the network. This service is running as its own container and requires the following keys.

```
BPP_PRIVATE_KEY: ${BPP_PRIVATE_KEY}
BPP_PUBLIC_KEY: ${BPP_PUBLIC_KEY}
BPP_UNIQUE_KEY_ID: ${BPP_UNIQUE_KEY_ID}
BPP_ID: ${BPP_ID}
```

Alongside these the protocol storage are using a postgres instance which require the following keys.

```
PG_HOST: ${PG_HOST}
PG_PORT: ${PG_PORT}
PG_DATABASE: ${PG_DATABASE}
PG_USER: ${PG_USER}
PG_PASSWORD: ${PG_PASSWORD}
```

The consumer end of the service has all the above keys with an additional key for identification of the consumer endpoint.

```
BPP_CLIENT_ENDPOINT: ${BPP_CLIENT_ENDPOINT}
```

**B. Google SMTP Service**
For mailing and invites to admins and delivery agents, we are using the GoogleSMTP service. The developer using this repository can select their own mailing service.

```
SMTP_HOST: ${SMTP_HOST}
SMTP_PORT: ${SMTP_PORT}
SMTP_AUTH_USERNAME: ${SMTP_AUTH_USERNAME}
SMTP_AUTH_PASSWORD: ${SMTP_AUTH_PASSWORD}
SMTP_EMAIL_SENDER: ${SMTP_EMAIL_SENDER}
SMTP_EXCEPTION_EMAIL_RECIPIENTS: ${SMTP_EXCEPTION_EMAIL_RECIPIENTS}
```

For receiving notifications as a super-admin we need to set a mail id in the environment for all events occuring in the web application.

```
LOGISTICS_ADMIN_EMAIL: ${LOGISTICS_ADMIN_EMAIL}
```

**D. S3 Bucket credentials with AWS access key**
S3 Bucket for storage of images uploaded by the delivery agent for confirmations at different parts of the delivery journey.

```
S3_ACCESS_KEY=${S3_ACCESS_KEY}
S3_SECRET_KEY=${S3_SECRET_KEY}
S3_BUCKET=${S3_BUCKET}
S3_REGION=${S3_REGION}
S3_VERSION=${S3_VERSION}
```

**E. MapMyIndia API keys(Optional)**
MapMyIndia (will be used in the future) for all positioning and location related details.

- Onboarding agent for home location
- Locating agent in deliveries
- Locating pickup and drop locations

```
MMI_CLIENT_ID: ${MMI_CLIENT_ID}
MMI_CLIENT_SECRET: ${MMI_CLIENT_SECRET}
MMI_ADVANCE_API_KEY: ${MMI_ADVANCE_API_KEY}
```

**F. Grafana Service (optional)**
An optional monitoring service can be used to look at the call structures for all the calls taking place on the network. For Grafana, please start the service and place the following keys in the environment. Grafana Loki can be used for aggregating logs from the application.

```
LOKI_HOST: ${LOKI_HOST}
```

### Client-side Environment

The client is kept as light as possible, shifting all computational load to the server. For this reason the react client only requires the following keys.

```
REACT_APP_BASE_URL=${BASE_URL for react application}
REACT_APP_ALLOWED_DOMAINS=${Allowed domains for the backend server}
REACT_APP_MASTER_EMAIL=${The master email should be an admin email for which the password cannot be changed(optional)}
```

## Generate Payload For ONDC Registration

```bash
{
    "country": "IND",
    "city": "*",
    "type": "BPP",
    "subscriber_id": "YOUR_HOSTED_DOMAIN",
    "subscriber_url": "YOUR_API_ENDPOINT",
    "domain": "nic2004:60232",
    "signing_public_key": "SIGNING_PUBLIC_KEY",
    "encr_public_key": "CRYPTO_PUBLICKEY",
    "created": "0000-00-00T00:00:00.000000Z"
    "valid_from": "0000-00-00T00:00:00.000000Z",
    "valid_until": "0000-00-00T00:00:00.000000Z",
    "updated": "0000-00-00T00:00:00.000000Z"
}
```

**Note**: ```"created": "2023-03-30T15:27:45.123456Z"```
(The date format is OpenAPI date-time notation)

```BPP_PRIVATE_KEY=``` This will be ```SIGNING_PRIVATE_KEY``` generated by the [utility](https://github.com/ONDC-Official/reference-implementations/tree/main/utilities/signing_and_verification).

```BPP_PUBLIC_KEY=```This will be ```SIGNING_PRIVATE_KEY```

```BPP_UNIQUE_KEY_ID=```  This will be ukId which we will get after subscribing.

```BPP_ID=``` This will be the ```HOSTED_DOMAIN```that you are sharing in the payload as the value of subscriber_id.

## Payload Submission To ONDC Staging Registry

Visit this [Google Form](https://docs.google.com/forms/d/e/1FAIpQLSdz5-LLGX4m_pOQNFstoZQd5zhb68md_9zoX-dC8N8j2DABbA/viewform) and fill it out carefully.

In return you will get payload with additional data which will include ukId.

```BPP_UNIQUE_KEY_ID=``` will be ukId which we will get after subscribing.

## To run all the services

```bash
docker-compose -f docker-compose-for-local.yaml --env-file .env-local up -d.
```

# Running the application for the first time

After following the steps in setting up the application and setting all the environment variables correctly, we can access the client admin application on the on the node localhost serving the application.

On the first access to the frontend, the developer will encounter the following screens.

[Staging URL](https://ref-logistics-app-stage.ondc.org/)

1. Login screen: [link](https://ref-logistics-app-stage.ondc.org/)
    - username: process.env.LOGISTICS_ADMIN_EMAIL (<refappsupport@ondc.org>)
    - password: Ondc@1234!

    Driver login (PWA): [link](https://ref-logistics-app-stage.ondc.org/driver/login)
    - default number: 87555150[01-25] (driver numbers from with 01 to 25 are seeded)
    - default OTP: 0701 (default for all)

2. After login, you will be met with an invitation screen to add
    - Admin
    - Logistics agents

    By filling up the relevant information, you will be able to invite Admins, and Agent to your application.

3. On Admin-side, an email is received for creating a new password and logging into the application. This applies to the agent as well.

4. After logging in as an Admin, you are greeted with a dashboard with the following components
    - Sidebar navigation to Driver and Admin management
    - Tasks navigation to check the currently active, ongoing and completed tasks
    - Issues for checking the issues raised for completed and ongoing tasks
The dashboard shows the summary of all the above tabs.
