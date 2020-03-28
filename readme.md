# O-Prescription

## Overview
This is the odorless prescription project. This project contains an application, which allows the save, privacy centric process of prescribing medications. It depends on an PKI for **health care providers**.

## Requirements
- A Prescription must be protected. No unauthorized access should be allowed.
- A prescription should readable by an arbitrary number health care providers, only if the patient allows the access.
- A prescription should be dispensable only once.
- It must be straight forward in use:
  - On patient site
  - On doctors site
  - On pharmacists site
- Technical avoiding the brokering of prescriptions.
- Should work easily also with paper an patient site.

## Basic Idea
The **patient** will receive two tokens, together with an encrypted document.
1. `p-id` This gives access to the key for decrypt the prescription.
2. `d-id` This gives access to the id which is reserved for the dispense.
If the patient don't have an technical devide or the technical infrastructure, the prescription might be provided on a public server. In this case the patient receive the URI of this document on the public server.

To decrypt the prescription, the user will get a key `p-id`, which he can provide to **health care providers**. This `p-id` will give access to an unique document key. This is stored in the document key store service *DKSS*. Only health care providers will get access to this service.

In order to dispense the prescription, the **patient** may provide the `d-id` to a **pharmacist**. This id entitles the **pharmacist** to dispense the prescription. The `d-id` can only be used once to store the dispense information in an central dispense service.

The prescription must be signed by an **doctor**. This signature must be checked by the pharmacist **health care provider** before he 
dispense the prescription. 

The *pharmacist* will create a dispense document. This document will contain the encrypted prescription, the key to decrypt the 
prescription and the dispense record. This document will signed by the **pharmacist** and will be encrypted with the public key of 
a *clearing or factoring authority*.

The *clearing authority* will check, if the dispense was conducted according the rules.

## Roles
### Patient
Receives the encrypted document and the ids for decrypting and dispensing the prescription.

### Health Care Providers
Have read access to the document key store service.

### Doctor
Creates the prescription. He signes the prescription with his private key. 

### Pharmacist
Can provide an offer and creates the dispense document, which is signed by him and encrypted for the clearing authority. He is a **health care provider** and has access to the document key store service, the dispens key service and the dispense service.

### Clearing Authority
Checks, if the dispensing is following the rules. They will receive the dispense document and will have access to the dispense service, where they can check, if the dispense was booked accordingly.

## System components
### Doctor Public Key Service (*DPKS*)
Publish the public key of all doctors. This is the basic for checking, if a prescription is valid.

### Document key store service (*DKSS*)
This service provides access for *health care providers* to the document keys. Every key, stored in this store can only be used
for one document.  

### Document key service (*DKS*)
This service creates unique keys, which will be used for exactly one prescription document. The access to this service is restricted
to *health care providers*.

### Dispense key service (*DPKSS*)
This service provides access to the key, which allows to dispense an prescription.

### Dispense service (*DPS*)
This service allows to dispense an prescription only once. This dispense service allows to dispense a particular prescription, if the reservice
d-id wasn't consumed before. 
While it is possible to store in this service also the prescription and dispense record for further factoring, this will set a high risk on this service.

# Dialectic 
## Disadvantages of this solution
- The different ids may require some training on the **patient** and **health care provider** site.
- It depends on one or more PKI, which validates the roles of **health care providers**, **pharmacists** and **clearing authorities**.

## Advantages of this solution
- The prescription document will be never stored centrally. A leak will never give access to all documents, as the documents are distributed by the **patients** individually.
- The prescription document is not readable by anybody but **health care providers**, because only they can access the document key in the *document key store service*.
- It is completely in the hand of the patient to decide who can make an offer and who can dispense.
- The leak of the ids, which are in the hands of an **patient**, endanger maximum one prescription for this particular **patient**. This will be contained by the fact that only **heath care providers** can access the *document key store service*.

## Possible Enhancements 
- The prescription can made available inside the electroical health record of the patient, if the provider of this record gets access to the *documnet key store service*.