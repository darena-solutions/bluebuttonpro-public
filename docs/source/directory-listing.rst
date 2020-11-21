Directory Listings
==================

Directory listings are a way for organizations to indicate that they are open for accepting Synapse
packages from other organizations.

A user in an organization can create one or more directory listings. The reason for this is because
it is possible that organizations want to display different names for locations that may be more recognizable
to another organization.

Creating a listing
------------------

To create a listing, you simply need to provide two values to the API. One being the organization id,
and another being the display name of that listing. Once these values are obtained, the request can
be constructed like so:

.. code-block:: console

   POST https://api.bluebuttonpro.com/DirectoryListings

   Content-Type: application/json
   Authorization: Bearer <token>

   {
     "organizationId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
     "displayName": "My listing name"
   }

Once the request is sent, the API will ensure that the current user belongs to the specified organization.
The listing will be created in an active state if this check is successful and a ``201 Created`` response
will be returned from the API.

Once at least one listing is created for an organization, they can now recieve Synapse packages from
a different organization.

.. _directory-listing-searching-for-listings:

Searching for listings
----------------------

The directory listings can be searched to determine which organizations are accepting Synapse packages.
This can be done by providing a query parameter to the search endpoint. The value is used in a *contains*
search. Here is an example:

.. code-block:: console

   GET https://api.bluebuttonpro.com/DirectoryListings/search?displayName=health

   Authorization: Bearer <token>

This will indicate to the API that all directory listings that *contain* the name "health" should be
returned. The API will return a paged model. By default, each page of the result will contain 30 resources.
To return more pages, additional calls will need to be made to the search endpoint indicating which
page to return. Continuing our example, suppose that search matched 100 resources. This is how the response
will look like:

.. code-block:: json

   {
     "total": 100,
     "totalPages": 4,
     "hasNextPage": true,
     "currentPage": 1,
     "resources": [
       {
         "id": "f1513cd3-5d2f-4e75-a6f3-c2a50c59b483",
         "organizationId": "becbfcde-c730-4aaa-9c43-4b0d2e79caba",
         "displayName": "My Health",
         "active": true
       }
     ]
   }

This model shows us that there are a total of 100 matched resources, total number of pages is 4, indicates
that based on the current page, there are additional pages that can be acquired, and contains the current
page of matched resources.

To get a separate page of results, it can be included as a query parameter:

.. code-block:: console

   GET https://api.bluebuttonpro.com/DirectoryListings/search?displayName=health&page=3

   Authorization: Bearer <token>

This request indicates the API to return page 3 of the matched resources.

.. _directory-listing-finding-by-id:

Finding by id
^^^^^^^^^^^^^

A specific directory can be retrieved by id by creating the following request, assuming the id of the
directory listing is ``987e8b0f-ee50-4509-9e7a-9f16e16fb1af``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/DirectoryListings/987e8b0f-ee50-4509-9e7a-9f16e16fb1af

   Authorization: Bearer <token>

.. _directory-listing-finding-for-an-organization:

Finding for an organization
^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is also possible to pull all directory listings for a specific organization. The request can be constructed
like so, assuming the id of the organization is ``3fa85f64-5717-4562-b3fc-2c963f66afa6``:

.. code-block:: console

   GET https://api.bluebuttonpro.com/DirectoryListings/organization/3fa85f64-5717-4562-b3fc-2c963f66afa6

   Authorization: Bearer <token>

This will return an array of directory listings for that specified organization.

Activate/Deactivate listings
-----------------------------

When :ref:`searching for listings <directory-listing-searching-for-listings>`, only the active listings
are displayed. Users of an organization can deactivate a listing, which will remove the listing from
the search.

.. note::

   Deactivating listings doesn't affect the :ref:`find by id <directory-listing-finding-by-id>` and
   :ref:`find by organization <directory-listing-finding-for-an-organization>` endpoints. It only affects
   the search endpoint.

To activate or deactivate a listing, the following request can be made, assuming the listing id is ``ddb502c8-791c-4c9f-8ad8-7a9311ec447d``:

.. code-block:: console

   POST https://api.bluebuttonpro.com/DirectoryListings/ddb502c8-791c-4c9f-8ad8-7a9311ec447d/update-active-status?isActive=false

Only users that belong to the listing's organization can update the active status.

Deleting a listing
------------------

To delete a listing entirely, the following request can be made, assuming the listing id is ``ddb502c8-791c-4c9f-8ad8-7a9311ec447d``:

.. code-block:: console

   DELETE https://api.bluebuttonpro.com/DirectoryListings/ddb502c8-791c-4c9f-8ad8-7a9311ec447d

Only users that belong to the listing's organization can delete a listing.