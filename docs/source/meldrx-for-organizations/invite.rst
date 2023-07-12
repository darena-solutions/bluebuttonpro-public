Invites
=======

There are two different types of invites in BlueButtonPRO, an organization invite and a registration
invite.

Organization invites allow an organization to link a user to a particular patient in a FHIR
server with restricted access. The user can also be newly registered through this method.

A registration invite is a much more simpler invite and only allows a user to be newly registered, without
a link to a patient in a FHIR server.

Regardless of the type of invite, the process to create this invites are the same. The only difference
between the two is how they are accepted and what happens after they are accepted.

.. _invite-creating-an-invite-security-question-and-answer:

Creating an Invite - Security Question & Answer
-----------------------------------------------

To create an invite that will register a new user without knowing the user's email address, the endpoint
at https://api.bluebuttonpro.com/Invites/security-details/create should be used. This endpoint takes
in the following parameters:

.. _invite-invite-type:

inviteType - **REQUIRED**
   The invite type, either ``Organization`` or ``Registration``. This is defaulted to ``Registration``
   if not explicitly given.

securityQuestion - **REQUIRED**
   This is the security question the user will need to answer before they can register. This security
   question will need to be displayed later once the user is ready to register an account. See :ref:`invite-registering-a-user-by-security-question-and-answer`
   for more information.

securityAnswer - **REQUIRED**
   This is the answer to the security question above. This will need to be entered in by the user once
   they are ready to register an account. See :ref:`invite-registering-a-user-by-security-question-and-answer`
   for more information.

.. _invite-is-synapse-role:

isSynapseRole
   This parameter is only relevant if the invite is an organization invite. If the invite is a registration
   invite, then do not send this parameter.
   
   This parameter indicates if the grant that is created after the organization invite is accepted should
   have a role of ``Synapse`` or ``Read``. This is ``false`` by default which indicates a role of ``Read``
   will be created by default. Review :ref:`grant roles <grants-roles>` for more information.

.. _invite-accessible-patient-id:

accessiblePatientId
   This parameter is only relevant if the invite is an organization invite. If the invite is a registration
   invite, then do not send this parameter.
   
   This parameter is the id of the FHIR patient resource in the organization's FHIR server. This will
   be the patient that the user will be linked to and have restricted access. If the id of the patient
   is not known, a search can be performed instead by using the ``accessiblePatientIdentifierSearchStr``
   parameter.

.. _invite-accessible-patient-identifier-search-str:

accessiblePatientIdentifierSearchStr
   This parameter is only relevant if the invite is an organization invite. If the invite is a registration
   invite, then do not send this parameter.

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
   
   POST https://api.bluebuttonpro.com/Invites/security-details/create
   
   Content-Type: application/json
   Authorization: Bearer <token>
   FhirServerId-Context: d9c34349-3e81-41bf-8113-27bc01dcaa9d

   {
     "inviteType": "Organization",
     "securityQuestion": "What is the name of your first pet?",
     "securityAnswer": "Charlie",
     "accessiblePatientId: "d1f84545-5981-4250-80d6-a99f8be0d716",
     "isSynapseRole": false
   }

If the operation was successful, the API will return a response that contains an 8 character alpha-numeric
security code along with a few other details:

.. code-block:: json

   {
     "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
     "createdOn": "01-01-2021",
     "inviteType": "Organization",
     "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
     "fhirServerName": "Good Health Clinic",
     "isSynapseRole": false,
     "securityCode": "ABC12345",
     "securityQuestion": "What is the name of your first pet?",
     "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",     
     "patient": { }
   }

The ``securityCode`` will need to be given to the user. The user will then use this security code to
:ref:`register an account <invite-registering-a-user-by-security-question-and-answer>`.

.. _invite-creating-an-invite-user-email-address:

Creating an Invite - User Email Address
---------------------------------------

To create an invite with a specific email address, the following endpoint will need to be used:
https://api.darenasolutions.com/Invites/user-details/create. This endpoint accepts the following parameters:

