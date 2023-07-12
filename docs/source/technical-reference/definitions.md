# Definitions

(definitions-pfr)=

PFR - Personal FHIR Record
   A personal FHIR record is a FHIR server that has been provisioned for a person. This is different from
   an organizational FHIR server in that this FHIR server is intended to be a private server to only be
   used by a particular user. It is still possible to share this type of FHIR server. The server is tied
   to a [person](#person), instead of a single user entity. However, only one user can own a person.
   The user can then create additional [grant](#grants) to that person, allowing other users or organizations
   to access this FHIR server directly. Additionally, only one FHIR patient resource can exist in this
   FHIR server at any time.