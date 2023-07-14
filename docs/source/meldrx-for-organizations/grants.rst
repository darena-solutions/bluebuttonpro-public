.. _grants:

Grants
======

Grants are records in our system that indicate the FHIR server an entity has access to under a particular
role. Grants are central in the BlueButtonPRO platform as almost all calls in the API require a FHIR
server id that the current user has access to.

.. _grants-roles:

Roles
-----

A grant to a FHIR server also includes a role:

Owner
   This role indicates that the specified :ref:`entity <grants-entities>` owns the FHIR server specified
   in the grant. There can only be one owner of a FHIR server. Owners have the highest privileges to
   a FHIR server. They can perform any operations from all other roles including deleting the FHIR server.

   Typically for organizational FHIR servers, the organization itself will be the owner rather than
   a specific user account. For personal FHIR servers, a specific user is usually the owner.

   .. note::

      Because of :ref:`inherited roles <grants-inherited-roles>`, this does mean that in the case of
      an organizational FHIR server, it cannot be deleted unless it is being deleted by a Darena Solutions
      member at this time. We are looking into how we can change this.
      

Administrator
   Administrators can perform a broad range of operations on a FHIR server. Typically, it is the same
   as the ``Write`` role, however, there are some calls in the API that are restricted to just administrators
   and owners. Creating additional grants to the FHIR server requires an administrator for example.

Write
   This role allows the user to read and write data from the FHIR server. :ref:`Organization invites
   <invite>` and other various functionality that requires writes can also be performed with this role.

Read
   This role only allows reads on the FHIR server. Any create, update, or delete operation is forbidden
   with this role.

Synapse
   This is currently the lowest permission among all the roles. It is a very specific role that only
   allows an entity to send :ref:`synapse packages <synapse>` to the organization that owns the FHIR
   server listed in the grant. The entity that has a grant with this role will not be able to perform
   any action on the listed FHIR server, not even reads. It should be noted that the other roles allow
   the entity to send :ref:`synapse packages <synapse>` as well.

.. _grants-inherited-roles:

Inherited roles
---------------

There are a couple situations where the role is inherited. If a grant is given to an organization, then
all users of that organization will inherit the ``Write`` role. This means that by default, all users
of that organization can read, create, update, or delete records in the FHIR server specified in the
grant.

Another situation is when a grant is given to a :ref:`person <person>` resource. In this situation all
users that have access to that person inherit the role that the user has with that person. EG: If the
user has write permissions to the person, then they also have write permissions to the FHIR server specified
in the grant.

If a specific role is required for a specific user outside of the group, then a separate grant will
need to be created. As an example, suppose you have a grant for an organization and all users of that
organization now have ``Write`` permissions to the FHIR server indicated on the grant. It may be necessary
that you want a few individuals in that organization to be administrators of that FHIR server. In this
case, a different grant for each individual user account will need to be :ref:`created <grants-creating-grants>`.

.. _grants-entities:

Entities
--------

Grants can be given to a few different entities:

User
   Users are specific user accounts that can log into the system. Usually, grants that are applied to
   a specific user indicate that they require a specific role that should be different from a related
   group. As an example, all users of an organization will contain the ``Write`` role, but it may be
   necessary that a few individuals in that organization require the ``Administrator`` role. In this
   case, separate grants for each individual user will need to be :ref:`created <grants-creating-grants>`.

.. _person:

Person
   Grants can be applied to :ref:`person <person>` resources. In this case, all users that have an association
   with this person will also have access to the FHIR server specified in the grant. Typically, grants
   to person resources are created for restricted patient access to a FHIR server, this is done through
   the :ref:`organization invite process <invite>`.

Organization
   When a grant is applied to an organization, all users of that organization will also have access
   to the FHIR server specified in the grant. Typically, grants to organizations are created for organizational
   FHIR servers.

Application
   Applications are servers that require interaction with the BlueButtonPRO API without user context.
   Typically in most scenarios, a user will log into the BlueButtonPRO system and then interact with
   the system using this context. However, it may be necessary for some vendors where they manager their
   own users. In this case, the vendor will have registered an application for API access without user
   context. Review the get started guide to understand how these applications
   can be registered.

   .. note::

      At this time, only a member of Darena Solutions can create grants for applications.

ExternalApplication
   External applications are applications that do not or cannot support OAuth2. BlueButtonPRO requires
   OAuth2 to authenticate and authorize entities. External applications are limited to only having read
   permissions to a FHIR server. External applications are only created in very rare cases and can only
   be created after a discussion with a member of Darena Solutions.

   .. note::

      At this time, only a member of Darena Solutions can create grants for applications.

Default grants
--------------

There are some situations where a grant is created by default:

* When a FHIR server is provisioned for an organization by a member of Darena Solutions, a grant is
  created by default that links that organization with the FHIR server with an ``Owner`` role. This
  means that all users of that organization can now access that FHIR server and they have the :ref:`inherited 
  role <grants-inherited-roles>` of ``Write``.

* When a user accepts an :ref:`organization invite <invite>` for a person, a grant is created automatically
  linking that person with the organization's FHIR server with a ``Read`` or ``Synapse`` role. In addition
  the grant will :ref:`limit access to the patient resource <grants-restrict-access-to-patient>` that
  was specified in the organization invite.

* When a :ref:`PFR <definitions-pfr>` is provisioned for a person resource, all users that have access
  to that person will now have a grant to that :ref:`PFR <definitions-pfr>` with whatever role the user
  has with that person. EG: If the user has write permissions to that person, they will also have write
  permissions to the :ref:`PFR <definitions-pfr>`.

.. _grants-restrict-access-to-patient:

Restrict access to patient
--------------------------

It is possible to restrict an entity to a specific patient in a FHIR server. Review the process of :ref:`creating
a grant <grants-creating-grants>` to understand how to do this. If this is done, it will indicate that
the entity has ``Read`` or ``Synapse`` permissions only for that particular patient. If the entity has
``Read`` permissions, it indicates that only this patient and any related resources for that patient
(EG: encounters, medications, etc.) can be read, and nothing else. For resources that are not patient-specific
(EG: questionnaires, value sets, etc.) these are still accessible. If the entity has ``Synapse`` permissions,
it indicates that the entity can create :ref:`synapse packages <synapse>` that contains FHIR resources
for only that particular patient.

When accepting an :ref:`organization invite <invite>`, these types of grants are created automatically
granting ``Read`` or ``Synapse`` permissions to the FHIR server to the specified entity with restricted
access to a single patient.

.. _grants-creating-grants:

Creating grants
---------------

To create a grant, the following information will be required:

entityType
   The type of entity the grant is being applied for. Review :ref:`grants-entities` for more information.

entityId
   The id of the entity the grant is being applied for.

   .. warning::

      When creating a grant for a ``User``, the id of the entity must be the **email address** of that
      user. This will be resolved to the id of the user internally.

fhirServerId
   The id of the FHIR server the grant is being applied for.

   .. note::

      Only users with an ``Owner`` or ``Administrator`` role to the FHIR server can create additional grants
      for the server.

role
   The role the entity should have to the FHIR server. Review :ref:`grants-roles` for more information.

   .. note::

      If the role is ``Synapse``, then ``accessiblePatientId`` is required. Additionally, when creating
      a role for an Azure FHIR server, the role **must** be set to ``Synapse``.

accessiblePatientId
   If the entity should be restricted to a specific patient, then the id of the patient resource needs
   to be included in this property.

   .. note::

      If creating a grant with restricted patient access, the ``role`` **must** be set to ``Read`` or
      ``Synapse``.

Once all this information has been acquired, a request can be made like so:

.. code-block:: console

   POST https://api.bluebuttonpro.com/FhirServers/grants
   
   Content-Type: application/json
   Authorization: Bearer <token>

   {
     "entityType": "User",
     "entityId": "example@gmail.com",
     "fhirServerId: "1eb93ff5-d72b-4556-a361-dc14d00bf1f8",
     "role": "Read",
     "accessiblePatientId": "ab511915-625c-43d4-8b49-f6f44caa90a6"
   }

Creating more than one restricted patient access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If an entity requires restricted patient access, but to more than just one patient, simply create an
additional grant for each patient. EG: If restricted access is required for both ``patient1`` and ``patient2``,
then two grants will need to be created using the create endpoint with the ``accessiblePatientId`` set
respectively.

Limitations
^^^^^^^^^^^

* There can only exist one ``Owner`` of a FHIR server. This is applied internally. When an organizational
  FHIR server is provisioned by a member of Darena Solutions, the organization is marked as the owner
  internally. When a :ref:`PFR <definitions-pfr>` is provisioned, the user that created the PFR is marked
  as the owner internally. Thus, grants cannot be created manually with the ``Owner`` role.

* Only owners and administrators of a FHIR server can create additional grants.

* If creating a grant with restricted patient access, then ``role`` must be set to ``Read`` or ``Synapse``.

* If ``role`` is ``Synapse``, then ``accessiblePatientId`` is required.

* If a grant is being created for an Azure FHIR server, then ``role`` must
  be set to ``Synapse``.

* Only a member of Darena Solutions can create grants for an ``Application`` or ``ExternalApplication``.

.. _grants-retrieving-grants-for-current-user:

Retrieving grants for current user
----------------------------------

To retrieve all grants for the current user, a request can be made to the following endpoint:

.. code-block:: console

   GET https://api.bluebuttonpro.com/FhirServers/records

   Authorization: Bearer <token>

This will return all grants for the user, including grants with the inherited role. As an example, suppose
a user belongs to ``orgA``. Then a grant will be returned that indicates that this user has a grant
to the organization's FHIR server with the ``Write`` role. Suppose now that this user was also assigned
as an administrator and a grant to the organization's FHIR server with the ``Administrator`` role for
that user was created. This grant will also be returned in this call. Thus two grants will be returned,
but the grant with the ``Adiministrator`` role will take precedence when executing endpoints in the
BlueButtonPRO API. In short, specific grants to entities will always take precedence over inherited
roles. Here is an example of such a scenario:

.. code-block:: json

   [
     {
      "grantId": "f0884c2f-078f-4118-834c-9b30af68b289",
      "entityType": "Organization",
      "entityId": "orgA",
      "role": "Write",
      "roleIsInherited": true,
      "fhirServerId": "7ba38f5d-3e98-47ca-82dd-48865d84aabd",
      "description": "FHIR server description",
      "fhirDatabaseId": "pdf203ef9f966b4c1d90809cafa8165d15",
      "fhirDatabaseDisplayName": "display-name",
      "fhirServerEndpoint": "https://api.bluebuttonpro.com/display-name",
      "type": "Organizational",
      "isAzureFhirServer": false
    },
    {
      "grantId": "0fb7ccf5-1ff7-45f2-b3a4-cb01c4e7040c",
      "entityType": "User",
      "entityId": "8d6c69f1-08d0-4eaa-9719-55db4405bf8c",
      "role": "Administrator",
      "roleIsInherited": false,
      "fhirServerId": "7ba38f5d-3e98-47ca-82dd-48865d84aabd",
      "description": "FHIR server description",
      "fhirDatabaseId": "pdf203ef9f966b4c1d90809cafa8165d15",
      "fhirDatabaseDisplayName": "display-name",
      "fhirServerEndpoint": "https://api.bluebuttonpro.com/display-name",
      "type": "Organizational",
      "isAzureFhirServer": false
    }
   ]

Notice how the grant is for the same FHIR server, but one is an inherited role, and the other is applied
to the specific user.