inviteType - **REQUIRED**
   See :ref:`inviteType <invite-invite-type>`.

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

isSynapseRole
   See :ref:`isSynapseRole <invite-is-synapse-role>`

accessiblePatientId
   See :ref:`accessiblePatientId <invite-accessible-patient-id>`.

accessiblePatientIdentifierSearchStr
   See :ref:`accessiblePatientIdentifierSearchStr <invite-accessible-patient-identifier-search-str>`.

In addition to the body of the request, a :doc:`FHIR server context <header-values>` header is required.

.. note::

   The user is created in our system and a confirmation email is sent only if the user does not already
   exist in our system. The user will have 24 hours to confirm their email address, after which they
   can no longer sign in. If the user already exists, then a registration does not occur and a confirmation
   email is not sent out.

Once all this information is obtained, a request can be constructed:

.. code-block:: console
   
   POST https://api.bluebuttonpro.com/Invites/user-details/create
   
   Content-Type: application/json
   Authorization: Bearer <token>
   FhirServerId-Context: d9c34349-3e81-41bf-8113-27bc01dcaa9d

   {
     "inviteType": "Organization",
     "userEmail": "user@example.com",
     "firstName": "Jane",
     "lastName": "Doe",
     "accessiblePatientId: "d1f84545-5981-4250-80d6-a99f8be0d716",
     "isSynapseRole": false
   }

.. _invite-registering-a-user-by-security-question-and-answer:

Registering a User By Security Question & Answer
------------------------------------------------

If an invite was :ref:`created using a security question & answer <invite-creating-an-invite-security-question-and-answer>`,
then a user can register an account by providing those values. Typically, the client should display
a window asking for the security code. Once the user enters in the security code, the security question
will need to be displayed to the user. To obtain the security question from the security code, the following
request will need to be sent to the API, assuming the security code is ``ABC12345``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/security-question

This endpoint can be called anonymously and does not require an authenticated request. The API will
return a plain text response that contains the security question. This security question should be displayed
to the user. The user must then enter the answer to the security question and provide their user account
details so that the account can be created in our system. The security code, answer, and user details
should then be sent to the following endpoint to complete registration, assuming the security code is
``ABC12345``: https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/register.

This endpoint accepts the following parameters:

securityCode - **REQUIRED**
   This is the security code that will identify the invite.

securityAnswer - **REQUIRED**
   This is the answer the user has provided for the given security question.

user - **REQUIRED**
   This value contains the user details needed to register and create the account.

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

   POST https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/register

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
in :ref:`invite-accepting-an-invite-user-email-address`. The invite is now associated with that user's
email address so only that user can accept the invite. This is what the response from the api looks
like:

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
user has logged in, the steps explained in :ref:`invite-accepting-an-invite-security-question-and-answer`
should be followed to accept the invite and link the patient to the account.

.. _invite-retrieving-invites-for-a-user:

Retrieving Invites For a User
-----------------------------

To retrieve all the invites for the current logged in user, the following request can be made:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/received

   Authorization: Bearer <token>

This will return all invites that were :ref:`created using the user's email address <invite-creating-an-invite-user-email-address>`,
and it will contain all invites that were :ref:`created using a security question & answer <invite-creating-an-invite-security-question-and-answer>`
where the user was able to successfully register a new account using the security question and answer.

This call will not contain any invites that were :ref:`created using a security question & answer <invite-creating-an-invite-security-question-and-answer>`
and the user could not register a new account because the account they specified already exists. This
is explained towards the bottom of :ref:`this section <invite-registering-a-user-by-security-question-and-answer>`.

The response of the API should contain an array of organization invites that the user can accept:

.. code-block:: json

   [
     {
       "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
       "createdOn": "01-01-2021",
       "inviteType": "Organization",
       "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
       "fhirServerName": "Good Health Clinic",
       "isSynapseRole": false,
       "userId": "e35819bf-6df0-447c-9484-3ff77029ac44",
       "userName": "Jane Doe",
       "userEmail": "user@example.com",
       "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",       
       "patient": { }
     },
   ]

