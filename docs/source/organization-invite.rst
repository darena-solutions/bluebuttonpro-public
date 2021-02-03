Organization Invites
====================

Organization invites allow an organization to link a user to a particular patient in a FHIR server with
read-only access. The user can also be newly registered through this method.

.. _organization-invite-creating-an-invite-security-question-and-answer:

Creating an Invite - Security Question & Answer
-----------------------------------------------

To create an invite that will register a new user without knowing the user's email address, the endpoint
at https://api.bluebuttonpro.com/OrganizationInvites/security-details/create should be used. This endpoint
takes in the following parameters:

securityQuestion - **REQUIRED**
   This is the security question the user will need to answer before they can register. This security
   question will need to be displayed later once the user is ready to register an account. See :ref:`organization-invite-registering-a-user-by-security-question-and-answer`
   for more information.

securityAnswer - **REQUIRED**
   This is the answer to the security question above. This will need to be entered in by the user once
   they are ready to register an account. See :ref:`organization-invite-registering-a-user-by-security-question-and-answer`
   for more information.

.. _organization-invite-accessible-patient-id:

accessiblePatientId
   This is the id of the FHIR patient resource in the organization's FHIR server. This will be the patient
   that the user will be linked to and have read-only access. If the id of the patient is not known,
   a search can be performed instead by using the *accessiblePatientIdentifierSearchStr* parameter.

.. _organization-invite-accessible-patient-identifier-search-str:

accessiblePatientIdentifierSearchStr
   If the patient id is not known, this parameter can be used. The value of this parameter should be
   a standard token based search parameter that is typically used in FHIR related searches. This is
   explained here in the `official FHIR documentation <https://www.hl7.org/fhir/search.html#token>`_.
   
   As an example, to search for a patient by social security number, the value of this parameter might
   look like this: ``http://hl7.org/fhir/sid/us-ssn|444222222``. Note that in this value, both the system
   and value is given delimited by the pipe, ``|``, character. However, it is also possible to just specify
   the social security number: ``444222222``. In this case, a search on only the value will be performed
   and the system will be ignored.

   .. note::
   
      The search must result in only one patient. If more than one patient is found by executing the search,
      an error will be returned by the API.

In addition to the body of the request, a :doc:`FHIR server context <header-values>` header is required.

Once all this information is obtained, a request can be constructed:

.. code-block:: console
   
   POST https://api.bluebuttonpro.com/OrganizationInvites/security-details/create
   
   Content-Type: application/json
   Authorization: Bearer <token>
   FhirServerId-Context: d9c34349-3e81-41bf-8113-27bc01dcaa9d

   {
     "securityQuestion": "What is the name of your first pet?",
     "securityAnswer": "Charlie",
     "accessiblePatientId: "d1f84545-5981-4250-80d6-a99f8be0d716"
   }

If the operation was successful, the API will return a response that contains an 8 character alpha-numeric
security code along with a few other details:

.. code-block:: json

   {
     "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
     "createdOn": "01-01-2021",
     "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
     "securityCode": "ABC12345",
     "securityQuestion": "What is the name of your first pet?",
     "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",
     "fhirServerName": "Good Health Clinic",
     "patient": { }
   }

The ``securityCode`` will need to be given to the user. The user will then use this security code to
:ref:`register an account <organization-invite-registering-a-user-by-security-question-and-answer>`.

.. _organization-invite-creating-an-invite-user-email-address:

Creating an Invite - User Email Address
---------------------------------------

To create an invite with a specific email address, the following endpoint will need to be used:
https://api.darenasolutions.com/OrganizationInvites/user-details/create. This endpoint accepts the following
parameters:

userEmail - **REQUIRED**
   This is the user's email address.

firstName - **REQUIRED**
   This is the first name of the user. If a user with the given email address is not found in our system,
   then a new user will be registered with this first name. If the user is found, this parameter is
   ignored.

lastName - **REQUIRED**
   This is the last name of the user. If a user with the given email address is not found in our system,
   then a new user will be registered with this last name. If the user is found, this parameter is
   ignored.

accessiblePatientId
   See :ref:`accessiblePatientId <organization-invite-accessible-patient-id>`.

accessiblePatientIdentifierSearchStr
   See :ref:`accessiblePatientIdentifierSearchStr <organization-invite-accessible-patient-identifier-search-str>`.

In addition to the body of the request, a :doc:`FHIR server context <header-values>` header is required.

