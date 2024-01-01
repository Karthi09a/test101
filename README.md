# GP2GP England Mock Setup
## Overview
To do local GP2GP England development, follow the below steps to setup up the necessary configurations and the mock applications.

We need to setup and configure the below application to be able to run GP2GP England locally.

PDS Mock – to fetch the patient details

SDS Mock – to fetch the organisation details and its service details

Spine tool – used to run the GP2GP flow

Inbound MHS – to receive PDS General update success response

GP2GP MHS – to receive GP2GP messages like EHR Request, EHR Extract & Acknowledgement.

Before we setup the above application we have to configure EMIS Web to support GP2GP and other dependant services.

## EMIS Web – EMAS Configuration:
Before jumping in to the EMAS configuration, need to get the SpinePartyKey and ASID of the organisation from the DB by using below query.

Query:
![58a3aa15-df4a-4d71-953b-f7a4ce6e3aff](https://github.com/Karthi09a/test101/assets/132992711/0d1ab5dc-d130-4d2a-9eeb-881a3180fc77)



select * from EMISWebCR6.dbo.Organisation where cdb in (50002, 50003)

Spine Key Format: <{NationalPracticeCode}-{AnyValue}>

SpinePartyKey:

50002: Z00029-822050

50003: Z00157-822050

ASID:

50002: 123456789010
50003: 123456789011

Now after moving into the EMAS Manager page, we must activate SDS, PDS & GP2GP service one by one.
![d23c6668-e189-4bcd-8537-d90217449a4f](https://github.com/Karthi09a/test101/assets/132992711/f93d9c1b-0b1d-48ef-8f58-3db7aa2309ed)


SDS:
While activating that service, it will show a page to update the Spine Party Key & ASID for SDS.

Update the Spine Party Key with the value that are having NACS Code of the organisation. ASID could be anything.
![2896d3e5-66e0-4567-bd72-58aa845a7a0b](https://github.com/Karthi09a/test101/assets/132992711/d1619447-7e99-404f-8de8-96217a39235e)


PDS:
Activating PDS will not ask for anything.

GP2GP:
Activating GP2GP will show a page to update the ASID & Party Key. We can use below mentioned ASID, but for Party Key, we can use the above key that was given to activate SDS.

![2047b7d4-72e8-49e1-8c1b-aa4953e235f8](https://github.com/Karthi09a/test101/assets/132992711/2a19352b-4892-46ce-8cc5-cbf23462953c)

 

After configured in EMAS, please verify the values in the below table by using below query for both CDB 50002 & 50003.
```
select organisationguid, * 
from EMISIndex.ExternalMessaging.ServiceMessages s
where organisationguid in ('2B028154-A473-44F2-8425-EE2B7885EE2A', '318B3A00-58C9-4782-BF7B-BF4F5DDE60C7')
and servicename in ('urn:nhs:names:services:gp2gp', 'urn:nhs:names:services:pds', 'urn:nhs:names:services:pdsquery', 'spine:directoryservices')
order by s.organisationguid, ServiceName, SenderKey
```
![e211f52c-58fa-4f49-a168-071088cb3b90](https://github.com/Karthi09a/test101/assets/132992711/4766fcaf-da6b-48fe-a46c-f8514e0e0b27)

We can be able to see the SenderKey & Recipient Key that are using the Spine party key from Organisation table for the GP2GP service. Theses services are used in External Messaging service application.

This table also has an Endpoint URL and certificate values. Theses values need to be updated after completing the entire setup.

# PDS Mock

Use below zip file to deploy it in the IIS manager.


After deploying the application, the URL should be updated in the table.

Note: 

this is the URL of the application - http://localhost/PDS_Service.

The PDS service is deployed under below URL. so we have to append the resource path with the above base URL.

http://localhost/PDS_Service/syncservice-pds/pds

Paste PDS file for the patient:
There is already the PDS files are placed under App_Data folder for 2 different CDB. Here please rename the folders with your configured CDB values.


The Xml file of the PDS information should be paste to theses folders. The file name should be the NHS number.


In this case, 50002 & 50003 CDB are used for GP2GP. So these 2 folders are created with the patient information.

Note: The PDS information can be fetched from already processed GP2GP flow in beast machine.

 

Inside the XML file, the NACS code should be the sending organisation value. In our case, 50003 is the Sending organisation, so that value should be Z00157. It has to be updated in XML file that are placed under 50002.

 


 

The same process should be followed for 50003 folder. Z00029 (50002 Nacs code) should be updated in the XML file that are placed under 50003 location.


 


Finally, the configuration file should be updated with ASID against the CDB key like below.


the key value should be started with Org followed by CDB number like mentioned in the above image file.

the value should be the ASID of the CDB number. ASID can be fetched from Organisation table from CR database.






