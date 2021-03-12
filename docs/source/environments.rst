Environments
============

There are two environments that developers will interact with. The sandbox and production environments.
For the sandbox environment, all API calls will be suffixed with ``-sandbox``. As an example, to find
a patient by an ID in the sandbox environment, the endpoint will look like this:

.. code-block:: console

   GET https://api-sandbox.bluebuttonpro.com/good-health/Patient/123456

   Authorization: Bearer <token>

When working in the production environment, there will be no suffixes. Taking the above example, to
find a patient by an ID in the production environment, the endpoint will look like this:

.. code-block:: console

   GET https://api.bluebuttonpro.com/good-health/Patient/123456

   Authorization: Bearer <token>

The sandbox environment exists so that developers can test their applications on the BlueButtonPRO platform.
No real data should be entered into this environment. The sandbox environment is also updated more frequently
than the production environment. It is possible that some features exist in the sandbox environment
but have not met our quality assurance guidelines to move on to the production environment just yet.