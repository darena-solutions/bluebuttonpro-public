Getting Started
===============

To get started with the BlueButtonPRO platform, you must first contact our sales department. During
that process, they will have created a new organization for you and will have created a user account
that will be listed as the administrator of that organization. A FHIR server will also be provisioned
for this organization.

.. _getting-started-smart-on-fhir:

SMART-on-FHIR
-------------

We follow the `SMART-on-FHIR <http://docs.smarthealthit.org/>`_ specification. The applications that
are registered here are considered SMART apps. These applications must follow the application launch
guidelines specified in `SMART App Launch Framework <http://hl7.org/fhir/smart-app-launch/index.html>`_.

We also implement the bulk export specification described in the `FHIR Bulk Data Access Implementation
Guide <https://hl7.org/fhir/uv/bulkdata/index.html>`_. An application cannot both be a standalone or
EHR launch application type and also be a bulk export application. Separate applications will need to
be created for those scenarios.

Creating an Application
-----------------------

Once the initial setup is complete with our sales team, you will now need to create an application/client
that will interact with the BlueButtonPRO API. We have built a UI to make this process easier and is
available at https://developer.darenasolutions.com.

Once you have navigated to that site and logged in with your new account, you should be at the home
page where it lists all of your existing applications. There shouldn't be any there at this time. Click
on the **+** button on the top right to add a new application:

This will open up the editor where you enter in the information about your application.

Basic Information
^^^^^^^^^^^^^^^^^

Client Name - **REQUIRED**
   This is the human readable client name and will be displayed to all of your users in the consent
   screen when they log in.

Client URI
   This is a URL for your application. Generally, we recommend this to be the URL to the homepage of
   a site that describes your application and its capabilities.

Logo URI
   This is the URL that contains the logo for your application. This will be displayed on the consent
   screen. If a logo is not provided, only the client name is displayed in the consent screen.

Registration Type
   The type of application you are creating. These follow the :ref:`SMART-on-FHIR <getting-started-smart-on-fhir>`
   application types. You can select the Standalone/EHR launch application type, or bulk export application
   type.

Client Type
   The client type is determined by how your application is hosted and what kind of application it is.
   If your application is a javascript, mobile, or desktop application, then the source code is available
   and accessible by the user. This type of application is a **Public** application.

   If the client is a web application where the source code is hosted on a server and not accessible
   by users, then this type of application is a **Private** application. In this situation, a client
   secret will be generated for the application.

   If the registration type is **Bulk Export**, then this value will remain **Public**.

JWKs URL
   This value is only applicable for bulk export applications. In this case, a URL to your JSON Web
   Keys is required. We use this URL to retrieve the public key that validates the JWT token provided
   by the application when retrieving an access token for bulk export. This process is described in
   the `SMART Backend Services <https://hl7.org/fhir/uv/bulkdata/authorization/index.html>`_ document.

Redirect URLs
^^^^^^^^^^^^^

Add one or more redirect URLs for the application here. This is where the server will redirect to with
authentication/authorization details.

Only a maximum of 5 redirect URLs are supported.

Post Logout Redirect URLs
^^^^^^^^^^^^^^^^^^^^^^^^^

Add one or more post logout redirect URLs for the application here. The application can perform a logout
process by interacting with our end session endpoint: https://oauth.darenasolutions.com/connect/endsession.
If the user is successfully logged out and a post logout URL is provided, then our identity provider
will redirect the user to that post logout URL.

For more information on how the logout process works, refer to the `OpenID Connect Specification <https://openid.net/specs/openid-connect-rpinitiated-1_0.html>`_.

Only a maximum of 5 post logout URLs are supported.

Finalizing
^^^^^^^^^^

Once the information has been entered into the editor, click on the **OK** button and your application
should now be created. If your application type was a **Private** application, then you should also
see a popup that contains your client secret. It is important that you take this secret and store it
in a secure location. Once the popup is closed, this secret can no longer be obtained. If the secret
is lost or forgotten, a new secret will need to be generated.

At this point, your application can begin using the BlueButtonPRO API in the sandbox environment. The
homepage should be updated to reflect that you have added an application, and your client ID should
be visible. You must use this client id to authenticate with our identity provider.

Dynamic Registration
--------------------

The process for creating an application with the UI follows the `dynamic registration specification
<https://tools.ietf.org/html/rfc7591>`_. This means that everything the UI has done, can also be done
through our dynamic registration endpoint: https://oauth.darenasolutions.com/api/DynamicRegistrations.


Production - Submit for Review
------------------------------

TODO