CMS Login
=========

There are a couple situations where logging into CMS is required by a user. This is required when :doc:`importing
an EOB bundle from CMS <cms-import-eob>`, and when :ref:`downloading an EOB bundle from CMS that represents
a synapse package <synapse-downloading-an-eob-bundle-from-cms>`.

Once the context has been created for either of those situations, the next step in the process is to
open a browser in your application (this can be the system browser or an embedded browser). You must
then direct the user to a URL depending on the type of context.

* If importing an EOB bundle from CMS, then https://api.bluebuttonpro.com/CmsInteraction/cms-login/cms-eob-import.
* If downloading an EOB bundle from CMS for a synapse package, then https://api.bluebuttonpro.com/CmsInteraction/cms-login/synapse-cms-eob-download.

Both of these endpoints require an ``id`` and ``token`` query parameter. These values will be the values
that the API returned when the contexts were created. Review :doc:`importing an EOB bundle from CMS
<cms-import-eob>` and :ref:`downloading an EOB bundle from CMS to represent a synapse package <synapse-downloading-an-eob-bundle-from-cms>`
for more information on that step. Once this information is available, you should set your browser url
to:

.. code-block:: console

   GET https://api.bluebuttonpro.com/CmsInteraction/cms-login/cms-eob-import?id=fe53b80d-215a-4b4d-a763-2ae4f219e7dc&token=mysecuritytoken

for importing an EOB bundle from CMS, and:

.. code-block:: console

   GET https://api.bluebuttonpro.com/CmsInteraction/cms-login/synapse-cms-eob-download?id=fe53b80d-215a-4b4d-a763-2ae4f219e7dc&token=mysecuritytoken

for downloading an EOB bundle from CMS for a synapse package.

At this point the API will handle the interaction with CMS's identity provider. It will redirect the
user to CMS's login page and ask the user to login. Once the user has logged in, our API will begin
a background job to complete the desired process.

.. note::

   Please review :ref:`this section <importing-and-processing-importing-an-eob-bundle>` to understand
   how the EOB import process is performed.

Once the background job has started, the API will take the return url that was supplied when creating
the context and return a :doc:`background job <background-jobs>` id which can be located in the ``background_job_id``
query parameter.

As an example, if the context was a created with a return url of https://www.goodhealthclinic.com?queryParam1=myvalue,
then the API will redirect the browser to:

.. code-block:: console

   GET https://www.goodhealthclinic.com?queryParam1=myvalue&background_job_id=ec838c02-5ad5-4a14-aa0a-55d4a30b07ff

Notice the ``background_job_id`` query parameter and notice how the previous query parameter is echoed
back.

Errors During Interaction
-------------------------

If any errors occurred during the interaction with CMS, then the API will redirect the user to the return
url of the context and append an ``error_description`` query parameter that will describe the error
that occurred. Taking the return url from our previous example, let's assume the context has expired
in the time the user is attempting to log in but before the background job started. The API will redirect
the browser to:

.. code-block:: console

   GET https://www.goodhealthclinic.com?queryParam1=myvalue&error_description=This+context+has+expired

Notice the ``error_description`` query parameter.