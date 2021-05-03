Importing and Processing
========================

BlueButtonPRO allows various ways to import resources in bulk into a FHIR server. Most of the import
functionality relies on performing the operation asynchronously in a :doc:`background job <background-jobs>`.
Each type of import has a very distinct set of requirements and performs a very specific task. These
imports are explained in this page.

Importing a Bundle
------------------

Importing a bundle is different from the bundle interaction endpoint specified in the `FHIR specification <https://www.hl7.org/fhir/http.html#transaction>`_.
This functionality is essentially a mirror of the process that occurs when :ref:`importing a synapse
package <synapse-importing-a-package>`. The main purpose of this functionality is to allow the ability
to import resources in bulk that targets a specific patient but without having to go through the :doc:`synapse
<synapse>` process. All the limitations and how the bundle is processed is the same (such as checking
for existing resources using a resource's identifier(s), updating patient reference values, etc.) as
with :ref:`importing a synapse package <synapse-importing-a-package>`. Review that section for a more
detailed explanation as to how the bundle is processed.

The endpoint to start this process is https://api.bluebuttonpro.com/Import/bundle. The endpoint accepts
the following parameters:

patientId
   This is the id of the patient that all resources in the bundle will have their references updated
   with. As an example, if an encounter exists in the bundle, the ``Encounter.subject`` property will
   be overwritten and set to: ``Patient/**patientId**``. Refer to the :ref:`synapse-importing-a-package`
   synapse section for more information as to how patient references are handled.

bundle
   This is the FHIR bundle resource in JSON representation that needs to be imported

In addition to the body of the request, a :doc:`FHIR server context <header-values>` header is required.

Once all this information is available, the request can be constructed:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Import/bundle

   Content-Type: application/json
   Authorization: Bearer <token>
   FhirServerId-Context: 3fa85f64-5717-4562-b3fc-2c963f66afa6

   {
     "patientId": "c7b42dce-0cd2-41b6-907f-c2b17f56ad88",
     "bundle": { <bundle resource> }
   }

Once the request has been made, the API will return a ``202 Accepted`` response which will indicate
that a :doc:`background job <background-jobs>` has started. The response will also contain information
about the background job.

.. _importing-and-processing-importing-an-eob-bundle:

Importing an EOB Bundle
-----------------------

EOB (short for `Explanation of Benefit <https://www.hl7.org/fhir/explanationofbenefit.html>`_) resources
can be processed by BlueButtonPRO. Each EOB resource contains specific information about medications,
encounters, conditions, procedures, etc. The API can inspect an EOB and extract this information
out of the EOB and create individual resources out of that information. For example, if an EOB contains
information about procedures that were preformed, the API will extract this information and create individual
`Procedure <https://www.hl7.org/fhir/procedure.html>`_ resources out of it. These resources are then
created in a specified FHIR server.

This functionality only works with :ref:`personal FHIR servers <definitions-pfr>`. This is because each
resource that is extracted out of an EOB will have its patient reference updated to the patient that
exists in the :ref:`personal FHIR server <definitions-pfr>`. For example, when a procedure resource
is extracted, it will have the ``Procedure.subject`` property overwritten and set to ``Patient/*patient id*``.

To process a bundle of EOB resources in bulk, the following endpoint can be used: https://api.bluebuttonpro.com/Import/eobbundle.
This endpoint accepts a `FHIR bundle resource <https://www.hl7.org/fhir/bundle.html>`_ as the sole parameter
in the request body. In addition to the body of the request, a :doc:`FHIR server context <header-values>`
header is required. Once all this information is available, the request can be constructed:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Import/eobbundle

   Content-Type: application/json
   Authorization: Bearer <token>
   FhirServerId-Context: 3fa85f64-5717-4562-b3fc-2c963f66afa6

   {
     <bundle resource>
   }

Once an EOB is processed and the resources are extracted, the EOB is then subsequently dismissed. The
EOB resource itself is not created in the FHIR server. In order to also keep the EOB resource and create
it in the FHIR server, set the ``keepEobs`` query parameter to ``true`` when making the request:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Import/eobbundle?keepEobs=true

.. note::

   The ``keepEobs`` query parameter is set to ``false`` by default.

Once the request has been made, the API will return a ``202 Accepted`` response which will indicate
that a :doc:`background job <background-jobs>` has started. The response will also contain information
about the background job.

The background job will iterate over each EOB and extract the resources contained in those EOBs. For
each of those extracted resources, the patient reference value is overwritten and set to the patient
that exists in the personal FHIR server. A check is then performed to determine if the resource already
exists in the FHIR server. If it already exists, the resource is skipped and the next resource is checked.
If the resource does not exist, it is created. The API determines if a resource exists by taking the
identifiers of each resource and an additional value that can uniquely identify the resource. As an
example, for a medication resource, the API wll take the identifiers of that medication resource and
it will also take the medication code. The identifiers and medication code are then used in an ANDed
search.

The result of each operation on a resource is stored and contained in a bundle which can later be inferred.
This is the same process as explained in :ref:`this synapse section <synapse-inferring-import-result>`.

Limitations
^^^^^^^^^^^

* Only the EOB resources in the bundle will be processed. If the bundle were to have a resource that
  is not an EOB, that resource will be ignored.

* The import only works with :ref:`personal FHIR servers <definitions-pfr>`

Additional Notes
^^^^^^^^^^^^^^^^

BlueButtonPRO integrates with CMS and allows the user to log in with their CMS account to download EOBs
from CMS and then subsequently import it into a personal FHIR server. This process is detailed in :doc:`cms-import-eob`.

Importing CCDAs
---------------

BlueButtonPRO can accept a CCDA or a collection of CCDAs and process them into individual FHIR resources.
Each FHIR resource that is extracted from the CCDA must conform to a profile in the `US Core IG <https://www.hl7.org/fhir/us/core/>`_.
This means that any required values as dictated by the implementation guide must exist in the CCDA.
If a value could not be extracted from the CCDA, an error will be thrown by the API and that CCDA will
not be processed. The validation is not limited to just required fields, we validate each resource against
the relevant profile fully. For example, for a patient resource, we validate against `this profile <https://www.hl7.org/fhir/us/core/StructureDefinition-us-core-patient.html>`_.
All bindings, invariants, required fields, cardinality, etc. will be validated.

We understand that CCDAs are complex and implementations may vary between organizations. We try to follow
the most utilized standard. Our mapping library is open source and is available on `GitHub <https://github.com/darena-solutions/ccda-fhir-converter>`_.
If you feel the conversion is not accurate or have any other suggestion, please feel free to create
an issue in that repository.

Once the CCDA has completed processing, they physical CCDA is then stored in our storage solution. A
link to download this CCDA is generated. This link is available and can be found in the `DocumentReference <https://www.hl7.org/fhir/documentreference.html>`_
FHIR resource that is generated, or from the result of the background job. This is explained in more
detail below.

The endpoint to start importing CCDAs is https://api.bluebuttonpro.com/Import/ccda. This endpoint accepts
a file as the sole parameter of the request body. In addition to the body of the request, a :doc:`FHIR
server context <header-values>` header is required. This is how the request should look like:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Import/ccda

   Content-Type: multipart/form-data; boundary=--abc123
   Authorization: Bearer <token>
   FhirServerId-Context: 3fa85f64-5717-4562-b3fc-2c963f66afa6

   ----abc123
   Content-Disposition: form-data; name="ccda"; filename="ccda.xml"
   Content-Type: text/xml

   <ClinicalDocument>...</ClinicalDocument>

.. note::

   Creating a request body with a file can be complex. We recommend using a library to handle generating
   this request rather than manually trying to create the request.

Once the request is handled by the API a ``202 Accepted`` response will be returned indicating that
a background job has started that will process the CCDA.

.. note::

   It is possible to send a collection of CCDAs instead of just one. To do this, place all CCDAs that
   need to be processed in a ``.zip`` file and send this file in the request. The CCDAs need to be placed
   in the root of the ``.zip`` file. All directories and subdirectories of the ``.zip`` file will be
   ignored.

In the background process, the CCDA (or each CCDA if a zip file was provided) will be processed by extracting
all resources in the CCDA to their FHIR resource representation. These FHIR resources are then validated
against the `US Core IG <https://www.hl7.org/fhir/us/core/>`_. All resources must pass the validation.
If at least one resource fails, an error is logged and the CCDA is not processed. If a collection of
CCDAs were provided, the next CCDA is then processed.

After validation each resource is iterated over. The API will determine whether to create this resource
as a new resource, or update an existing resource. This is done by taking the identifiers of each resource
and performing a search with them. The identifers are used in an ORed search. If no existing resource
was found, the resource is created as a new resource. If one existing resource was found, the existing
resource is updated. If more than one resource matched the identifier search, an error is logged and
the CCDA is not processed any further. The next CCDA, if a zip file was provided, begins processing.

.. note::

   No resources are created or updated *during* the existence check. Once all resources are iterated over
   and are determined to not have more than one matching existing resource, *then* the process to perform
   the actual creates and updates are performed.

Once the resources are done processing, the CCDA is stored in our internal storage solution. This allows
a user to later download the CCDA if needed. A link to the physical CCDA is generated. A `DocumentReference
FHIR resource <https://www.hl7.org/fhir/documentreference.html>`_ is also generated that contains this
link. This resource is also created in the FHIR server.

.. note::

   It is possible that the DocumentReference resource is updated instead of created. This will occur
   in situations where a CCDA is re-uploaded. This is done by reading the identifier(s) that identify
   a CCDA. We read these values from ``<ClinicalDocument> <id>..identifier..</id> </ClinicalDocument>``.

Each CCDA will have a result object tied to them to indicate the result of processing that CCDA. This
information can be obtained by querying the :doc:`background job <background-jobs>`. Assuming that the
id of the background job is ``091a5ebf-e766-4ac4-bf1f-790e8f3ca6e2``, the following request can be made
to obtain the status of this background job:

.. code-block:: console

   GET https://api.bluebuttonpro.com/BackgroundJobs/091a5ebf-e766-4ac4-bf1f-790e8f3ca6e2

   Authorization: Bearer <token>

This will return an object that looks like the following:

.. code-block:: json

   {
      "id": "091a5ebf-e766-4ac4-bf1f-790e8f3ca6e2",
      "createdOn": "01-01-2020",
      "modifiedOn": "01-01-2020",
      "completedOn": "01-01-2020",
      "type": "CCdaProcessor",
      "status": "Completed",
      "fhirServerId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "userId": "d94b3379-9f3f-40b4-b0f3-a076e214834a",
      "clientId": "c906d1e11019437b8a275fef1dc8631e",
      "percentageComplete": 100,
      "title": "Importing CCDA(s)",
      "description": "Importing CCDAs into the FHIR server: Good Health Clinic",
      "patientOnly": false,
      "results": [
         {
            "fileName": "ccda.xml",
            "patientId": "5f37d5d0-7054-4c2e-a060-2d06166fef0b",
            "documentReferenceId": "91830afe-30c9-459c-bfb4-a5781d3f40b5",
            "success": true,
            "downloadUrl": "https://api.bluebuttonpro.com/Downloads/ccda/91830afe-30c9-459c-bfb4-a5781d3f40b5/fhir-server/3fa85f64-5717-4562-b3fc-2c963f66afa6/file/7ae141d7-4ed1-4d9d-9f36-7397c6fec5d3"
         }
      ]
   }

.. note::

   Take note of the ``patientOnly`` property above. The purpose of this property is explained in the
   :ref:`importing-and-processing-patient-only-processing` section.

The key property to look at is the ``results`` property. This property is an array because it will contain
a result object for each CCDA that was processed. Multiple CCDAs can be processed if provided in a zip
file. If only one CCDA is given, then this property will still be an array which contains just one object.
This object contains the following properties:

fileName
   The filename of the physical CCDA file that was processed.

patientId
   This is the id of the patient that was created or updated by reading the CCDA. The patient resource
   is a required resource that must exist in the CCDA.

documentReferenceId
   This is the id of the document reference that was created or updated which contains the link to download
   the physical CCDA file.

success
   Indicates if the CCDA was processed successfully or not. If the CCDA was not processed successfully,
   then there will be error messages in the ``errorMessages`` property describing the issue.

errorMessages
   This is an array of string values that describes any issues that occurred while processing the CCDA.
   This property is not returned if the CCDA was process successfully (``success = true``).

downloadUrl
   This is the link to download the physical CCDA. While this link also exists in the DocumentReference
   resource, it is also provided here for ease-of-use.

.. _importing-and-processing-patient-only-processing:

Patient-only Processing
^^^^^^^^^^^^^^^^^^^^^^^

It is possible to process a CCDA and process it only for the `FHIR Patient resource <https://www.hl7.org/fhir/patient.html>`_
and ignore all other resources. In this situation, the patient resource is validated and created or
updated as normal, and all other resources are ignored. The physical CCDA is still uploaded into our
internal storage solution and a DocumentReference is also still created. This can by done by using the
``patientOnly`` query parameter and setting it to ``true``:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Import/ccda?patientOnly=true

When querying the background job endpoint for the status of the background job, the ``patientOnly``
property will be set to ``true`` to indicate that only patients are being processed in the CCDA(s).