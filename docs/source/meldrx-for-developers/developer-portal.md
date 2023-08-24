# Developer Portal

The *MeldRx Developer Portal* is the front door to building integrated Health IT applications.
From this portal, you will be able to manage, configure, and test all of your applications.

## Registering

## Workspaces

**Workspaces** are environments that are used for testing your application.
They can be interacted with via a *FHIR API* and will often contain sample data that can be useful for testing.

Workspaces can come in many shapes and sizes.
MeldRx provides a personalized workspace as part of your developer account that you can use without restrictions.
Many *FHIR API Providers* also provide a workspace to use for testing their FHIR APIs.

### MeldRx Workspace

When you create an account on the *MeldRx Dev Portal*, you are automatically provisioned your own personal *MeldRx Workspace*.
This workspace is a complete FHIR server that you can use to test your SMART-on-FHIR apps.

The *MeldRx Workspace* comes pre-loaded with data so that you can begin testing immediately.
See the [MeldRx Workspace](#technical-reference-meldrx-workspace) reference for more details.

The *MeldRx Workspace* exists so that developers can test their applications on the MeldRx platform. No real data should be entered into this environment. The workspace environment is also updated more frequently than the production environment. It is possible that some features exist in the workspace environment but have not met our quality assurance guidelines to move on to the production environment just yet.

### Linked Workspace

A *Linked Workspace* is a *MeldRx Workspace* that is linked to an external FHIR environment.
A *Linked Workspace* can be configured to specify where requests are sent.
For example, a *Linked Workspace* can be configured to read patient data from the external (linked) FHIR environment, but written to the MeldRx environment.
This is useful for situations where an external FHIR environment has restrictions, for example, when integrating with an EHR that doesn't allow apps to write data back.

#### Creating a Linked Workspace

TODO:


## Apps

An App is any application that needs to connect to a FHIR server to read and/or write data.

### Creating an App

### Linked Apps

#### Creating a Linked App
NOTE: See our EHR Guides for help with this

#### Linked App Configuration

### Developing Your App
When you use MeldRx, the complexity of SMART-on-FHIR is completely abstracted away.

### Testing Your App