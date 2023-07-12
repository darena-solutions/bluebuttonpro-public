# MeldRx API

## Header Values

In many of the API requests, a header value is typically required to indicate which FHIR server the
request must operate under. There are two ways to provide this value.

**FhirServerId-Context**:
If the id of the FHIR server is known, then this header value can be used. Simply add the this headerwith the id of the FHIR server in the request. EG:

```console
GET/POST/PUT/PATCH/DELETE: <api url>

Content-Type: <content type>
FhirServerId-Context: <FHIR server id>
```

**Tin-Context**
If the id of the FHIR server is not known and the request must operate under an organization's FHIR server, then the tax identification number (TIN) of the organization can be provided. The API will find the FHIR server that is accessible by this organization and will set that FHIR server as the server to operate under.

```{admonition} Note
The organization must have access to only one FHIR server to use this header value. If more than
      one FHIR server is accessible by this organization, then `FhirServerId-Context` must be used.
```

The request can look like this:

```console
GET/POST/PUT/PATCH/DELETE: <api url>

Content-Type: <content type>
Tin-Context: <organization tin>
```