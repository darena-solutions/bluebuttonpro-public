# Sandboxes

**Sandboxes** are environments that are used for testing your application.
They can be interacted with via a *FHIR API* and will often contain sample data that can be useful for testing.

Sandboxes can come in many shapes and sizes.
MeldRx provides a personalized sandbox as part of your developer account that you can use without restrictions.
Many *FHIR API Providers* also provide a sandbox to use for testing their FHIR APIs.

## MeldRx Sandbox

When you create an account on the *MeldRx DevSphere*, you are automatically provisioned your own personal *MeldRx Sandbox*.
This sandbox is a complete FHIR server that you can use to test your SMART-on-FHIR apps.

The *MeldRx Sandbox* comes pre-loaded with data so that you can begin testing immediately.
See the [MeldRx Sandbox](#technical-reference-meldrx-sandbox) reference for more details.

## Linked Sandbox

A *Linked Sandbox* is a *MeldRx Sandbox* that is linked to an external FHIR environment.
A *Linked Sandbox* can be configured to specify where requests are sent.
For example, a *Linked Sandbox* can be configured to read patient data from the external (linked) FHIR environment, but written to the MeldRx environment.
This is useful for situations where an external FHIR environment has restrictions, for example, when integrating with an EHR that doesn't allow apps to write data back.

### Creating a Linked Sandbox

TODO:

