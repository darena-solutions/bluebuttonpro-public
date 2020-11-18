Synapse
=======

Synapse is a functinality that allows resources for a specific patient to be shared with an organization
or given to that patient in question themselves.

.. _synapse-creating-a-package:

Creating a package
------------------

To create a Synapse package a FHIR bundle will need to be created first. This bundle should contain
FHIR resources for one specific target patient. Patient resources are not allowed in this bundle. Next,
you will need the id of the FHIR patient resource the bundle targets. This id is extracted from the
**organization's** FHIR server where the patient resource exists. This should not be confused with the
FHIR patient resource that exists in a :ref:`PFR <definitions-pfr>`.

In addition to the id of the FHIR patient resource, the id of the FHIR server where the patient exists
in must also be included. The reason for this is because all FHIR servers are completely isolated from
each other, which means that there is a small chance the same id for a patient can be assigned across
FHIR servers.

Whether the package is being sent to an organization or to a person needs to be defined.

* When sending to a person:

  * A security code will be required. This security code is hashed and stored in our system. The code
    will be required to be entered in by a user that has access to the person before the package can
    be imported by that user.
  * The user must have accepted an :doc:`organization invite <organization-invite>` that binds a person
    with a particular patient in the organization's FHIR server.

* When sending to an organization:

  * Security code is not required. If one is given, it will be ignored by the API.
  * The user sending the package must have accepted an :doc:`organization invite <organization-invite>`
    from the organization that binds a person to a particular patient in the organization's FHIR server.

.. note::

   The only direction in which packages can be sent is organization-to-person and person-to-organization.
   Support for organization-to-organization or from person-to-person does not currently exist.

Finally, an expiration date can be set for the package. If an expiration date is set, it will indicate
that the package can no longer be imported after this expiration date. However, the package can still
be viewed in searches depending on the type of search being performed. Review :ref:`synapse-finding-packages`
for more information. If no expiration date is provided, the package doesn't expire and can be imported
at any time.

Example bundle
^^^^^^^^^^^^^^

The following is an example bundle. It contains a simple encounter, condition, and procedure.

.. code-block:: json

   {
     "resourceType": "Bundle",
     "type": "batch",
     "entry": [
       {
         "fullUrl": "urn:uuid:111111",
         "resource": {
           "resourceType": "Encounter",
           "id": "111111",
           "status": "in-progress",
           "class": {
             "system": "http://terminology.hl7.org/CodeSystem/v3-ActCode",
             "code": "IMP",
             "display": "inpatient encounter"
           },
           "subject": {
             "reference": "Patient/doesnotmatter",
             "display": "This does not matter because it will be replaced"
           }
         }
       },
       {
         "fullUrl": "urn:uuid:222222",
         "resource": {
           "resourceType": "Condition",
           "id": "222222",
           "category": [
             {
               "coding": [
                 {
                   "system": "http://snomed.info/sct",
                   "code": "439401001",
                   "display": "diagnosis"
                 }
               ]
             }
           ],
           "severity": {
             "coding": [
               {
                 "system": "http://snomed.info/sct",
                 "code": "6736007",
                 "display": "Moderate"
               }
             ]
           },
           "code": {
             "coding": [
               {
                 "system": "http://snomed.info/sct",
                 "code": "368009",
                 "display": "Heart valve disorder"
               }
             ]
           },
           "subject": {
             "reference": "Patient/doesnotmatter",
             "display": "This does not matter because it will be replaced"
           },
           "encounter": {
             "reference": "urn:uuid:111111"
           }
         }
       },
       {
         "fullUrl": "urn:uuid:333333",
         "resource": {
           "resourceType": "Procedure",
           "id": "333333",
           "status": "completed",
           "category": {
             "coding": [
               {
                 "system": "http://snomed.info/sct",
                 "code": "103693007",
                 "display": "Diagnostic procedure (procedure)"
               }
             ],
             "text": "Diagnostic procedure"
           },
           "code": {
             "coding": [
               {
                 "system": "http://snomed.info/sct",
                 "code": "90105005",
                 "display": "Biopsy of soft tissue of forearm (Procedure)"
               }
             ],
             "text": "Biopsy of suspected melanoma L) arm"
           },
           "subject": {
             "reference": "Patient/doesnotmatter",
             "display": "This does not matter because it will be replaced"
           },
           "performedDateTime": "2014-02-03",
           "bodySite": [
             {
               "coding": [
                 {
                   "system": "http://snomed.info/sct",
                   "code": "368225008",
                   "display": "Entire Left Forearm"
                 }
               ],
               "text": "Left forearm"
             }
           ]
         }
       }
     ]
   }

Notice how the patient ids are ``Patient/doesnotmatter`` this is because the package is bound to a specific
patient. These references will all be updated to the id of the patient specified in the package **at
the time of import.** It is important to note that the patient references do not get updated at the
time of creating the package.