.. note::

   The user is created in our system and a confirmation email is sent only if the user does not already
   exist in our system. The user will have 24 hours to confirm their email address, after which they
   can no longer sign in. If the user already exists, then a registration does not occur and a confirmation
   email is not sent out.

Once all this information is obtained, a request can be constructed:

.. code-block:: console
   
   POST https://api.bluebuttonpro.com/OrganizationInvites/user-details/create
   
   Content-Type: application/json
   Authorization: Bearer <token>
   FhirServerId-Context: d9c34349-3e81-41bf-8113-27bc01dcaa9d

   {
     "userEmail": "user@example.com",
     "firstName": "Jane",
     "lastName": "Doe",
     "accessiblePatientId: "d1f84545-5981-4250-80d6-a99f8be0d716"
   }

.. _organization-invite-registering-a-user-by-security-question-and-answer:

Registering a User By Security Question & Answer
------------------------------------------------

If an invite was :ref:`created using a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`,
then a user can register an account by providing those values. Typically, the client should display
a window asking for the security code. Once the user enters in the security code, the security question
will need to be displayed to the user. To obtain the security question from the security code, the following
request will need to be sent to the API, assuming the security code is ``ABC12345``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/OrganizationInvites/security-details/code/ABC12345/security-question

This endpoint can be called anonymously and does not require an authenticated request. The API will
return a plain text response that contains the security question. This security question should be displayed
to the user. The user must then enter the answer to the security question and provide their user account
details so that the account can be created in our system. The security code, answer, and user details
should then be sent to the following endpoint to complete registration, assuming the security code is
``ABC12345``: https://api.bluebuttonpro.com/OrganizationInvites/security-details/code/ABC12345/register.

This endpoint accepts the following parameters:

securityCode - **REQUIRED**
   This is the security code that will identify the invite.

securityAnswer - **REQUIRED**
   This is the answer the user has provided for the given security question.

user - **REQUIRED**
   This value contaisn the user details needed to register and create the account.

   user.email - **REQUIRED**
      The email address of the user

   user.password - **REQUIRED**
      The password.

   user.confirmPassword - **REQUIRED**
      The password entered a second time. Should match the original password.

   user.firstName - **REQUIRED**
      The user's first name.

   user.middleName
      The user's middle name.

   user.lastName - **REQUIRED**
      The user's last name.

Once all the information is obtained, a request can be constructed:

.. code-block:: console

   POST https://api.bluebuttonpro.com/OrganizationInvites/security-details/code/ABC12345/register

   Content-Type: application/json

   {
     "securityCode": "ABC12345",
     "securityAnswer": "Charlie",
     "user": {
       "email": "user@example.com",
       "password": "P@ssw0rd123",
       "confirmPassword": "P@ssw0rd123",
       "firstName": "Jane",
       "lastName": "Doe"
     }
   }

If the operation was successful, two different types of responses can be returned by the API. If the
email address entered by the user does not already exist, then the account will be registered successfully
and the user can then accept the invite and link the patient with their account by following the steps
in :ref:`organization-invite-accepting-an-invite-user-email-address`. The invite is now associated with
that user's email address so only that user can accept the invite. This is what the response from the
api looks like:

.. code-block:: json

   {
     "userExists": false,
     "user": {
       "createdAt": "01-01-2021",
       "modifiedAt": "01-01-2021",
       "id": "e35819bf-6df0-447c-9484-3ff77029ac44",
       "firstName": "Jane",
       "lastName": "Doe",
       "email": "user@example.com",
       "active": true,
       "isSuperAdmin": false,
       "isOrganizationModerator": false,
       "isUserModerator": false,
       "organizationUserRelations": [ ]
     }
   }

.. note::

   A confirmation email will also be sent out to the registered email address. The user will have 24
   hours to confirm their email before they will no longer be able to login.

There is a situation where the user will enter in an email address that already exists in the system.
This can be determined by checking the ``userExists`` property that is returned by the API. If the user
exists, this is how the response from the API will look like:

.. code-block:: json

   {
     "userExists": true
   }

In this case, the client should display a message or screen to the user indicating that the account
exists and that they will need to login with that account before they can accept the invite. Once the
user has logged in, the steps explained in :ref:`organization-invite-accepting-an-invite-security-question-and-answer`
should be followed to accept the invite and link the patient to the account.

.. _organization-invite-retrieving-invites-for-a-user:

Retrieving Invites For a User
-----------------------------

