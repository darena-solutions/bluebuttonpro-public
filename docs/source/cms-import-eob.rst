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
with a plain text body that contains the identifier for this context. This identifier will need to be
used when you direct the user to begin the login process with CMS.

.. warning::

   This context is only valid for 15 minutes

Begin CMS Login
---------------

The next step in the process is to open a browser in your application (this can be the system browser
or an embedded browser). You must then direct the user to https://api.bluebuttonpro.com/CmsInteraction/cms-login.
This endpoint requires the ``contextId`` query parameter. This is the unique identifier that you should
have received after creating the context explained in the previous section. Assuming that the identifier
value is ``fe53b80d-215a-4b4d-a763-2ae4f219e7dc``, you should set your browser url to:

.. code-block:: console

   GET https://api.bluebuttonpro.com/CmsInteraction/cms-login?contextId=fe53b80d-215a-4b4d-a763-2ae4f219e7dc

At this point the API will handle the interaction with CMS's identity provider. It will redirect the
user to CMS's login page and ask the user to login. Once the user has logged in, our API will begin
the EOB import process.

.. note::

   Please review :ref:`this section <importing-and-processing-importing-an-eob-bundle>` to understand
   how the EOB process is performed.

Once the EOB import process has started, the API will take the return url from the context created in
the previous section and return a :doc:`background job <background-jobs>` id which can be located in
the ``background_job_id`` query parameter.

Taking our previous example, when the context was a created a return url of https://www.goodhealthclinic.com?queryParam1=myvalue
was used. In this example, the API will redirect the browser to:

.. code-block:: console

   GET https://www.goodhealthclinic.com?queryParam1=myvalue&background_job_id=ec838c02-5ad5-4a14-aa0a-55d4a30b07ff

Notice the ``background_job_id`` query parameter and notice how the previous query parameter is echoed
back.

Errors During Interaction
-------------------------

If any errors occurred during the interaction with CMS, then the API will redirect the user to the return
url of the context and append an ``error_description`` query parameter that will describe the error
that occurred. Taking the return url from our previous example, let's assume the context has expired
in the time the user is attempting to log in but before the EOB import process started. The API will
redirect the browser to:

.. code-block:: console

   GET https://www.goodhealthclinic.com?queryParam1=myvalue&error_description=This+context+has+expired

Notice the ``error_description`` query parameter.