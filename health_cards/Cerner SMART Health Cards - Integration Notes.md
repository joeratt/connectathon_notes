# Cerner COVID-19 SMART Card Connectathon Guide

This is the guide for integrating with Cerner's COVID-19 SMART Health Cards implementation. 

**Table of Contents:**
* [Integration Notes](#Integration-Notes)
* [Health Card Endpoints](#Health-Card-Endpoints)
* [Example Call](#Example-Call)
* [Quick-Start Guide](#Quick-Start-Guide)
* [Additional Utility Endpoints](#additional-utility-endpoints)
* [Test Patient Information](#test-patient-information)
* [Further Links / References](#further-links--references)

## Integration Notes

* Cerner's implementation only supports the FHIR-based operation: https://smarthealth.cards/#via-fhir-health-cards-issue-operation 
* Cerner's implementation only supports Immunizations. Lab Results are not yet supported.
* Cerner's implementation does not yet support the optional claims (like `_since` and `includeIdentityClaim`)

## Health Card Endpoints

* Open Endpoint (no Authorization required): https://fhir-open.stagingcerner.com/beta/ec2458f2-1e24-41c8-b71b-0e701af7583d/Patient/{id}/$health-cards-issue
* Closed Endpoint: https://fhir-myrecord.stagingcerner.com/beta/ec2458f2-1e24-41c8-b71b-0e701af7583d/Patient/{id}/$health-cards-issue
  * Note - only `Patient` access is supported per the spec

## Example Call

### Required Request Headers

`Accept: application/fhir+json`

`Content-Type: application/fhir+json`

### Example Request
POST https://fhir-myrecord.stagingcerner.com/beta/ec2458f2-1e24-41c8-b71b-0e701af7583d/Patient/12724065/$health-cards-issue

**POST Body**
```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "credentialType",
      "valueUri": "https://smarthealth.cards#immunization"
    },
    {
      "name": "credentialType",
      "valueUri": "https://smarthealth.cards#covid19"
    }
  ]
}
```

### Example Response:
```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "verifiableCredential",
            "valueString": "eyJhbGciOiJFUzI1NiIsInppcCI6IkRFRiIsImtpZCI6IkJlbXNQUm83dTBrTFd4ZERKSzk2cmx4ZHFhOVpicjJsSXhzZmU3VGlFMTAifQ.fZLLbtswEEV_JWC3elGyY1u7NN0EqIuicdpF4QU1Glks-BBISqgb6N87lJ2iBdIA3JAcnrn3cp6Z9J7VrA9h8HWed710qT47BOvazAdxkuYE6Ay6DKzOGwwiRyhX621XphzLVbrisE2bDW_SAjcFF91mva1aljDTdKzmtyWvqt1tuUnYBKx-ZuE8IKu__2nptXChR6FCn4FwrX932aRxQ5j_10mtRyN_iSCtebMQ7CRbvmPHhIHDFk2QQj2OzQ-EECVF11_R-cip2SorsoJ48fT9aFqFscaht6MDPCzy2fUiudphYJUi2kUJNXBn8kjkUaknp6jg5X0d0S-bV8CfyQ69j_kJjReI0FIRjz3u774c6OYkJzQxw28PH_d37DiTsUaS6Q8iRAbf7Yq02KV8zeY5eVUFf1vFw7_R0iCE0S829aAwYPyYSQBIg_e2XQhgW5qVRbA_-4D6Olb0I73aZNadlunKvWxzmH4SAJaXrCy2bD7OCRuu1hc5HTo0UdvfyVGRBRjdchXNHqS-IEqeFuu0WBFW2fBp1A26mERZlRWdDeg663Q8I30CgnWxTSv9oESMdk9a1M29kkbCzZNuHColKL3jsub5Nw.NRDfsvGyy-2Q3DbZt_wLW_yUYsL0P5OBLnjmRV8DacEYTvKX4_YJgrWUT5qMh_WpnZE8nKc7dTjNENGimtyvVg"
        },
        {
            "name": "resourceLink",
            "part": [
                {
                    "name": "vcIndex",
                    "valueInteger": 0
                },
                {
                    "name": "bundledResource",
                    "valueUri": "resource:0"
                },
                {
                    "name": "hostedResource",
                    "valueUri": "https://fhir-myrecord.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d/Patient/12724065"
                }
            ]
        },
        {
            "name": "resourceLink",
            "part": [
                {
                    "name": "vcIndex",
                    "valueInteger": 0
                },
                {
                    "name": "bundledResource",
                    "valueUri": "resource:1"
                },
                {
                    "name": "hostedResource",
                    "valueUri": "https://fhir-myrecord.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d/Immunization/M197477278"
                }
            ]
        }
    ]
}
```

## Quick-Start Guide

This is just the steps you'll need to take to integrate with Cerner's Health Cards Functionality

### Read Cerner's Documentation

Our FHIR Integration documentation can be found on our FHIR landing page: https://fhir.cerner.com
* Further information for app developers can be found on our CODE landing page: https://code.cerner.com

### Create a Developer Account and Create your app

Use our CODE Developer Portal to register and define your application: https://code.cerner.com/developer/smart-on-fhir/apps 

#### Required Configuration for SMART Health Cards integration

* **App Type**: Patient
* **FHIR Spec**: r4
* **Scopes**:
  * patient/Patient.read
  * patient/Immunization.read

### Authorize your App

You can then request authorization tokens by following our Authorization guide:  http://fhir.cerner.com/authorization/#requesting-authorization-on-behalf-of-a-user
* Your registered application will automatically generate your Client ID. You can find this in the [Code Developer Portal](https://code.cerner.com/developer/smart-on-fhir/apps)
* Authorizing for a Patient token will redirect you to our patient login portal. You can use any of our test patient credentials: https://docs.google.com/document/d/10RnVyF1etl_17pyCyK96tyhUWRbrTyEcqpwzW-Z-Ybs
* **Note**: the audience (`aud` Authorize parameter) to use for the Connectathon is `https%3A%2F%2Ffhir-myrecord.cerner.com%2Fr4%2Fec2458f2-1e24-41c8-b71b-0e701af7583d`

### Issue a HealthCard

Using the closed [Endpoint](#Endpoints) listed above, you can now make your call to retrieve a Health Card. The [Example Call](#Example%20Call) is provided for ease-of-use.


## Additional Utility Endpoints

These utilities are __not__ production code. These are meant to help with development and will not be released in our production deployment.

### Decode/Decompress and verify JWS signature
Uses the iss in the jws payload to retrieve the public jwks for the issuer to use while validating the signature.

**Endpoint:**

POST https://fhir-open.stagingcerner.com/beta/admin/health-cards/decode 

**Request Parameters:**

* `jws`: The JWS token to decode
*	`verify_signature`: Whether public jwks retrieval and signature verification should be performed

#### Example:
Request:
POST https://fhir-open.stagingcerner.com/beta/admin/health-cards/decode

**Request Body:**
```json
{
    "jws": "eyJhbGciOiJFUzI1NiIsInppcCI6IkRFRiIsImtpZCI6IkJlbXNQUm83dTBrTFd4ZERKSzk2cmx4ZHFhOVpicjJsSXhzZmU3VGlFMTAifQ.fZLLbtswEEV_JWC3elGyY1u7NN0EqIuicdpF4QU1Glks-BBISqgb6N87lJ2iBdIA3JAcnrn3cp6Z9J7VrA9h8HWed710qT47BOvazAdxkuYE6Ay6DKzOGwwiRyhX621XphzLVbrisE2bDW_SAjcFF91mva1aljDTdKzmtyWvqt1tuUnYBKx-ZuE8IKu__2nptXChR6FCn4FwrX932aRxQ5j_10mtRyN_iSCtebMQ7CRbvmPHhIHDFk2QQj2OzQ-EECVF11_R-cip2SorsoJ48fT9aFqFscaht6MDPCzy2fUiudphYJUi2kUJNXBn8kjkUaknp6jg5X0d0S-bV8CfyQ69j_kJjReI0FIRjz3u774c6OYkJzQxw28PH_d37DiTsUaS6Q8iRAbf7Yq02KV8zeY5eVUFf1vFw7_R0iCE0S829aAwYPyYSQBIg_e2XQhgW5qVRbA_-4D6Olb0I73aZNadlunKvWxzmH4SAJaXrCy2bD7OCRuu1hc5HTo0UdvfyVGRBRjdchXNHqS-IEqeFuu0WBFW2fBp1A26mERZlRWdDeg663Q8I30CgnWxTSv9oESMdk9a1M29kkbCzZNuHColKL3jsub5Nw.NRDfsvGyy-2Q3DbZt_wLW_yUYsL0P5OBLnjmRV8DacEYTvKX4_YJgrWUT5qMh_WpnZE8nKc7dTjNENGimtyvVg",
    "verify_signature": true
}
```

**Example Response:**
```json
{
    "iss": "https://fhir-myrecord.stagingcerner.com/beta/ec2458f2-1e24-41c8-b71b-0e701af7583d",
    "nbf": 1621339627,
    "vc": {
        "type": [
            "https://smarthealth.cards#health-card",
            "https://smarthealth.cards#immunization",
            "https://smarthealth.cards#covid19"
        ],
        "credentialSubject": {
            "fhirVersion": "4.0.0",
            "fhirBundle": {
                "resourceType": "Bundle",
                "type": "collection",
                "entry": [
                    {
                        "fullUrl": "resource:0",
                        "resource": {
                            "resourceType": "Patient",
                            "name": [
                                {
                                    "family": "SMART",
                                    "given": [
                                        "WILMA"
                                    ]
                                }
                            ],
                            "birthDate": "1990-09-15"
                        }
                    },
                    {
                        "fullUrl": "resource:1",
                        "resource": {
                            "resourceType": "Immunization",
                            "status": "completed",
                            "vaccineCode": {
                                "coding": [
                                    {
                                        "system": "http://hl7.org/fhir/sid/cvx",
                                        "code": "208"
                                    }
                                ]
                            },
                            "patient": {
                                "reference": "resource:0"
                            },
                            "occurrenceDateTime": "2021-05-04",
                            "lotNumber": "12323",
                            "performer": [
                                {
                                    "actor": {
                                        "display": "Model Clinic Umbrella"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        }
    }
}
```
 
## Test Patient Information
### Gilbert, Alice S
* Patient ID: 12742542
* Date of Birth: Nov 16, 1970
* MRN: 9002472173
* Administrative Gender: Female
* Inpatient FIN: 16048
* Inpatient Location: Model Hospital, MX Hospital, ICU, 101, A
* Patient Access Login
  * Username: alicegilbert1
  * Password: Cerner01
* Observations
  * COVID-19 Test - Positive
* Immunizations
  * COVID-19 Vaccination - 2 dose; Moderna
  * influenza
  * measles
  * influenza virus vaccine, H5N1, inactive
  * diphtheria toxoid

### Triplett, Kristen B
* Patient ID: 12742544
* Date of Birth: Nov 15, 1980
* MRN: 9004575813
* Administrative Gender: Female
* Inpatient FIN: 16049
* Inpatient Location: Model Hospital, MX Hospital, ICU
* Patient Access Login
  * Username: ktriplett1
  * Password: Cerner01
* Observations
  * COVID-19 Test - Positive
* Immunizations
  * COVID-19 Vaccination - 2 dose; Pfizer
  * tetanus toxoid
  * measles/mumps/rubella
  * influenza

### SMART, Fredrick
* Patient ID: 12724070
* Date of Birth: August 22, 1946
* MRN: 6935
* Administrative Gender: Male
* Inpatient FIN: 16004
* Inpatient Location: Model Hospital, MX Hospital, NU04, 101, A
* Outpatient FIN: 15962
* Outpatient Location: Model Clinic 1, MX Clinic 1, MX Clinic 1
* Patient Access Login
  * Username: fredricksmart
  * Password: Cerner01
* Immunizations
  * COVID-19 Vaccination - None
  * tetanus toxoid


## Further Links / References
* SMART Health Cards: https://smarthealth.cards/
* Vaccination & Testing Implementation Guide: http://build.fhir.org/ig/dvci/vaccine-credential-ig/branches/main/