In addition, regular references within the bundle as `specified in the FHIR specification <https://www.hl7.org/fhir/bundle.html#references>`_
apply. In our example, this means that ``Condition.encounter.reference`` will be updated to reflect
the new id that will be assigned to the encounter resource.

.. warning::

   Only references within the bundle itself are guaranteed to resolve when working with Synapse packages.
   If conditional references are given, they will be attempted to be resolved by using the :doc:`grants <grants>`
   of the user that is performing the import. Remember that references are resolved **at the time of
   import**, not at the time of creating the package. So permissions and grants are in effect by the
   user that is doing the import.

The reason references are applied at import time is to ensure data integrity. Packages can be sitting
in a pending state ready to be imported. If references were resolved at the time of creating the package
those references may no longer apply once the user decides to import the package. The resolved resource
reference may have been deleted for example.

Sending the API request
^^^^^^^^^^^^^^^^^^^^^^^

Now that all the details are ready, the following API request can be made:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse

   Authorization: Bearer <token>
   Content-Type: application/json

   {
     "isForOrganization": true, <-- REQUIRED
     "description": "A small description", <-- REQUIRED,
     "securityCode": "123456789", <-- OPTIONAL.. Required if 'isForOrganization' = false,
     "fhirServerId": "9d9ba380-a269-4b3f-b5fa-178f70d5432c", <-- REQUIRED
     "patientId": "d741dcee-57ff-4cfe-b4a5-02746cc9e327", <-- REQUIRED,
     "expiresAt": null, <-- OPTIONAL
     "bundle": <bundle resource as json object> <-- REQUIRED
   }

Once the package is sent to the API, the API will validate the request and store the package for import.
If the package is not for an organization and it is for a person, the API will take the FHIR server
id and patient id provided in the request and find the :doc:`person resource <person>` that was bound
to that patient in that FHIR server. This binding happens during the :doc:`organization invite <organization-invite>`
process.

If the request was successful, the API will return a ``200 OK`` response.

The recipient(s) will be notified that a package has arrived and is ready for them to import. If the
package is going to an organization, all users of that organization will be notified.

.. note::

   Only SignalR messaging is currently supported for a real-time notification. Review :ref:`synapse-signalr-notifications`
   for more information in how to subscribe for real-time notifications.

.. _synapse-finding-packages:

Finding packages
------------------

There are a few different methods to finding packages that have arrived for the current user.

If you have the id of the Synapse package, you can retrieve it with the following request assuming the
id is ``80835d88-ef93-483e-a21e-5c18d121aea7``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Synapse/80835d88-ef93-483e-a21e-5c18d121aea7

   Authorization: Bearer <token>

If you would like to search for all packages that are for a recipient FHIR server, you can retrieve
it with the following request assuming the FHIR server id is ``9d9ba380-a269-4b3f-b5fa-178f70d5432c``:

.. code-block:: console

  GET https://api.bluebuttonpro.com/Synapse/fhirserver/9d9ba380-a269-4b3f-b5fa-178f70d5432c

  Authorization: Bearer <token>

If you would like to search for all packages that are for a recipient person or a list of people, you
can retrieve it with the following request:

.. code-block:: console

   POST https://api.bluebuttonpro.com/people

   Content-Type: application/json
   Authorization: Bearer <token>

   [
     "67003828-6267-4a47-9f7b-5e4ed659c06c",
     "6ed03923-98e7-4f0a-bc55-5f328d26c19e",
     "fa49baf8-c089-4a7c-98c1-3b2aad58f22b"
   ]

To clarify, to search for a single person id, the above request should be used with just one id in the
array list.

.. _synapse-user-intended-recipient:

All the requests above assume that the user is the intended recipient of the package. This means that
a package is available to the user if the package is for a target FHIR server and the user belongs in
the organization that has access to this FHIR server, or the package is for a target person and the
user has access to that person.

For the two search methods that return a list of packages, it is possible to filter packages based on
their current state. The query parameter ``searchType`` can be used with the following values:

* ``expiredOnly`` : Only retrieve the expired packages
* ``notExpiredOnly`` : Only retrieve the packages that have not expired
* ``importedOnly`` : Only retrieve the packages that have been imported
* ``notImportedOnly`` : Only retrieve the packages that have not yet been imported
* ``notExpiredAndNotImportedOnly`` : Only retrieve the packages that have not expired and have not been
  imported yet
* ``all`` : Retrieve all packages regardless of status

The default value as you may have guessed is ``notExpiredAndNotImportedOnly``.

This query parameter can be used like so, taking one of the examples from above:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Synapse/fhirserver/9d9ba380-a269-4b3f-b5fa-178f70d5432c?searchType=notExpiredOnly

   Authorization: Bearer <token>

Importing a package
-------------------

To import a package, you must first find the id of the pakage to import. This can be retrieved by one
of the finding methods described in :ref:`synapse-finding-packages`.

Once you have the id of the package, you will need to determine if the package is for a person. If it
is for a person, a security code will be required. This security code is set when first :ref:`creating
the package <synapse-creating-a-package>`. If the package is for an organization, the security code
can be ignored.

