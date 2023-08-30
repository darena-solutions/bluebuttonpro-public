# EHR Guides

## Epic

### Create An App with Epic
1. Create an account on [https://appmarket.epic.com](https://appmarket.epic.com)
2. Go to "Build Apps" and configure your app

#### Backend Apps
For backend apps, follow the instructions here to generate a key pair Or, run the following commands:
- `openssl genrsa -out /path_to_key/privatekey.pem 2048`
- `openssl req -new -x509 -key /path_to_key/privatekey.pem -out /path_to_key/publickey509.pem -subj '/CN=myapp'`

#### Patient/Clinician Apps
For patient/clinician apps, you need to specify a redirect URL.

### Tips
- It can take up to 24 hours for your Client ID to activate
- It's recommended to make an app specifically for testing in addition to an app you'll use for production

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


## AthenaHealth

### Create an App with AthenaHealth
1. Create an account on [https://developer.api.athena.io/ams-portal/apps](https://developer.api.athena.io/ams-portal/apps)



## NextGen

### Create an App with NextGen
1. Create an account on [https://developer.nextgen.com/](https://developer.nextgen.com/)


https://www.nextgen.com/patient-access-api
(Includes Sandbox URL)