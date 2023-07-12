Importing an EOB Bundle from CMS
================================

BlueButtonPRO integrates with CMS and allows a user to download a bundle of EOB (`Explanation of Benefit <https://www.hl7.org/fhir/explanationofbenefit.html>`_)
resources from CMS and then process them and store them in their :ref:`personal FHIR server <definitions-pfr>`.
The process of taking an EOB and extracting various resources of that EOB is explained in :ref:`this
section <importing-and-processing-importing-an-eob-bundle>`.

Creating a Context
------------------

BlueButtonPRO handles the interaction with CMS and allows a user to login using their credentials by
using our API. To begin this process, a context needs to be created. This context is then persisted
between the interaction with CMS. This context can be created at https://api.bluebuttonpro.com/Import/cms-eob-import-ctx/create.
This endpoint takes two **query** parameters:

returnUrl - **REQUIRED**
   This the url that our API will redirect to after the user has authenticated themselves with CMS and
   the EOB import process has started. This return url can contain query parameters if needed. All query
   parameters will be echoed back by the API.

keepEobs
   This is an optional parameter and is ``false`` by default. When processing each EOB, the EOB resource
   itself is discarded after the resources have been extracted from the EOB and created in the target
   FHIR server. To keep the EOB and also create it in the FHIR server instead of discarding it, set
   this query parameter to ``true``.

In addition to these query parameters, a :doc:`FHIR server context <header-values>` header is required.
This indicates which FHIR server the resources should be created in. Once all this information is obtained
the request can be made:

.. code-block:: console

   POST https://api.bluebuttonpro.com/Import/cms-eob-import-ctx/create?returnUrl=https://www.goodhealthclinic.com?queryParam1=myvalue&keepEobs=true

   Authorization: Bearer <token>
   FhirServerId-Context: 3fa85f64-5717-4562-b3fc-2c963f66afa6

In the following request we can see that the value of ``returnUrl`` is https://www.goodhealthclinic.com?queryParam1=myvalue.
This url contains a query parameter called ``queryParam1``. This query parameter will be echoed back
when the API redirects the user to this return url after they have logged in and started the EOB import
process. This can be useful in the event your application needs to persist some state. Additionally,
``keepEobs`` is set to ``true`` indicating that each EOB should also be created in the FHIR server rather
than discard it.

Once the request has been made the API will create the context. A ``200 OK`` response will be returned
with a JSON response that contains an identifer and a security token for the context:

.. code-block:: json

   {
      "id": "5d51e5b6-5e62-4ae4-8afb-ef54d83faacc",
      "contextToken": "mysecuritytoken"
   }


These details will need to be used when you direct the user to begin the login process with CMS. This
is explained :doc:`here <cms-login>`.

.. warning::

   This context is only valid for 15 minutes