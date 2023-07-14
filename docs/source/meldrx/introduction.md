# Introduction

## What is MeldRx?

MeldRx is a platform through which healthcare data can be stored, managed, and accessed through a variety of APIs.
The platform is suitable for use by healthcare organizations for managing their patient data.
Applications may be built on top of the platform to further enhance its utility.

At a high level, MeldRx consists of the following components
1. **Data Vault** A Data Vault for storing healthcare data. The Data Vault can store both structured and binary data.
2. **Identity Management** A complete identity and access management system. Administrators, clinicians, and patients can register accounts within the system and access can be controlled accordingly.
3. **APIs** The entire system may be accessed through a comprehensive set of APIs.
4. **Apps** Apps can be built on top of MeldRx to further enhance the system and widen its capabilities.

.. image:: ../_images/meldrx-components.png

<div>
    <img src="../_images/meldrx-components" style="background-color:#343131; padding:20px; border-radius:10px;" />
</div><div style="padding-bottom:20px;">

## Who Uses MeldRx?

### Health Systems

Health Systems, such as hospitals and clinics, can use MeldRx as a way of managing the data of their patient population and meeting all regulatory requirements.

### Providers/Clinicians

Clinicians can benefit from MeldRx by having a platform to document critical patient information and through which patients can share other health data. They also have access to any applications that may help them better care for their patients or have custom apps built if they are not happy with the ones that are available.

### Patients

Patients can use MeldRx to access and manage their personal health data.
They can aggregate health data from multiple organizations (health systems, insurance companies, etc.)
Patients are able to share their health data with others, such as clinicians and researchers.
And patients can use patient-facing SMART-on-FHIR apps to better understand their health data.

### App Developers

MeldRx enables developers to build integrated healthcare applications.
Developers can build apps targeted toward patients, clinicians, or administrative users, payers, etc.

MeldRx provides a mechanism for developers to connect to external (non-MeldRx) FHIR sources like other EHRs and build apps that integrate with them.

### Researchers

Researchers may receive patient data for use in a research study.

### Payers

Payers can receive patient data for use in claims analysis or onboarding a new member.