To retrieve all the invites for the current logged in user, the following request can be made:

.. code-block:: console

   GET https://api.bluebuttonpro.com/OrganizationInvites/received

   Authorization: Bearer <token>

This will return all invites that were :ref:`created using the user's email address <organization-invite-creating-an-invite-user-email-address>`,
and it will contain all invites that were :ref:`created using a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`
where the user was able to succesfully register a new account using the security question and answer.

This call will not contain any invites that were :ref:`created using a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`
and the user could not register a new account because the account they specified already exists. This
is explained towards the bottom of :ref:`this section <organization-invite-registering-a-user-by-security-question-and-answer>`.

The response of the API should contain an array of organization invites that the user can accept:

.. code-block:: json

   [
     {
       "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
       "createdOn": "01-01-2021",
       "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
       "userId": "e35819bf-6df0-447c-9484-3ff77029ac44",
       "userName": "Jane Doe",
       "userEmail": "user@example.com",
       "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",
       "fhirServerName": "Good Health Clinic",
       "patient": { }
     },
   ]

Searching For Sent Invites
--------------------------

An organization can search for the invites that they have sent. The organization will need to indicate
which FHIR server to search with by specifying a :doc:`FHIR server context <header-values>` header.
The request can be constructed like this:

.. code-block:: console

   GET https://api.bluebuttonpro.com/OrganizationInvites/sent?page=1&count=15

   Authorization: Bearer <token>
   FhirServerId-Context: d9c34349-3e81-41bf-8113-27bc01dcaa9d

This will return a paged response that will contain a page of invites that were sent for the specified
FHIR server:

.. code-block:: json

   {
     "total": 30,
     "totalPages": 2,
     "hasNextPage" true,
     "currentPage": 1,
     "resources": [
       {
         "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
         "createdOn": "01-01-2021",
         "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
         "userId": "e35819bf-6df0-447c-9484-3ff77029ac44",
         "userName": "Jane Doe",
         "userEmail": "user@example.com",
         "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",
         "fhirServerName": "Good Health Clinic",
         "patient": { }
       },
     ]
   }

The ``page`` and ``count`` query parameters can be manipulated to better define how the result set should
be determined. This endpoint will return all invites regardless of whether they were :ref:`created using
the user's email address <organization-invite-creating-an-invite-user-email-address>` or :ref:`created
using a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`,
and regardless of whether the user has interacted with any of the invites.

.. _organization-invite-finding-an-invite-by-id:

Finding an Invite by ID
-----------------------

If the id of the invite is known, the invite can be retrieved by making the following request, assuming
the id of the invite is ``f05df920-b51e-4da2-b7a6-9eebc67e7059``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/OrganizationInvites/f05df920-b51e-4da2-b7a6-9eebc67e7059

   Authorization: Bearer <token>

If the invite is found and the current logged in user has access to the invite, then the invite will
be returned. A user has access to the invite if they have access to the FHIR server that the invite
is associated with or if they have an email address that matches the email address the invite is associated
with.

.. note::

   If the user has access to the invite based on the FHIR server rather than an email address, the user
   must have at least write permissions on the FHIR server for it to be accessible. Users that have
   read-only permissions to the FHIR server will not be able to view this invite.

Determining Accepted Invites
----------------------------

To determine if an invite was already accepted, there is a property included in the invite called ``acceptedOn``
that will contain the date the invite was accepted. Any of the search functions described can be used.
As an example, if an invite is found :ref:`using an id <organization-invite-finding-an-invite-by-id>`,
then the response will contain an ``acceptedOn`` property:

.. code-block:: json

   {
     "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
     "createdOn": "01-01-2021",
     "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
     "userId": "e35819bf-6df0-447c-9484-3ff77029ac44",
     "userName": "Jane Doe",
     "userEmail": "user@example.com",
     "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",
     "acceptedOn": "01-01-2021",
     "fhirServerName": "Good Health Clinic",
     "patient": { }
   }

If the invite has not yet been accepeted, this property will not be included.

.. _organization-invite-accepting-an-invite-person-association:

Accpeting an Invite - Person Association
----------------------------------------

To accept an invite, a :doc:`person <person>` resource is required. This person will then be associated
with read-only permissions to the patient in the invite. Each invite will have a ``patient`` object
which is a standard `FHIR patient resource <https://www.hl7.org/fhir/patient.html>`_. This patient object
can be used to :ref:`create a new person <person-creating-a-person>`. It is also possible to associate
the invite to an existing person if needed. The client should display a message or screen to the user
indicating which they prefer.


