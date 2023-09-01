# Developer Portal

The *MeldRx Developer Portal* is the front door to building integrated Health IT applications.
From this portal, you will be able to manage, configure, and test all of your applications.

## Registering

To get started, register for a new Developer account at [https://app.meldrx.com](https://app.meldrx.com).

(meldrx-workspace)=
## Workspaces

**Workspaces** are what your applications will connect to.
They include a Data Vault and a complete FHIR API.
You may create as many workspaces as you need.

### Linked Workspace

A *Linked Workspace* is a Workspace that is linked to an external FHIR environment.
A *Linked Workspace* can be configured to specify where requests are sent.
For example, a *Linked Workspace* can be configured to read patient data from the external (linked) FHIR environment, but written to the MeldRx environment.
This is useful for situations where an external FHIR environment has restrictions, for example, when integrating with an EHR that doesn't allow apps to write data back.

#### Creating a Linked Workspace
1. Login to the Developer Portal
2. Go to your workspaces (`Command Center > My Workspaces`)
3. Create a new Workspace
4. To link to an external FHIR server, select a FHIR Provider and enter the endpoint of the FHIR server you want to link to

(meldrx-app)=
## Apps

An App is any application that needs to connect to a FHIR server to read and/or write data.

### Creating an App
1. Login to the Developer Portal
2. Go to your apps (`Command Center > My Apps`)
3. Create a new app

### Linked Apps

Linked apps provide a mechanism for you to manage your application credentials for other EHRs.
Once you have entered your app credentials as _Linked Apps_, you can then forget them and work stricly with your MeldRx App.

#### Creating a Linked App
1. Register an app with the EHR(s) that you want to link to
    - Note: See our [EHR Guides](ehr-guides) for help with this step
2. Login to the Developer Portal
3. Go to your apps (`Command Center > My Apps`)
4. Select an app
5. Add Linked App(s)