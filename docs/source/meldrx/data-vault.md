# Data Vault

Every instance of MeldRx includes a _data vault_ which is simply a repository for storing data.
The types of data that MeldRx can store can be categorized into three groups:
1. Structured Data
2. FHIR Data
3. Binary Data

Data in the _MeldRx Data Vault_ is accessed through a set of APIs which provide complete control over how this data is created, read, and deleted.

## Types of Data

### Structured Data

_Structured Data_ refers to data that is organized and formatted in a consistent and predefined manner, making it easily searchable, analyzable, and interpretable by both humans and machines.
MeldRx stores structured data in [JSON](https://en.wikipedia.org/wiki/JSON) format.
MeldRx can store any type of JSON-based structured data.

Structured data is accessed via the _MeldRx Non-FHIR Data API_.

### FHIR Data

_FHIR data_ is a specific type of _structured data_ that MeldRx is capable of storing in the data vault.

FHIR data is accessed via the _MeldRx FHIR API_.

### Binary Data

_Binary Data_ refers to files like PDFs, images, videos, etc.
This type of data is not bound by any specific format or structure, allowing it to accommodate various file types and sizes.
MeldRx provides a flexible and efficient solution for managing binary data files.

Binary data is accessed via the _MeldRx Documents API_.

## Data Importers

MeldRx includes a set of APIs that allows it to ingest data in various formats and store it within MeldRx. For example, CCDA documents can be imported and filed into the MeldRx platform.