.. _organization-invite-accepting-an-invite-user-email-address:

Accepting an Invite - User Email Address
----------------------------------------

Accepting an invite using an email address is achievable if the invite was :ref:`created using the user's
email address <organization-invite-creating-an-invite-user-email-address>`, or if the invite was :ref:`created
using a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`
and the user was able to successfully register a new account. If the invite was :ref:`created using
a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`
and the user could not register a new account because the email address they provided already exists,
then the user can only accept the invite following the steps in :ref:`organization-invite-accepting-an-invite-security-question-and-answer`.

In both of these situations, the user must be logged in to accept the invite. To begin, the invite to
accept must be determined. This can be typically done by displaying the list of invites that the user
can accept by :ref:`retrieving them <organization-invite-retrieving-invites-for-a-user>` and then allowing
the user to select which invite to accept.

A person will need to be selected which is explained :ref:`here <organization-invite-accepting-an-invite-person-association>`.
Once a person has been selected a request will need to be sent to the following endpoint to accept the
invite, assuming the id of the invite is ``f05df920-b51e-4da2-b7a6-9eebc67e7059``: https://api.bluebuttonpro.com/OrganizationInvites/f05df920-b51e-4da2-b7a6-9eebc67e7059/accept.

The endpoint accepts the following parameters:

id - **REQUIRED**
   This is the id of the invite.

personId - **REQUIRED**
   This is the id of the person to associate the patient in the invite with.

The request can be constructed like this:

.. code-block:: console
  
   POST https://api.bluebuttonpro.com/OrganizationInvites/f05df920-b51e-4da2-b7a6-9eebc67e7059/accept

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
      "personId": "2e755707-1d7b-435b-9ae7-32fcddb87fdb"
   }

If the operation was successful, a :doc:`grant <grants>` that indicates that the person now has read-only
access to the patient in the invite should be returned by the API.

.. _organization-invite-accepting-an-invite-security-question-and-answer:

Accepting an Invite - Security Question & Answer
------------------------------------------------

If an invite was :ref:`created using a security question & answer <organization-invite-creating-an-invite-security-question-and-answer>`
and the user could not register a new account because the email address they want to use already exists
in the system, then they can accept the invite by logging in to that account and then providing the
security question and answer that identifies the invite.

To start, the client should display a screen to the user asking for the security code that they received
when the invite was :ref:`first created <organization-invite-creating-an-invite-security-question-and-answer>`.
Once the user enters in this information, the client should then make a call to the API to retrieve
the security question by constructing the following request, assuming the security code is ``ABC12345``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/OrganizationInvites/security-details/code/ABC12345/security-question

Once the secrity question is obtained, it should be displayed to the user. The user then enters in the
security answer. Once that is complete, the client can then retrieve the invite by calling the endpoint:
https://api.bluebuttonpro.com/OrganizationInvites/security-details/find. This endpoint accepts the following
parameters:

securityCode - **REQUIRED**
   The security code entered by the user that identifies the invite.

securityAnswer - **REQUIRED**
   The security answer entered by the user that identifies the invite.

The request should look like this:

.. code-block:: console

   POST https://api.bluebuttonpro.com/OrganizationInvites/security-details/find

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "securityCode": "ABC12345",
      "securityAnswer": "Charlie"
   }

The API will then return the invite if the security answer matches.

To accept the invite, a person will need to be selected. More information on that is explained :ref:`here
<organization-invite-accepting-an-invite-person-association>`. Once a person has been selected, a call
to the following endpoint will need to be performed: https://api.bluebuttonpro.com/OrganizationInvites/security-details/code/ABC12345/accept.

This endpoint accepts the following parameters:

securityCode - **REQUIRED**
   The security code entered by the user that identifies the invite.

securityAnswer - **REQUIRED**
   The security answer entered by the user that identifies the invite.

personId - **REQUIRED**
   This is the id of the person to associate the patient in the invite with.

The request should look like this:

.. code-block:: console

   POST https://api.bluebuttonpro.com/OrganizationInvites/security-details/code/ABC12345/accept

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "securityCode": "ABC12345",
      "securityAnswer": "Charlie",
      "personId": "2e755707-1d7b-435b-9ae7-32fcddb87fdb"
   }

If the operation was successful, the API will return a :doc:`grant <grants>` that indicates that the
person now has read-only access to the patient in the invite should be returned by the API.