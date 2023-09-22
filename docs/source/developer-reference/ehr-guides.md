# EHR Guides

When registering an application with an EHR, you will need to specify a redirect URL if you choose a patient-facing or clinician-facing app. In order to make your app compatible with MeldRx, you must set one of your redirect URLs to `https://app.meldrx.com/sof-callback`.

___
## SMART-Health-IT

SMART-Health-IT provides a FHIR server that is excellent to use for testing and doesn't require any authentication.

### Sandbox Details
- FHIR Server: [https://launch.smarthealthit.org/v/r4/fhir](https://launch.smarthealthit.org/v/r4/fhir)

#### Sample Patients
- Gerardo Botello
    - First Name: `Gerardo`
    - Last Name: `Botello`
    - Date of Birth: `2010-09-07`
    - Gender: `M`
    - Postal Code: `02215`
___
## Epic

### Create An App with Epic
1. Create an account on [https://appmarket.epic.com](https://appmarket.epic.com)
2. Go to "Build Apps" and configure your app

#### Backend Apps
For backend apps, follow the instructions here to generate a key pair Or, run the following commands:
- `openssl genrsa -out /path_to_key/privatekey.pem 2048`
- `openssl req -new -x509 -key /path_to_key/privatekey.pem -out /path_to_key/publickey509.pem -subj '/CN=myapp'`

### Sandbox Details
- FHIR Server: [https://fhir.epic.com/interconnect-fhir-oauth/api/FHIR/R4](https://fhir.epic.com/interconnect-fhir-oauth/api/FHIR/R4)

#### Sample Patients
- Jason Argonaut
    - FHIR ID: `eD.LxhDyX35TntF77l7etUA3`
    - MRN: `203675`
    - First Name: `Jason`
    - Last Name: `Argonaut`
    - Date of Birth: `1985-08-01`
    - Gender: `M`
    - Home Phone: `608-555-5555`
- Jessica Argonaut
    - MRN: `E3977`
    - First Name: `Jessica`
    - Last Name: `Argonaut`
    - Date of Birth: `1985-08-01`
- Theodore MyChart
    - MRN: `202500`
    - First Name: `Theodore`
    - Last Name: `MyChart`
    - Date of Birth: `1948-07-07`

#### Sample Logins
- Patient Portal: `fhircamila` / `epicepic1`
- Patient Portal: `fhirjason` / `epicepic1`
- Patient Portal: `fhirderrick` / `epicepic1`
- Patient Portal: `fhirdaisy` / `epicepic1`
- Patient Portal: `fhirdesiree` / `epicepic1`
- Patient Portal: `fhirelijah` / `epicepic1`
- EHR: `FHIR` / `EpicFhir11!`

___
## Cerner

### Create an App with Cerner
1. Create an account on [https://code-console.cerner.com/](https://code-console.cerner.com/)
2. Click + New Application
3. Select if this is a Provider, Patient, or System (Backend) application
    - For patient/clinician apps, you need to specify a redirect URL.
4. Select the product family (for most cases, this will be Millennium)

### Sandbox Details
- FHIR Server: [https://fhir-myrecord.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d](https://fhir-myrecord.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d)

#### Sample Patients
- Johnny Abbot
    - FHIR ID: `12819390`
    - First Name: `Johnny`
    - Last Name: `Abbot`
- John2 Ace
    - FHIR ID: `12745871`
    - First Name: `John2`
    - Last Name: `Ace`

#### Sample Logins
- Patient Portal: `wilmasmart` / `Cerner01`
- Patient Portal: `joesmart1` / `Cerner01`
- Patient Portal: `nancysmart` / `Cerner01`
- Patient Portal: `valeriesmart1` / `Cerner01`
- Patient Portal: `fredricksmart` / `Cerner01`
- Patient Portal: `timmysmart` / `Cerner01`
- Patient Portal: `haileysmart` / `Cerner01`
- Patient Portal: `timApeters` / `Cerner01`


___
## AthenaHealth

### Create an App with AthenaHealth
1. Create an account on [https://developer.api.athena.io/ams-portal/apps](https://developer.api.athena.io/ams-portal/apps)

### Sandbox Details
- FHIR Server:

___
## NextGen

### Create an App with NextGen
1. Create an account on [https://developer.nextgen.com/](https://developer.nextgen.com/)

### Sandbox Details
- FHIR Server:
    - [https://www.nextgen.com/patient-access-api](https://www.nextgen.com/patient-access-api)