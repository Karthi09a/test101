# GP2GP England Mock Setup

## Overview

To do local GP2GP England development, follow the below steps to setup up the necessary configurations and the mock applications.

We need to setup and configure the below application to be able to run GP2GP England locally.

1. PDS Mock – to fetch the patient details

1. SDS Mock – to fetch the organisation details and its service details

1. Spine tool – used to run the GP2GP flow

1. Inbound MHS – to receive PDS General update success response

1. GP2GP MHS – to receive GP2GP messages like EHR Request, EHR Extract & Acknowledgement.

Before we setup the above application we have to configure EMIS Web to support GP2GP and other dependant services.

### EMIS Web – EMAS Configuration:
Before jumping in to the EMAS configuration, need to get the SpinePartyKey and ASID of the organisation from the DB by using below query.

Query:
```sql
select * from EMISWebCR6.dbo.Organisation where cdb in (50002, 50003)
```

![58a3aa15-df4a-4d71-953b-f7a4ce6e3aff](https://github.com/Karthi09a/test101/assets/132992711/0d1ab5dc-d130-4d2a-9eeb-881a3180fc77)


**Spine Key Format**: <{NationalPracticeCode}-{AnyValue}>

**SpinePartyKey:**

50002: Z00029-822050

50003: Z00157-822050

**ASID:**

50002: 123456789010
50003: 123456789011

Now after moving into the EMAS Manager page, we must activate SDS, PDS & GP2GP service one by one.

![d23c6668-e189-4bcd-8537-d90217449a4f](https://github.com/Karthi09a/test101/assets/132992711/f93d9c1b-0b1d-48ef-8f58-3db7aa2309ed)


### SDS:

While activating that service, it will show a page to update the Spine Party Key & ASID for SDS.

Update the Spine Party Key with the value that are having NACS Code of the organisation. ASID could be anything.

![2896d3e5-66e0-4567-bd72-58aa845a7a0b](https://github.com/Karthi09a/test101/assets/132992711/d1619447-7e99-404f-8de8-96217a39235e)


### PDS:

Activating PDS will not ask for anything.

### GP2GP:

Activating GP2GP will show a page to update the ASID & Party Key. We can use below mentioned ASID, but for Party Key, we can use the above key that was given to activate SDS.

![2047b7d4-72e8-49e1-8c1b-aa4953e235f8](https://github.com/Karthi09a/test101/assets/132992711/2a19352b-4892-46ce-8cc5-cbf23462953c)

 

After configured in EMAS, please verify the values in the below table by using below query for both CDB 50002 & 50003.


```sql
select organisationguid, * 
from EMISIndex.ExternalMessaging.ServiceMessages s
where organisationguid in ('2B028154-A473-44F2-8425-EE2B7885EE2A', '318B3A00-58C9-4782-BF7B-BF4F5DDE60C7')
and servicename in ('urn:nhs:names:services:gp2gp', 'urn:nhs:names:services:pds', 'urn:nhs:names:services:pdsquery', 'spine:directoryservices')
order by s.organisationguid, ServiceName, SenderKey
```


![e211f52c-58fa-4f49-a168-071088cb3b90](https://github.com/Karthi09a/test101/assets/132992711/4766fcaf-da6b-48fe-a46c-f8514e0e0b27)


We can be able to see the SenderKey & Recipient Key that are using the Spine party key from Organisation table for the GP2GP service. Theses services are used in External Messaging service application.

This table also has an Endpoint URL and certificate values. Theses values need to be updated after completing the entire setup.

## PDS Mock

Use below zip file to deploy it in the IIS manager.

![65977ab4-e4e7-4b34-a053-ab46d51164f2](https://github.com/Karthi09a/test101/assets/132992711/319779a4-58fa-4e6d-ae0d-d226091e54b5)

After deploying the application, the URL should be updated in the table.

> [!NOTE]
> this is the URL of the application - * *http://localhost/PDS_Service* *.
> The PDS service is deployed under below URL. so we have to append the resource path with the above base URL.
> * *http://localhost/PDS_Service/syncservice-pds/pds* *

### Paste PDS file for the patient:
There is already the PDS files are placed under App_Data folder for 2 different CDB. Here please rename the folders with your configured CDB values.

![7a7da4e8-4598-412f-beed-9eec78131072](https://github.com/Karthi09a/test101/assets/132992711/d7ce866e-9ed9-4872-bf83-14ab4a721b10)

The Xml file of the PDS information should be paste to theses folders. The file name should be the NHS number.

![04af9605-3ec6-430e-b942-f3e5e5d05e09](https://github.com/Karthi09a/test101/assets/132992711/7015ed90-7521-4c03-bbc1-d35708c06d84)

In this case, 50002 & 50003 CDB are used for GP2GP. So these 2 folders are created with the patient information.

> [!NOTE]
> The PDS information can be fetched from already processed GP2GP flow in beast machine.

 

Inside the XML file, the NACS code should be the sending organisation value. In our case, 50003 is the Sending organisation, so that value should be Z00157. It has to be updated in XML file that are placed under 50002.

 ![51baabcd-e9a9-4b7a-8319-add97723cdd3](https://github.com/Karthi09a/test101/assets/132992711/074d769c-dd59-42a7-9348-76f499e51e68)

 

The same process should be followed for 50003 folder. Z00029 (50002 Nacs code) should be updated in the XML file that are placed under 50003 location.

![565791a9-8729-40ce-a668-a843a07ac3b1](https://github.com/Karthi09a/test101/assets/132992711/17b8e27c-914f-4f0a-a7ca-1c0642f15cc4)

![8e3b7154-7759-42a8-8e0d-b5e796e077c9](https://github.com/Karthi09a/test101/assets/132992711/4ce25006-e671-4618-9b35-b4be29181a89)



Finally, the configuration file should be updated with ASID against the CDB key like below.
![2458d3ca-2f51-469a-afcf-05e72de7706d](https://github.com/Karthi09a/test101/assets/132992711/b539ac5c-7163-4e7b-92d1-15459cf72541)


the key value should be started with Org followed by CDB number like mentioned in the above image file.

the value should be the ASID of the CDB number. ASID can be fetched from Organisation table from CR database.

## SDS Mock (NHS Spine directory service)

### Prerequisite
SDS mock was written in TypeScript and ldapjs package was used. So NodeJs should be installed in the machine.

### Setup
Extract the zip file and place it in the any of the folder.

simple-ldap-server-test.zip
05 Jan 2023, 03:11 PM
Before running the application, some of the configuration should be changed as per the local configuration.

After extracting, go to the etc folder. two files are available, one is having Organisation information, another one is having service information.


### Organisation Json
![91d71b2a-279e-490c-b9dd-0f4a63fd7b80](https://github.com/Karthi09a/test101/assets/132992711/5252bedd-6505-4f52-841f-7bade08eaf29)

Open the Organisation json file, modify the nhsIDCode. this value should the NACS code of the organisation.

the NACS code can be fetched from Organisation table. In this case, Nacs code of 50002 & 50003 are Z00029 & Z00157 respectively.
![1deb2568-6796-4479-91e2-325d958c919b](https://github.com/Karthi09a/test101/assets/132992711/a1a5f6ad-5e0f-4a8a-9814-ad00ae5b4998)


Address details can also be changed, based on your wish.

### Service Json
Below values should be updated to make SDS work based on the configuration in the EMIS web.

```nhsMHSPartyKey, nhsIDcode, nhsMhsCPAId, uniqueIdentifier```

```nhsMHSPartyKey:```

This key has 3 type of party key values. PDS, Requesting Party Key & Sending Party Key.

To update PDS Party Key (```"nhsMHSPartyKey": "YEA-0000806"```), replace the value by getting the value from  ‘AccreditedSystemIdentifier’ table in CR Database.

![b1fb60a7-0f62-43c6-9430-30df1cb867f1](https://github.com/Karthi09a/test101/assets/132992711/2229ea18-27b8-4dab-8f47-8aaf6fc1a801)


To Update requesting Org party Key (```"nhsMHSPartyKey": "Z00029-822051"```), replace the value by getting the value from ‘Spine.ServiceOrganisation’ table for requesting Org.

![375af5de-4b65-42d3-a256-9863df8a67bb](https://github.com/Karthi09a/test101/assets/132992711/d1b5ba66-0197-49dc-af1e-51f0827b38db)


To Update Sending Org Party Key (```"nhsMHSPartyKey": "Z00157-822051"```), replace the value by getting the value from ‘Spine.ServiceOrganisation’ table for sending Org. 

![00271352-7b3f-4a7e-9a29-d221294a2338](https://github.com/Karthi09a/test101/assets/132992711/53b0b1f4-7fea-4cdf-b2bc-d78a8484812b)


```nhsIDcode:```

Requesting Org Value - Z00029

Sending Org Value - Z00157

To replace this value, use your NACs code for both sending & requesting org.

```nhsMhsCPAId, uniqueIdentifier:```

In the parent section, I have given details to update the EMAS configuration in EMIS Web. There, while activating GP2GP, I have given the ASID & Party Key. We have to use that party key value to replace the existing value.

the value I have used is, ```200000000260``` for requesting org, ```200000000261``` for sending org.

replace this value with the value what you have used while activating the GP2GP in EMAS page.

### Run the application
after completing the configuration changes, its time to run the application. use the below command to run the application.

First open command prompt and navigate to the location, then run below command.

command: ```node lib\index.js```

![4eb77879-d114-4865-919e-734fa667bd2f](https://github.com/Karthi09a/test101/assets/132992711/c5f12663-3ae3-4608-9cd8-3fd291ae6a87)


the URL for this application is ldap://127.0.0.1:389 & ldaps://127.0.0.1:636. it will be updated in Connect & ExternalMessaging.ServiceMessages tables. that will be covered in later sections.

## Inbound Mhs & GP2GP Mhs

### Inbound Mhs

This service is used to handle the PDS General update success response. this project can be found in the ExternalMessaging solution.

Path: EMIS Web\OIS\ExternalMessaging\Sources\Emis.ExternalMessaging.InboundEbxmlListener

![ff09ab71-5295-43bf-832e-879348a7061e](https://github.com/Karthi09a/test101/assets/132992711/58b20f27-5ac2-41cb-b78e-5c782721cc9d)


 

Deploy this application in IIS Manager.

### GP2GP Mhs
This service is used to handle the GP2GP inbound messages like Ehr Request, Ehr Extract & Acknowledgement. This project can be found in Connect solution.

Path: Source\Listener\Emis.Connect.Listener.Ebxml

![c18caa61-bbda-4d69-804a-bd6a7ada12f0](https://github.com/Karthi09a/test101/assets/132992711/0afb29d3-057e-4f0c-9042-ab8470b62a84)


Deploy this application in IIS. 

 

Note: Deploy these applications, if it is not already deployed in IIS. Use its build location path as physical path in the IIS Manager, for both project. 

For Example, physical path for 

'Emis.Connect.Listener.Ebxml'

![ff0bb0e1-44ec-4a58-b10c-351fdfe7fb24](https://github.com/Karthi09a/test101/assets/132992711/e3b7f755-da4a-4946-a7d6-1397b7b6b39f)


Build location from my local path
'Emis.ExternalMessaging.InboundEbxmlListener'

![f35b6fef-5a63-4b8f-9834-5caba3dbfc5e](https://github.com/Karthi09a/test101/assets/132992711/dc58897b-6051-4f03-a946-c9fbbf2961e6)


## Spine TMS
(Adapted from NHS-D tool)

Copy & paste the below application.

SpineTMS.zip
05 Jan 2023, 06:01 PM
We should update the config file of this application to make GP2GP flow works.

In the previous section, we have deployed the Inbound.Mhs & GP2GP.Mhs service in IIS Manager from ExternalMessaging & Connect solution respectively. Copy that URL and paste it against “InboundMhs” & “Gp2GpMhs“ key.

![2c511e8c-64b2-4425-b1a0-b11ac9e2170f](https://github.com/Karthi09a/test101/assets/132992711/bc05cdae-e78e-4199-be32-b1d0e0e2144f)


Inside the extract folder, there is a certificate available under cert folder. Install the certificate.

Password: Emis123

![90376ff3-51d7-4ab2-b94e-35a8ccfee866](https://github.com/Karthi09a/test101/assets/132992711/2979559c-c5de-4e4e-a084-960e41fcad2a)


To run the application, double click ‘SpineStarter.exe’ file. it launches the application.

![350f060b-f235-4bc1-9265-fb8fa53ffda5](https://github.com/Karthi09a/test101/assets/132992711/500e5ee5-70b0-4a76-a331-de4fccf34979)


Ignore the warnings.



## DB & Additional Changes

### DB Change
So far, we have deployed the PDS, SDS & Spine TMS Mock. to make that application work for GP2GP flow, we have to update those URL & Certificate thumbprint in the connect & Index DB instead of the production values.

### External Messaging
We have to update the URL & certificate thumbprint in the ‘EMISIndex.ExternalMessaging.ServiceMessages’ table.

the URL & certificate thumbprint should be taken from PDS, SDS & Spine TMS mock.

In the below query, it is enough to replace the PDS Trace URL (2nd query). Find the URL from IIS manager against PDS service and replace with the value given here. Execute the script.

```sql
----SDS
update EMISIndex.ExternalMessaging.ServiceMessages
set Endpoint = 'ldap://127.0.0.1:636',
ClientCertificateThumbprint = '37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7'
where MessageName = 'SPINE_SDS'

--PDS Trace
update EMISIndex.ExternalMessaging.ServiceMessages
set Endpoint = 'http://localhost/PDS_Service/syncservice-pds/pds',
ClientCertificateThumbprint = '37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7'
where MessageName = 'QUPA_IN040000UK32'

--PDS General Update
update EMISIndex.ExternalMessaging.ServiceMessages
set Endpoint = 'http://localhost/PDS_Service/spine/rmessaging',
ClientCertificateThumbprint = '37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7'
where MessageName = 'PRPA_IN160000UK30'

--GP2GP
update EMISIndex.ExternalMessaging.ServiceMessages
set Endpoint = 'http://localhost/PDS_Service/spine/rmessaging',
ClientCertificateThumbprint = '37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7'
where MessageName in('RCMR_IN030000UK06', 'RCMR_IN030000UK07', 'COPC_IN000001UK01', 'MCCI_IN010000UK13', 'RCMR_IN010000UK05')
and RecipientKey = 'GP2GP_EMIS'
```

### Connect
To update the connect changes with the URL & certificate thumbprint, execute below query in EMISConnectCatalogue DB.

```sql
update cp
set KeyValuePair = '<KeyValuePair xmlns="http://connect.e-mis.co.uk/model/" xmlns:ecm="http://connect.e-mis.co.uk/model/">
  <ecm:Item Key="Forwarder">SpineSds</ecm:Item>
  <ecm:Item Key="ForwardAsync">False</ecm:Item>
  <ecm:Item Key="Endpoint">127.0.0.1</ecm:Item>
  <ecm:Item Key="Port">636</ecm:Item>
  <ecm:Item Key="Timeout">60</ecm:Item>
  <ecm:Item Key="ClientCertificateFindValue">37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7</ecm:Item>
  <ecm:Item Key="ServerCertificateVerificationData">None</ecm:Item>
  <ecm:Item Key="httpEndPoint">https://localhost:8080</ecm:Item>
</KeyValuePair>'
from Access.ContractTemplate ct
inner join Access.Contract c on c.ContractTemplateId = ct.ContractTemplateId
inner join Access.ContractPlugin cp  on cp.ContractId = c.ContractId
where ct.MessageName in ('MCCI_IN010000UK13', 'RCMR_IN010000UK05', 'RCMR_IN030000UK06', 'RCMR_IN030000UK07', 'COPC_IN000001UK01')
and SenderIdentifier = '*' and RecipientIdentifier = '*'
and cp.KeyValuePair is not null and InvocationOrder = 2

update cp
set KeyValuePair = '<KeyValuePair xmlns="http://connect.e-mis.co.uk/model/" xmlns:ecm="http://connect.e-mis.co.uk/model/">
  <ecm:Item Key="Forwarder">Http</ecm:Item>
  <ecm:Item Key="ForwardAsync">False</ecm:Item>
  <ecm:Item Key="HttpUseClientCertificate">True</ecm:Item>
  <ecm:Item Key="ClientCertificateFindValue">37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7</ecm:Item>
  <ecm:Item Key="HttpMethod">POST</ecm:Item>
</KeyValuePair>'
from Access.ContractTemplate ct
inner join Access.Contract c on c.ContractTemplateId = ct.ContractTemplateId
inner join Access.ContractPlugin cp  on cp.ContractId = c.ContractId
where ct.MessageName in ('MCCI_IN010000UK13', 'RCMR_IN010000UK05', 'RCMR_IN030000UK06', 'RCMR_IN030000UK07', 'COPC_IN000001UK01')
and SenderIdentifier = '*' and RecipientIdentifier = '*'
and cp.KeyValuePair is not null and InvocationOrder = 3


update cp
set KeyValuePair = '<KeyValuePair xmlns="http://connect.e-mis.co.uk/model/" xmlns:ecm="http://connect.e-mis.co.uk/model/">
  <ecm:Item Key="Forwarder">SpineSds</ecm:Item>
  <ecm:Item Key="ForwardAsync">False</ecm:Item>
  <ecm:Item Key="Endpoint">127.0.0.1</ecm:Item>
  <ecm:Item Key="Port">636</ecm:Item>
  <ecm:Item Key="Timeout">60</ecm:Item>
  <ecm:Item Key="ClientCertificateFindValue">37 af 35 e3 f1 3c 65 ea 0d 7b ba d1 48 33 29 24 a1 ce 41 d7</ecm:Item>
  <ecm:Item Key="ServerCertificateVerificationData">None</ecm:Item>
</KeyValuePair>'
from Access.ContractTemplate ct
inner join Access.Contract c on c.ContractTemplateId = ct.ContractTemplateId
inner join Access.ContractPlugin cp  on cp.ContractId = c.ContractId
where ct.MessageName = 'SDS_QUERY'
```

### EMIS Connect Config Changes
To avoid internal routing, below configuration should be applied in the connect config file.

Go to the location where EMIS Connect are built. Find the ‘Emis.Connect.Core.Host.exe.config’ file and open it.

Change the value of the key ‘internalRoutingDisabled’ from false to true.

![2d30bdbb-74df-4855-9e39-f2c61082630c](https://github.com/Karthi09a/test101/assets/132992711/d697faf9-35c9-4e74-8358-2ab6b1f45a61)

### EMIS Web Registration Module Changes
This change will avoid the validation for Smart card. by applying this change, we can able to see the PDS tab in patient trace dialog.

Open the Registration.build solution from ‘EMIS Web\Demographics\Registration’ location. Find the project ‘Emis.Registration.UI’ and go to the below file as mentioned in the below screen.

Comment the the logic that was written at line 104 & 105. Add the new code as highlighted in the line 106.

![dd2045bd-f228-4986-9472-c811edb4ea6f](https://github.com/Karthi09a/test101/assets/132992711/25e454e5-90f5-4574-8c8e-a2e17951f0f9)

## Run GP2GP flow

- Make sure the InboudMhs, GP2GPMhs & PDS mock are deployed correctly. It should not throw any exception.

- Launch the SDS mock as mentioned in that section.

- Launch Spine tool as mentioned in that section.

- Launch EMIS Web and trace the patient by NHS number. the patient information should be present in the PDS Mock service as mentioned in that section.

- After registration GP2GP flow will be completed within a minute.



