# Cerner COVID-19 SMART Card Connectathon Guide

This is the guide for integrating with Cerner's COVID-19 SMART Health Cards implementation. 

**Table of Contents:**
* [Integration Notes](#Integration-Notes)
* [Health Card Endpoints](#Health-Card-Endpoints)
* [Example Call](#Example-Call)
* [Quick-Start Guide](#Quick-Start-Guide)
* [Additional Utility Endpoints](#additional-utility-endpoints)
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
      "valueUri": "https://smarthealth.cards#covid19"
    },
    {
      "name": "credentialType",
      "valueUri": "https://smarthealth.cards#immunization"
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
            "valueString": "eyJhbGciOiJFUzI1NiIsInppcCI6IkRFRiIsImtpZCI6IlhVTVR0Q2pfYUlFejdWaFo0Y05YLVdKd0V1ckxBWnQ0SjRPa2dvNEc5ZFkifQ.3VJNc9MwEP0rjLj6S24aJzkxlEM5FJhpCwemB1laJwJ9eCQ5aej4v7PrJKWH0ANHPL5I-_Te27f7xHSMbMU2KfVxVZbdRofc7gNIH1QRk1hrt5YQHIRCelu2kEQJsp5dLro651DP8hmXi7xteJtX0FRcdM3l4kKxjGmR2IrP-YI3y-VynrGtZKsn9k56l-ARa9-fZXe7XbG7KHxYl3XFF6UMoMAlLUwst5w9ZCzte6AXXyHoTovWwNUzBrVORNGKkDYgTNoUUgQV3x4OOR1exUm_1YovX8Voawenf4mkvSNTf2zeDu0PkIn6owjRZSTMis2KqqiQlG7fD04ZIEyA6Icg4W7qih0Lpy6Z9MYgGzFkDAXCHltH5sGY-2AQcHq_IurT4QzxF7SK7xHkhIUDibDaIB-7_nbj5c_PjnTXeguO4r0SwfiIN7ebvXDY5IhtrsEpCPikAysmn63GZD6IRBp82VR5xfOqxoJQCi3ESan3uD_myitCzWe8WiBA-uHQD7u_ZePDOGZn--Kv9_Xx5SAyhjppiFNwtjeQgEa9FVJqBwf9JywpXOXJWNzHBPa49TjojWmm1aMZlVGrUm4fJ6uT87pqyGjG-mOYk50OAjjy9nIWCPJSDmEqUTx32h4oason55SA8enTYNsp0Io-yq2H0Plg6RINCpl8IB2lY28EpXWDZsybax97jaGy8e_R1f9hdDyvz0ZX8X_NDv9x_A0.Swhe0EFn3flfcT1hg0_ZyWTPdANux9fpEZvKJpyeZFLqzlWQaqBns9ejc3Wdu9YbfeHpO1uj_6g2Ikn_lQA4HQ"
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
POST https://fhir-myrecord.stagingcerner.com/beta/admin/health-cards/decode

**Request Body:**
```json
{
    "jws": "eyJhbGciOiJFUzI1NiIsInppcCI6IkRFRiIsImtpZCI6IlhVTVR0Q2pfYUlFejdWaFo0Y05YLVdKd0V1ckxBWnQ0SjRPa2dvNEc5ZFkifQ.3VI9c9QwEP0rjGj9IfkuseOK4ShCEWAmIRRMCllenwX68EiyL0fG_52V7y6kCFdQ4nGj1du37z3tE5Hek5r0IQy-zvOuly7VewfCujbzgW-l2QpwBlwmrM4bCDwHUawvqq5IGRTrdM1ElTYla1IKJWW8Ky-qVUsSYpqO1OySVUVZVqvLhEyC1E_knbAmwGMg9ffnsbvdLtutMuu2eUFZlQsHLZggufL5xMhDQsJ-gNhxD052kjcKNs8YnHUi8pq70ANXoc8Ed61_ezik8XAWJ-wkW3Z1FiO1Ho38xYO0Jor6I_N2bH6ACNFfjBBV-oipyTqjGUXSWH0_mlZBxDjwdnQC7hZX5HhxckmEVQrZIkNCcIDbo3VkHpX66hQCTv11pD4dXiH-glKxPz4G13Ag4Voq5CPX326s-PnZxLlbOYGJ8W64U9Zj5bbfc4MmZ7TZSMzhAw-RkV2VNKUspQWZ5-RVTey8po8vQ0wI7lgY_WJaDwoCxGeauBDSwMa2C4OwLa7hIt_vfQB93Fh8pF6Vy9rEfHMv21xMj0gglk5S0JLMD3NChmMQi5wOHJio7WWOCLJCjG65imbvpD5QFNFtyiqkVTZ8GnUDDi9o_AosDuA663QsokAugnVxTiv9oHhM-gbFqDfX1g8y4LbOqOgv0RX_YXQsLV6NjrJ_zQ7_ef4N.0r_Q1FGp9ouxT88r-ole37FSuP10XX1MfmHMt1JirI3QqryWBhJy5dfri7L2WYqnEPE32fNQ-KsSQbqTkTrRJg",
    "verify_signature": true
}
```

**Example Response:**
```json
{
    "iss": "https://fhir-myrecord.stagingcerner.com/beta/ec2458f2-1e24-41c8-b71b-0e701af7583d",
    "nbf": 1618277836,
    "vc": {
        "@context": [
            "https://www.w3.org/2018/credentials/v1"
        ],
        "type": [
            "VerifiableCredential",
            "https://smarthealth.cards#health-card",
            "https://smarthealth.cards#covid19",
            "https://smarthealth.cards#immunization"
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
                                    "family": "HWMockOne",
                                    "given": [
                                        "Carlos",
                                        "Shyann"
                                    ]
                                }
                            ],
                            "birthDate": "1970-01-02"
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
                                        "code": "207"
                                    }
                                ]
                            },
                            "patient": {
                                "reference": "resource:0"
                            },
                            "occurrenceDateTime": "2021-02-18",
                            "lotNumber": "000002",
                            "performer": [
                                {
                                    "actor": {
                                        "display": "Model Hospital"
                                    }
                                }
                            ]
                        }
                    },
                    {
                        "fullUrl": "resource:2",
                        "resource": {
                            "resourceType": "Immunization",
                            "status": "completed",
                            "vaccineCode": {
                                "coding": [
                                    {
                                        "system": "http://hl7.org/fhir/sid/cvx",
                                        "code": "207"
                                    }
                                ]
                            },
                            "patient": {
                                "reference": "resource:0"
                            },
                            "occurrenceDateTime": "2021-01-28",
                            "lotNumber": "0000001",
                            "performer": [
                                {
                                    "actor": {
                                        "display": "Model Hospital"
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
 

## Further Links / References
* SMART Health Cards: https://smarthealth.cards/
* Vaccination & Testing Implementation Guide: http://build.fhir.org/ig/dvci/vaccine-credential-ig/branches/main/