It is possible to filter the invites so that only open invites are returned instead of all invites.
To do that, simply add the ``openOnly`` query parameter to the endpoint and set its value to ``true``.
EG:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/received?openOnly=true

   Authorization: Bearer <token>

Searching For Sent Invites
--------------------------

An organization can search for the invites that they have sent. The organization will need to indicate
which FHIR server to search with by specifying a :doc:`FHIR server context <header-values>` header.
The request can be constructed like this:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/sent?page=1&count=15

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
         "inviteType": "Organization",
         "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
         "fhirServerName": "Good Health Clinic",
         "isSynapseRole": false,
         "userId": "e35819bf-6df0-447c-9484-3ff77029ac44",
         "userName": "Jane Doe",
         "userEmail": "user@example.com",
         "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",         
         "patient": { }
       },
     ]
   }

The ``page`` and ``count`` query parameters can be manipulated to better define how the result set should
be determined. This endpoint will return all invites regardless of whether they were :ref:`created using
the user's email address <invite-creating-an-invite-user-email-address>` or :ref:`created using a security
question & answer <invite-creating-an-invite-security-question-and-answer>`, and regardless
of whether the user has interacted with any of the invites.

It is possible to filter the invites so that only open invites are returned instead of all invites.
To do that, simply add the ``openOnly`` query parameter to the endpoint and set its value to ``true``.
EG:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/sent?openOnly=true&page=1&count=15

   Authorization: Bearer <token>
   FhirServerId-Context: d9c34349-3e81-41bf-8113-27bc01dcaa9d

.. _invite-finding-an-invite-by-id:

Finding an Invite by ID
-----------------------

If the id of the invite is known, the invite can be retrieved by making the following request, assuming
the id of the invite is ``f05df920-b51e-4da2-b7a6-9eebc67e7059``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/f05df920-b51e-4da2-b7a6-9eebc67e7059

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
As an example, if an invite is found :ref:`using an id <invite-finding-an-invite-by-id>`, then the response
will contain an ``acceptedOn`` property:

.. code-block:: json

   {
     "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
     "createdOn": "01-01-2021",
     "inviteType": "Organization",
     "fhirServerId": "d9c34349-3e81-41bf-8113-27bc01dcaa9d",
     "fhirServerName": "Good Health Clinic",
     "isSynapseRole": false,
     "userId": "e35819bf-6df0-447c-9484-3ff77029ac44",
     "userName": "Jane Doe",
     "userEmail": "user@example.com",
     "accessiblePatientId": "d1f84545-5981-4250-80d6-a99f8be0d716",
     "acceptedOn": "01-01-2021",     
     "patient": { }
   }

If the invite has not yet been accepted, this property will not be included.

.. _invite-accepting-an-invite-person-association:

Accepting an Invite - Person Association
----------------------------------------

To accept an invite, a :doc:`person <person>` resource is required. Depending on the type of invite,
a couple different things can happen.

If the invite being accepted is a registration invite, then a new person will need to be created. The
details about this person needs to be supplied in the acceptance request in the ``person`` parameter.
This is explained further down in the next section.

For organization invites the required person will be associated with read-only permissions or synapse
permissions to the patient in the invite. This is determined based on how the :ref:`invite was created
<invite-creating-an-invite-security-question-and-answer>` and whether the ``isSynapseRole`` was set
to ``true`` or not. There are two different ways this person resource can be determined. One way is
to select an existing person. To do this, the ``existingPersonId`` parameter should contain the id of
the existing person when performing the request. Another way is to create a new person when accepting
an invite. To do this, ``existingPersonId`` should remain empty and a request should be performed. The
API will determine that since ``existingPersonId`` is empty, a new person should be created. Each organization
invite will have a ``patient`` object which is a standard `FHIR patient resource <https://www.hl7.org/fhir/patient.html>`_.
This patient object will be used to create the new person.

The ``existingPersonId`` parameter is explained more in the sections below.

