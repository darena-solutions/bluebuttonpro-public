# Data Vault

Every instance of MeldRx includes a *Data Vault* which is simply a repository for storing data.
The types of data that MeldRx can store can be categorized into three groups:
1. Structured Data
2. FHIR Data
3. Binary Data

Data in the *MeldRx Data Vault* is accessed through a set of APIs which provide complete control over how this data is created, read, and deleted.

## Types of Data

### Structured Data

*Structured Data* refers to data that is organized and formatted in a consistent and predefined manner, making it easily searchable, analyzable, and interpretable by both humans and machines.
MeldRx stores structured data in [JSON](https://en.wikipedia.org/wiki/JSON) format.
MeldRx can store any type of JSON-based structured data.

Structured data is accessed via the *MeldRx Non-FHIR Data API*.

### FHIR Data

*FHIR Data* is a specific type of *structured data* that MeldRx is capable of storing in the data vault.

FHIR data is accessed via the *MeldRx FHIR API*.

### Binary Data

*Binary Data* refers to files like PDFs, images, videos, etc.
This type of data is not bound by any specific format or structure, allowing it to accommodate various file types and sizes.
MeldRx provides a flexible and efficient solution for managing binary data files.

Binary data is accessed via the *MeldRx Documents API*.

## Data Importers

MeldRx includes a set of APIs that allows it to ingest data in various formats and store it within MeldRx. For example, CCDA documents can be imported and filed into the MeldRx platform.