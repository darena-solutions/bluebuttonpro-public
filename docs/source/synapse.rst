Synapse
=======

Synapse is a functionality that allows resources for a specific patient to be shared with an organization,
user, or person.

.. _synapse-creating-a-package:

Creating a package
------------------

Creating a Synapse package is a little different based on the sending and receiving party type. But
for all packages, a FHIR bundle will need to be created first. This bundle should contain FHIR resources
for one specific target patient. The FHIR patient resource must exist in this bundle.

Below is an example of a bundle:

.. code-block:: json

   {
     "resourceType": "Bundle",
     "type": "batch",
     "entry": [
       {
         "fullUrl": "urn:uuid:444444",
         "resource": {
           "resourceType": "Patient",
           "name": [
             {
               "family": "Doe",
               "given": [ "Jane", "X" ]
             }
           ]
         }
       },
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

Notice how the patient ids are ``Patient/doesnotmatter`` this is because at the time of :ref:`importing
the package <synapse-importing-a-package>`, a patient id will need to be provided. All references in
the bundle that can reference a patient will have their references updated to that specified patient
id. :ref:`Read more here <synapse-importing-a-package>` to understand how importing a package works.

In addition, regular references within the bundle as `specified in the FHIR specification <https://www.hl7.org/fhir/bundle.html#references>`_
apply. In our example, this means that ``Condition.encounter.reference`` will be updated to reflect
the new id that will be assigned to the encounter resource. Conditional references when working with
synapse is not supported at this time.

Once the bundle is available and a package is ready to be created, the following ways are different
ways to send packages to other entities:

Organization-to-organization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

An organization can send packages to another organization, provided that they have an active :doc:`directory listing <directory-listing>`.
The request to the API must be constructed like so:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse

   Content-Type: application/json
   Authorization: Bearer <token>

   {
     "senderType": "Organization",
     "receiverType": "Organization",
     "receiverId": "19bde8b1-e670-40c7-8181-d75a900ce1b8",
     "description": "A simple description of the package",
     "fhirServerId": "17132b89-30bf-44a8-9f24-7d547b569eb1",
     "bundle": { <bundle resource> }
   }

You will have to specify that the sender type and receiver type are organizations. You will then have
to include the id of the receiving organization in the ``receiverId`` field. A small description for
the package will be required. A FHIR server id is also required. You will need to provide this so that
the application can determine under which FHIR server the package is being sent on behalf of. This is
used in various checks which include ensuring that the current logged in user has access to that FHIR
server. In addition, this FHIR server is also used in resolving any conditional references that exist
in the bundle.

Finally, the bundle is also included in the request body in the ``bundle`` parameter.

.. note::

   The bundle should be included as JSON, not as a string of JSON.

Organization-to-user
^^^^^^^^^^^^^^^^^^^^

An organization can send packages to a specific user. To do that, the request must be constructed like
so:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse

   Content-Type: application/json
   Authorization: Bearer <token>

   {
     "senderType": "Organization",
     "receiverType": "User",
     "receiverId": "example_user@gmail.com",
     "description": "A simple description of the package",
     "fhirServerId": "17132b89-30bf-44a8-9f24-7d547b569eb1",
     "securityCode": "123456",
     "bundle": { <bundle resource> }
   }

The receiver type in this scenario should be set to ``"User"``, and the receiver id must be the email
address of the user that is receiving the package. In this scenario, a security code is required. When
the receiving user is ready to :ref:`import <synapse-importing-a-package>` or :ref:`download <synapse-downloading-a-package>`
the package, they will need to provide the same security code before the package contents can be accessed.
This security code is hashed and stored in our system.

Organization-to-linked person
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A linked person is a person that has been associated with a FHIR patient resource in an organization's
FHIR server. This is done by going through the :doc:`organization invite process <organization-invite>`.
Once a link has been established between a person resource and a FHIR patient resource in the organization's
FHIR server, the following request can be constructed:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse

   Content-Type: application/json
   Authorization: Bearer <token>

  {
     "senderType": "Organization",
     "receiverType": "Person",
     "description": "A simple description of the package",
     "fhirServerId": "17132b89-30bf-44a8-9f24-7d547b569eb1",
     "patientId": "1c1e3f94-99e5-4c67-a315-c5c16a958c41",
     "securityCode": "123456",
     "bundle": { <bundle resource> }
   }

This request is a little different from the others. Notice that a receiver id is not provided. This
is because the receiver will ultimately be a person resource. The id of this person will be determined
internally by the API. The API finds the id by using the values provided in ``fhirServerId`` and ``patientId``.
The patient id must be the the id of the FHIR patient resource that exists in the **organization's**
FHIR server. And of course the FHIR server id must be the id of the FHIR server where this patient exists.
Remember that the patient id must reflect the id of the patient that was used when going through the
:doc:`organization invite process <organization-invite>`.

A security code is also required in this type of request. The receiving user that has access to this
person will need to provide this security code before the package can be :ref:`imported <synapse-importing-a-package>`
or :ref:`downloaded <synapse-downloading-a-package>`.

Linked person-to-organization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the same way an organization can send to a linked person, a linked person can send a package back
to an organization. This is how the request will look like:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse

   Content-Type: application/json
   Authorization: Bearer <token>

  {
     "senderType": "Person",
     "receiverType": "Organization",
     "description": "A simple description of the package",
     "fhirServerId": "17132b89-30bf-44a8-9f24-7d547b569eb1",
     "patientId": "1c1e3f94-99e5-4c67-a315-c5c16a958c41",
     "bundle": { <bundle resource> }
   }

The request should look very similar compared to sending a package from an organization to a linked
person. The sender and receiver type are flipped since the package is now being sent from the linked
person to an organization. The FHIR server id and patient id have the same values. This is an important
thing to understand. The FHIR server id and patient id when sending between an organization and linked
person, regardless of who is the sender and who is the receiver, must always be the values that were
used during the :doc:`organization invite process <organization-invite>`, and always reflects the ids
belonging to the organization. This should not be confused with a :ref:`PFR <definitions-pfr>` id and
the patient id in that :ref:`PFR <definitions-pfr>`.

Limitations
^^^^^^^^^^^

* Users can never be the sender type.
* Linked person to linked person messaging is not supported.

Additional notes
^^^^^^^^^^^^^^^^

Once the package is sent to the API, the API will validate the request and store the package for import.
If the request was successful, the API will return a ``200 OK`` response.

The recipient(s) will be notified that a package has arrived and is ready for them to import. If the
package is going to an organization, all users of that organization will be notified.

.. note::

   Only SignalR messaging is currently supported for a real-time notification. Review :ref:`synapse-signalr-notifications`
   for more information in how to subscribe for real-time notifications.

.. _synapse-finding-packages:

Finding packages
------------------

There are a couple different methods to finding packages.

If you have the id of the Synapse package, you can retrieve it with the following request assuming the
id is ``80835d88-ef93-483e-a21e-5c18d121aea7``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Synapse/80835d88-ef93-483e-a21e-5c18d121aea7

   Authorization: Bearer <token>

To find all packages where the :ref:`current user is the recipient <synapse-intended-receiver>`, the
following request can be made:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Synapse/received

   Authorization: Bearer <token>

This request can be further filtered by a query parameter. The query parameter ``searchType`` can be
used with the following values:

* ``expiredOnly`` : Only retrieve the expired packages
* ``notExpiredOnly`` : Only retrieve the packages that have not expired
* ``importedOnly`` : Only retrieve the packages that have been imported
* ``notImportedOnly`` : Only retrieve the packages that have not yet been imported
* ``notExpiredAndNotImportedOnly`` : Only retrieve the packages that have not expired and have not been
  imported yet
* ``all`` : Retrieve all packages regardless of status

The default value as you may have guessed is ``notExpiredAndNotImportedOnly``.

This query parameter can be used like so:

.. code-block:: console

   GET https://api.bluebuttonpro.com/Synapse/received?searchType=notExpiredOnly

   Authorization: Bearer <token>

.. _synapse-intended-receiver:

Intended receiver
-----------------

The current logged in user is the intended receiver if:

* The package receiver is an organization and the user is part of that organization.
* The package receiver is a user and the current logged in user is that user.
* The package receiver is a person and the current logged in user has owner or administrator access
  to that person.

.. _synapse-importing-a-package:

Importing a package
-------------------

To import a package, you must first find the id of the package to import. This can be retrieved by one
of the finding methods described in :ref:`synapse-finding-packages`.

If the package receiver is a user or person, a security code will be required. This security code was
established when the package was :ref:`first created <synapse-creating-a-package>`. Finally, when importing
a package that is not being sent between an organization and linked person, the API requires that the
package be imported based on a FHIR patient resource. Thus, the id of that FHIR patient resource and
the id of the FHIR server where this patient exists must be supplied. This patient id will then be used
to overwrite all patient references in the package bundle to reflect that specified patient id. With
these values, the request can be constructed like so, assuming the id of the package to import is
``347175fd-7b94-4a7d-8561-9478a4ea3192``:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse/347175fd-7b94-4a7d-8561-9478a4ea3192/import

   Content-Type: application/json
   Authorization: Bearer <token>

   {
     "id": "347175fd-7b94-4a7d-8561-9478a4ea3192", <-- Must match id in URL
     "patientId": "8ddcbd6f-705d-43f0-b09e-6668323c43c8", <-- NOT required if package is sent between an organization and linked person
     "fhirServerId": "c7db9f2c-2768-4064-a107-0fbb5abfa5d4", <-- NOT required if package is sent between an organization and linked person
     "securityCode": "123456", <-- Only required if receiver is user or person
   }

To re-iterate, if the package is being sent between an organization and a linked person, the FHIR server
id and patient is not required. This will be automatically determined internally by the API.

.. note::

   Note that when importing a package where the receiver is a person, a :ref:`PFR <definitions-pfr>`
   must have been provisioned for that person. This also means that a package can only be imported to
   a :ref:`PFR <definitions-pfr>` if the package receiver is a person.

Once the request comes in, the API will ensure the current user :ref:`has access to the package <synapse-intended-receiver>`.
If this check is successful, a :doc:`background process <background-jobs>` is initiated. Once the background
process is initiated, the server returns a ``202 Accepted`` response. The client can then use the :doc:`background
jobs <background-jobs>` endpoint to determine the status of this process.

.. warning::

   After a package has been imported. It can no longer be imported again.

During the background process, each resource in the bundle is iterated over and all reference properties
that can point to a patient will have their values updated to the id of the patient given in the :ref:`import
request <synapse-importing-a-package>`. For example, if the patient id is ``123456`` and an encounter
is currently being processed, the ``Encounter.subject`` property will be overwritten to ``{ "reference": "Patient/123456" }``.

.. note::

   As a reminder, when importing a package for a person, the API will determine the :ref:`PFR <definitions-pfr>`
   id and patient id to be used **internally**.

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

Inferring import result
-----------------------

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

.. _synapse-downloading-a-package:

Downloading a package
---------------------

In some cases, it is desirable to download the bundle that is contained in a package without importing
it. This can be done by making the following request, assuming the id of the package is ``356cf9d7-09bb-422a-b0e9-630e0cce293c``:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Synapse/356cf9d7-09bb-422a-b0e9-630e0cce293c/download

   Authorization: Bearer <token>

The API will respond with the bundle contained in the package as a file. The user :ref:`must have access
<synapse-intended-receiver>` to the package in order to download it.

.. note::

   You can always download the contents of a package even if it has already been imported or has expired.

Deleting a package
------------------

To delete a package, retrieve the id of the package by using one of the :ref:`find methods <synapse-finding-packages>`.

Assuming the id of the package is ``356cf9d7-09bb-422a-b0e9-630e0cce293c``, the request can be constructed
like so:

.. code-block:: console

   DELETE http://api.bluebuttonpro.com/Synapse/356cf9d7-09bb-422a-b0e9-630e0cce293c

   Authorization: Bearer <token>

The user :ref:`must have access <synapse-intended-receiver>` to the package to delete the package.

.. _synapse-signalr-notifications:

SignalR notifications
---------------------

TODO