.. note::

   An option to only return the grants with the highest precedence is in-progress.

Another scenario where multiple grants are displayed for the same FHIR server is when there needs to
be restricted patient access, but the restriction applies to more than one patient. In this scenario,
a grant will be retrieved for each patient. The grant structure will be pretty much the same, the only
differing value will be ``accessiblePatientId``. Here is an example for two restricted patients:

.. code-block:: json

   [
     {
      "grantId": "78c63883-583a-46e8-967d-53013aaa3f07",
      "entityType": "Person",
      "entityId": "1e1d0ad7-e473-401b-ab6e-69e3da587fce",
      "role": "Read",
      "roleIsInherited": true,
      "fhirServerId": "7ba38f5d-3e98-47ca-82dd-48865d84aabd",
      "description": "FHIR server description",
      "fhirDatabaseId": "pdf203ef9f966b4c1d90809cafa8165d15",
      "fhirDatabaseDisplayName": "display-name",
      "fhirServerEndpoint": "https://api.bluebuttonpro.com/display-name",
      "type": "Organizational",
      "isAzureFhirServer": false,
      "accessiblePatientId": "05851edf-faab-4e77-aec5-69407f2831dd"
    },
    {
      "grantId": "26d330b0-ba9a-44ba-afa2-7102cc109fdb",
      "entityType": "Person",
      "entityId": "1e1d0ad7-e473-401b-ab6e-69e3da587fce",
      "role": "Read",
      "roleIsInherited": true,
      "fhirServerId": "7ba38f5d-3e98-47ca-82dd-48865d84aabd",
      "description": "FHIR server description",
      "fhirDatabaseId": "pdf203ef9f966b4c1d90809cafa8165d15",
      "fhirDatabaseDisplayName": "display-name",
      "fhirServerEndpoint": "https://api.bluebuttonpro.com/display-name",
      "type": "Organizational",
      "isAzureFhirServer": false,
      "accessiblePatientId": "74404e3b-9c5a-4d34-b505-30477303eb38"
    }
   ]

Notice how all properties of the grant are the same, it's just the ``accessiblePatientId`` that has
a different value (and ``grantId`` to uniquely identify each grant).

.. _grants-retrieving-grants-for-a-fhir-server:

Retrieving grants for a FHIR server
-----------------------------------

You can retrieve all grants for a particular FHIR server rather than just retrieving grants for the
current user. Calling the following endpoint will return all grants for a particular FHIR server, assuming
the id of the FHIR server is ``e86b0217-17e1-4259-974d-f08397776d33``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/FhirServers/e86b0217-17e1-4259-974d-f08397776d33/grants

   Authorization: Bearer <token>

As long as the current user has access to the FHIR server, this endpoint call will succeed, no specific
role is required.

.. note::

   Precedence of grants also applies here as is explained in :ref:`grants-retrieving-grants-for-current-user`.

Retrieving grants by ID
-----------------------

If the id of a grant is known, this can be retrieve by making the following call, assuming the id of
the grant is ``891f2653-1863-4c9a-ad51-aba9050bb855``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/FhirServers/grants/891f2653-1863-4c9a-ad51-aba9050bb855

   Authorization: Bearer <token>

If the grant applies to the current user, it will be returned, otherwise a ``404 Not Found`` response
will be returned.

Deleting grants
---------------

Only owners of a FHIR server can delete grants, this is even restricted for administrators. If you are
an owner of a FHIR server and wish to delete a grant, first obtain the grant to delete by using one
of the :ref:`retrieval methods <grants-retrieving-grants-for-current-user>` above. Once this is retrieved,
a request can be made like so, assuming the id of the grant is ``7c1f80d2-50c0-40c5-ac61-6629fb31ac7b``:

.. code-block:: console

   DELETE https://api.bluebuttonpro.com/FhirServers/grants/7c1f80d2-50c0-40c5-ac61-6629fb31ac7b

   Authorization: Bearer <token>