Once this information has been obtained, a request can be constructed like so:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse/356cf9d7-09bb-422a-b0e9-630e0cce293c/import

   Content-Type: application/json
   Authorization: Bearer <token>

   {
     "id": "356cf9d7-09bb-422a-b0e9-630e0cce293c", <-- REQUIRED (Must match url id)
     "securityCode": "123456" <-- OPTIONAL.. Required if package is for a person
   }

Once the request comes in, the API will ensure the current user :ref:`has access to the package <synapse-user-intended-recipient>`
and has sufficient permissions to import the package. If the package is being imported for a person,
the user must have administrator level permissions to that person. If the package is being imported
for an organization, the user must have write permissions to the target FHIR server of that organization.
If this check is successful, a :doc:`background process <background-jobs>` is initiated. Once the bakground
process is initiated, the server returns a ``202 Accepted`` response. The client can then use the :doc:`background
jobs <background-jobs>` endpoint to determine the status of this process.

.. warning::

   After a package has been imported. It can no longer be imported again.

During the background process, the API uses the FHIR server id that was resolved when :ref:`creating
the package <synapse-creating-a-package>` as the target FHIR server to import the resources in the bundle
into. What this means is that if the package is for an organization, the target FHIR server resolves
to whatever was supplied when :ref:`creating the package <synapse-creating-a-package>`. However, if
the package is for a person, the FHIR server that is resolved is the :ref:`PFR <definitions-pfr>` for
that person. This does mean that if the package is for a person, a :ref:`PFR <definitions-pfr>` for
that person must have been provisioned for the person before it can be imported.

Each resource in the bundle is iterated over and all reference properties that can point to a
patient will have their values updated to the id of the patient given when :ref:`creating the package
<synapse-creating-a-package>`. For example, if the patient id is ``123456`` and an encounter is currently
being processed, the ``Encounter.subject`` property will be overwritten to ``{ "reference": "Patient/123456" }``.

Additionally, the API ensures duplicate resources are not created again in the FHIR server. This is
done by checking the `identifier property <https://www.hl7.org/fhir/datatypes.html#Identifier>`_ of
each resource. As an example, suppose an encounter resource needs to be imported with the following
identifiers:

.. code-block:: json
   
   {
     "resourceType": "Encounter",
     "identifier": [
       {
         "system": "http://www.example.com",
         "value": "value123"
       },
       {
         "system": "http://www.example.com",
         "value": "value456"
       }
     ]
   }

The API will check to see if an existing encounter exists by either of the identifiers. The identifier
check is ORed. Specifically, this is the FHIR query parameter that is executed for the search:

.. code-block:: console

   ?identifier=http://www.example.com|value123,http://www.example.com|value456

If no existing resource is found, the resource to be imported is created.

If one resource is found, that resource is updated with the new resource to be imported.

If more than one resource is found, an error is logged and written in the result bundle.

Infering import result
----------------------

In the process of importing a package, the result of processing each resource in the bundle is tracked
and subsequently logged to a result bundle. The result bundle will look very similar to the FHIR specification's
`bundle interaction result specification. <https://www.hl7.org/fhir/http.html#transaction-response>`_.

To retrieve this bundle, it can be downloaded by sending the following request, assuming the id of the
package is ``356cf9d7-09bb-422a-b0e9-630e0cce293c``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/BackgroundJobs/356cf9d7-09bb-422a-b0e9-630e0cce293c/importbundleresult

   Authorization: Bearer <token>

This result bundle should indicate the result of each resource that was processed. If an error occurred,
there will be an ``OperationOutcome`` resource detailing why this resource has failed to import.

Downloading a package
---------------------

In some cases, it is desirable to download the bundle that is contained in a package without importing
it. This can be done by making the following request, assuming the id of the package is ``356cf9d7-09bb-422a-b0e9-630e0cce293c``:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse/356cf9d7-09bb-422a-b0e9-630e0cce293c/download

   Authorization: Bearer <token>

The API will respond with the bundle contained in the package as a file. The user :ref:`must have access
<synapse-user-intended-recipient>` to the package in order to download it.

.. note::

   You can always download the contents of a package even if it has already been imported or has expired.

Deleting a packge
-----------------

To delete a package, retrieve the id of the package by using one of the :ref:`find methods <synapse-finding-packages>`.

Assuming the id of the package is ``356cf9d7-09bb-422a-b0e9-630e0cce293c``, the request can be constructed
like so:

.. code-block:: console

   DELETE http://api.bluebuttonpro.com/Synapse/356cf9d7-09bb-422a-b0e9-630e0cce293c

   Authorization: Bearer <token>

The user :ref:`must have access <synapse-user-intended-recipient>` to the package and have sufficient
permissions to delete the package. If the package is for a person, the user must have owner level permissions
to that person. If the package is for an organization, the user must have write permissions to the target
FHIR server of that organization.

.. _synapse-signalr-notifications:

SignalR notifications
---------------------

TODO