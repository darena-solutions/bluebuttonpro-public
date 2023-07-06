FHIR Tools
===============

We provide a suite of tools that will be useful when developing your SMART-on-FHIR application. Most of these tools can be accessed without signing up for the MeldRx DevSphere.

********************
FHIR API Providers
********************

A `FHIR API Provider` is any organization that provides a FHIR API. For example, many EHRs and payers are FHIR API Providers. We have curated a list of all registered FHIR API Providers that can be searched on our `Tools > FHIR API Providers` page.

We provide additional documentation, a discussion forum, and other resources for every FHIR API Provider.

====================================
Register with a FHIR API Provider
====================================

If you'd like to create an app that interacts with a FHIR API Provider, you will want to register your app on their platform before linking it via MeldRx.

When you select a FHIR API Provider, you will be brought to details about that provider, including a link to their app registration portal. Go to their portal to register your application, making note of the credentials that are created for you. In some cases, we provide guides (`TODO: Add a link`) to help you register your app with these provides.

> TODO: Include an image

====================================
Testing Your App
====================================

If a FHIR API Provider provides a public sandbox environment for testing, you can test your application from the MeldRx DevSphere. You must have first registered your app with the FHIR API Provider.

.. note::
    If you have registered a Patient or Provider application, please make sure to set one of your redirect URLs to the URL specified on the FHIR API Providers details page (it should look like `https://...meldrx.com/sof-callback`)

Testing your application with this tool will tell you if your credentials are active and functioning properly. This can be useful if you are having problems in your app, as this will eliminate the possibility that your app is not registered properly.

To test your application:

1. Find the FHIR API Provider in our list of FHIR API Providers
2. Select the sandbox environment that you want to connect to (some FAPs have more than one sandbox)
3. Enter your application credentials and make any necessary adjustments
4. Click `Generate Url`. This will generate a launch URL.
5. Click `Connect` to launch the URL and connect your app to the selected sandbox environment.

> TODO: Instead of "Generate URL" and "Connect", can we just make this one step? Like a button called "Launch". If we want the user to be able to see the URL, then we can display it on the screen at the same time we are launching it (in a new tab)

If everything worked properly, you will be brought to a diagnostic application providing status information about your application.

If there is an error, you may have an issue with the app that you registered with the FHIR API Provider.

====================================
FHIR API Provider Not Listed
====================================

If you cannot find a FHIR API Provider in our list of registered providers, you can still test your application by navigating to `Tools > FHIR API Tester`. From here, enter the URL of the FHIR server that you want to test.

The rest of the steps are identical to testing from a known FHIR API Provider.

====================================
Discussion
====================================

We provide a discussion forum for all FHIR API Providers. Use this forum to communicate with other developers, ask questions, and get support for working with that provider. A link to the discussion forum can be found on the FHIR API Provider details page.