.. _invite-accepting-an-invite-user-email-address:

Accepting an Invite - User Email Address
----------------------------------------

Accepting an invite using an email address is achievable if the invite was :ref:`created using the user's
email address <invite-creating-an-invite-user-email-address>`, or if the invite was :ref:`created using
a security question & answer <invite-creating-an-invite-security-question-and-answer>` and the user
was able to successfully register a new account. If the invite was :ref:`created using a security question
& answer <invite-creating-an-invite-security-question-and-answer>` and the user could not register a
new account because the email address they provided already exists, then the user can only accept the
invite following the steps in :ref:`invite-accepting-an-invite-security-question-and-answer`.

In both of these situations, the user must be logged in to accept the invite. To begin, the invite to
accept must be determined. This can be typically done by displaying the list of invites that the user
can accept by :ref:`retrieving them <invite-retrieving-invites-for-a-user>` and then allowing the user
to select which invite to accept.

A person will need to be associated which is explained :ref:`here <invite-accepting-an-invite-person-association>`.
Once all this information is obtained a request will need to be sent to the following endpoint to accept
the invite, assuming the id of the invite is ``f05df920-b51e-4da2-b7a6-9eebc67e7059``: https://api.bluebuttonpro.com/Invites/f05df920-b51e-4da2-b7a6-9eebc67e7059/accept.

The endpoint accepts the following parameters:

id - **REQUIRED**
   This is the id of the invite.

.. _invite-existing-person-id:

existingPersonId
   This parameter is only relevant for organization invites. For registration invites, use the ``person``
   parameter instead.
   
   If the invite should be associated with an existing person, then this parameter should contain the
   id of that person. If a new person needs to be created, then do not send a request with this parameter,
   or set the parameter to ``null``.

.. _invite-person-relationship-type:

personRelationshipType
   This parameter is only relevant for organization invites. For registration invites, use the ``person``
   parameter instead.

   This parameter is only required if a new person needs to be generated, otherwise the parameter is
   ignored. The relationship between the user and the new person needs to be specified here.

.. _invite-person:

person
   This parameter is only relevant for registration invites. For organization invites, use the ``existingPersonId``
   or ``personRelationshipType`` parameters instead.

   When accepting a registration invite, a person needs to be created. The details about this person
   needs to be supplied in this parameter.

   person.firstName - **REQUIRED**
      The person's first name.

   person.middleName
      The person's middle name.

   person.lastName - **REQUIRED**
      The person's last name.

   person.gender - **REQUIRED**
      The person's gender. Valid values: ``Male``, ``Female``, ``Other``, ``Unknown``.

   person.birthDate - **REQUIRED**
      The person's birth date.

   person.addressLine1
      The person's first address line.

   person.addressLine2
      The person's second address line.

   person.city
      The person's city.

   person.state
      The person's state. The state value must be the two character abbreviation.

   person.zipCode
      The person's zip code. This value can only be a 5 digit or 9 digit number.

   person.relationship
      The relationship of this person to the user. Valid values: ``Self``, ``Parent``, ``Child``, ``Sibling``,
      ``Spouse``, ``Relative``, ``Provider``, ``Other``.  If not specified, this defaults to ``Self``.
   
   person.identifiers
      An array of additional identifiers for this person. For example, a social security number.

      person.identifiers.system - **REQUIRED**
         The system that represents the identifier.

      person.identifiers.value - **REQUIRED**
         The identifier value.

   person.contacts
      An array of contacts for this person.

      person.contacts.type - **REQUIRED**
         The contact type. Valid values: ``Phone``, ``Fax``, ``Email``, ``Pager``, ``Url``, ``Sms``,
         ``Other``.

      person.contacts.value - **REQUIRED**
         The contact value.

      person.contacts.primary - **REQUIRED**
         A ``true`` or ``false`` value to indicate if this is the primary contact.

The request can be constructed like this for accepting an organization invite:

.. code-block:: console
  
   POST https://api.bluebuttonpro.com/Invites/f05df920-b51e-4da2-b7a6-9eebc67e7059/accept

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
      "existingPersonId": "2e755707-1d7b-435b-9ae7-32fcddb87fdb"
   }

A request for accepting an registration invite looks something like this:

.. code-block:: console
  
   POST https://api.bluebuttonpro.com/Invites/f05df920-b51e-4da2-b7a6-9eebc67e7059/accept

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "id": "f05df920-b51e-4da2-b7a6-9eebc67e7059",
      "person": {
         "firstName": "Jane",
         "lastName": "Doe",
         "gender": "Female",
         "birthDate": "1985-01-01",
         "contacts": [
            {
               "type": "Phone",
               "value": "555-555-5555",
               "primary": true
            },
            {
               "type": "Email"
               "value": "jane_doe@example.com",
               "primary": false
            }
         ]
      }
   }

If the operation was successful, a result model will be returned that contains the details about the
operation. This model will contain the person that was created or used. The invite that was accepted.
And in the case of an organization invite, a :doc:`grant <grants>` model will also be included to indicate
that the person now has read-only or synapse access to the patient.

.. _invite-accepting-an-invite-security-question-and-answer:

Accepting an Invite - Security Question & Answer
------------------------------------------------

If an invite was :ref:`created using a security question & answer <invite-creating-an-invite-security-question-and-answer>`
and the user could not register a new account because the email address they want to use already exists
in the system, then they can accept the invite by logging in to that account and then providing the
security question and answer that identifies the invite.

To start, the client should display a screen to the user asking for the security code that they received
when the invite was :ref:`first created <invite-creating-an-invite-security-question-and-answer>`. Once
the user enters in this information, the client should then make a call to the API to retrieve the security
question by constructing the following request, assuming the security code is ``ABC12345``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/security-question

Once the security question is obtained, it should be displayed to the user. The user then enters in the
security answer. Once that is complete, the client can then retrieve the invite by calling the endpoint:
https://api.bluebuttonpro.com/Invites/security-details/find. This endpoint accepts the following parameters:

securityCode - **REQUIRED**
   The security code entered by the user that identifies the invite.

securityAnswer - **REQUIRED**
   The security answer entered by the user that identifies the invite.

The request should look like this:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Invites/security-details/find

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "securityCode": "ABC12345",
      "securityAnswer": "Charlie"
   }

The API will then return the invite if the security answer matches.

To accept the invite, a person will need to be associated. More information on that is explained :ref:`here
<invite-accepting-an-invite-person-association>`. Once this information has been obtained, a call to
the following endpoint will need to be performed: https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/accept.

This endpoint accepts the following parameters:

securityCode - **REQUIRED**
   The security code entered by the user that identifies the invite.

securityAnswer - **REQUIRED**
   The security answer entered by the user that identifies the invite.

existingPersonId
   See :ref:`existingPersonId <invite-existing-person-id>`

personRelationshipType
   See :ref:`personRelationshipType <invite-person-relationship-type>`

person
   Sett :ref:`person <invite-person>`.

The request should look like this for organization invites:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/accept

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "securityCode": "ABC12345",
      "securityAnswer": "Charlie",
      "personRelationshipType": "Self"
   }

And it should look like this for registration invites:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Invites/security-details/code/ABC12345/accept

   Content-Type: application/json
   Authorization: Bearer <token>

   {
      "securityCode": "ABC12345",
      "securityAnswer": "Charlie",
      "person": {
         "firstName": "Jane",
         "lastName": "Doe",
         "gender": "Female",
         "birthDate": "1985-01-01",
         "contacts": [
            {
               "type": "Phone",
               "value": "555-555-5555",
               "primary": true
            },
            {
               "type": "Email"
               "value": "jane_doe@example.com",
               "primary": false
            }
         ]
      }
   }

If the operation was successful, a result model will be returned that contains the details about the
operation. This model will contain the person that was created or used. The invite that was accepted.
And in the case of an organization invite, a :doc:`grant <grants>` model will also be included to indicate
that the person now has read-only or synapse access to the patient.