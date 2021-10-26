# App Connect Flows for MAXIMO/TRIRIGA Application Suites

### Summary

This repository is for a developer looking to sync the databases of Maximo and TRIRIGA using App Connect flows to facilitate. App Connect is used to synchronize records in both Maximo and TRIRIGA. When there is a change on one application, this triggers a request to App Connect which updates or populates the records in the other application.

<img src="/Pics/MX2TRI-Graph.png" >

1. In the above example, when a record is updated in Maximo, it will trigger the flow to synchronize with TRIRIGA. There is also a flow that works in the reverse direction, that works in a similar way.
2. App Connect sends a request with the updated information through the flow towards the end destination (TRIRIGA).
3. A JSON Parser sifts through the Request and converts it to an object. 
4. Each object that is returned from the JSON Parser goes through Steps 5-8.
5. The data from the object is mapped to the corresponding fields in the destination application (TRIRIGA). 
6. The newly mapped data is sent to the destination application (TRIRIGA) where the record is created or updated.
7. This record is then validated with the original application (Maximo) via another Post request. 
8. An ID is either created or updated within the original application (Maximo).
9. This process is repeated for each object that is present in the original request (Maximo).

At the end of this process, you should be able to send a Person, Asset, or from Maximo to TRIRIGA and TRIRIGA to Maximo utilizing App Connect.

## Pre-requisites

 <details><summary><b>MAXIMO</b></summary>

You should have credentials and access to an instance of Maximo.
 
The following steps and pre-requisites are done against a Maximo demo database. The naming conventions may slightly differ from this, but these are the necessary components. 

Within Maximo, you will need to configure your instance to be ready to receive records from TRIRIGA. If these pre-requisites are not completed, the action will not be recorded.

### 1. Create an Organization named TRIRIGA
 
  - Navigate to the 'Organizations' page and click the blue + button on the top row.
  - Fill in the Organization name with TRIRIGA and the description as "TRIRIGA Organization".
  - Fill in the remaining required fields as such
    1. Base Currency 1: USD
    2. Item Set: SET1
    3. Company Set: COMPSET1
    4. Default Item Status: PENDING
    5. Default Stock Category: STK
  - Click Save Organization on the left side of the screen under Common Actions. We will come back later to set to Active once we have a clearing account.

### 2. Create a site TRIMAIN and set it to active
 
  - On the Organization page, click on the 'Sites' tab at the top of the page.
  - Click New Row under 'Sites' and enter TRIMAIN for Site and "MAIN Site" for Description. Set the site to Active.
  - Click Save Organization.

### 3. Create a Testing clearing account in Chart of Accounts
  
  - Navigate to Financial -> Chart of Accounts and click on the previously created TRIRIGA org in the Organizations table. Currently, there should be no GL Accounts for TRIRIGA present
  - Click 'GL Component Maintenance' on the left side under More Actions and add a New Row with the following values:
    1. GL Component Value: 1001
    2. Description: Testing
    3. Active?: Yes
  - Click OK and you should be able to add a GL Account. Click New Row under GL Accounts for TRIRIGA and click the magnifying glass to search for the GL Component you just created. Select it and it should populate in the GL Account & Description fields. The Active Date field should auto populate to the current date.
 - Now that this account is present, head back to Organizations and update the TRIRIGA organization to show the Clearing Account you just created, tick the Active box, and click Save Organization.

### 4. Create the PLUSTTRIRIGA External System

  - Navigate to Integration -> External Systems and click on the blue plus button at the top of the page.
  - Under the System name fill in PLUSTTRIRIGA and in the Description fill in "To integrate Maximo with TRIRIGA"
  - Enable the System and then fill in the Queues on the right hand side as follows:
    1. Outbound Sequential Queue: jms/maximo/int/queues/sqout
    2. Inbound Sequential Queue: jms/maximo/int/queues/sqin
    3. Inbound Continuous Queue: jms/maximo/int/queues/cqin
  - Save the External System

### 5. Create the Publish Channels for each integration

  - Navigate to Integration -> Publish Channels
  - For Asset
    1. Search for 'MXASSETInterface' under the Publish Channel field. Click on the channel and from the left side of the screen select 'Duplicate Publish Channel' 
    2. Rename the channel PLUSTMXASSETInterface
    3. Click on 'Enable Event Listener' on the left side under More Actions
    4. Make sure Publish JSON and Retain MBO's are checked, the Operation should default to Publish and the Adapter should default to MAXIMO.
    5. Click 'Save Publish Channel' on the left under Common Actions
  - For Location
    1. Search for 'MXOPERLOCInterface' under the Publish Channel field. Click on the channel and from the left side of the screen select 'Duplicate Publish Channel' 
    2. Rename the channel PLUSTMXOPERLOCInterface
    3. Click on 'Enable Event Listener' on the left side under More Actions
    4. Make sure Publish JSON and Retain MBO's are checked, the Operation should default to Publish and the Adapter should default to MAXIMO.
    5. Click 'Save Publish Channel' on the left under Common Actions
  - For Person
    1. Search for 'MXPERSONInterface' under the Publish Channel field. Click on the channel and from the left side of the screen select 'Duplicate Publish Channel' 
    2. Rename the channel PLUSTMXPERSONInterface
    3. Click on 'Enable Event Listener' on the left side under More Actions
    4. Make sure Publish JSON and Retain MBO's are checked, the Operation should default to Publish and the Adapter should default to MAXIMO.
    5. Click 'Save Publish Channel' on the left under Common Actions

### 6. Create the Enterprise Services for each integration
 
  - Navigate to Integration -> Enterprise Services and click on the blue plus button at the top of the page
  - For Asset
    1. Under the System name fill in PLUSTMXASSETInterface and in the Description fill in "ASSETS"
    2. Select 'MXASSET' under Object Structure which will populate the Object Structure Sub-Records table
    3. Click on 'Enable Event Listener' on the left side under More Actions
    4. Make sure Publish JSON and Retain MBO's are checked, the Operation should default to Publish and the Adapter should default to MAXIMO.
    5. Click 'Save Publish Channel' on the left under Common Actions
  - For Location
    1. Under the System name fill in PLUSTMXOPERLOCInterface and in the Description fill in "OPERATION LOCATION"
    2. Select 'MXOPERLOC' under Object Structure which will populate the Object Structure Sub-Records table
    3. Click on 'Enable Event Listener' on the left side under More Actions
    4. Make sure Publish JSON and Retain MBO's are checked, the Operation should default to Publish and the Adapter should default to MAXIMO.
    5. Click 'Save Publish Channel' on the left under Common Actions
  - For Person
    1. Under the System name fill in PLUSTMXPERSONInterface and in the Description fill in "PERSON"
    2. Select 'MXPERSON' under Object Structure which will populate the Object Structure Sub-Records table
    3. Click on 'Enable Event Listener' on the left side under More Actions
    4. Make sure Publish JSON and Retain MBO's are checked, the Operation should default to Publish and the Adapter should default to MAXIMO.
    5. Click 'Save Publish Channel' on the left under Common Actions

### 7. Create the End Points for each integration 
 
  - Navigate to Integration -> End Points and click on the blue plus bitton at the top of the page
  - For Asset
    1. Under End Point fill in PLUSTASSET and in the Description fill in "AppConnect ASSET outbound to TRIRIGA"
    2. Select 'HTTP' for Handler
    3. Click on 'Save End Point' on the left side under More Actions which will populate the Properties for the End Point
    4. Until the flows have a destination url, we can only fill in certain fields:
       - HEADERS: "Content-Type: application/json"
       - HTTPMETHOD: POST
    5. Save the End Point
 
  - For Location
    1. Under End Point fill in PLUSTLOCATION and in the Description fill in "AppConnect LOCATION outbound to TRIRIGA"
    2. Select 'HTTP' for Handler
    3. Click on 'Save End Point' on the left side under More Actions which will populate the Properties for the End Point
    4. Until the flows have a destination url, we can only fill in certain fields:
       - HEADERS: "Content-Type: application/json"
       - HTTPMETHOD: POST
    5. Save the End Point
  - For Person
    1. Under End Point fill in PLUSTPERSON and in the Description fill in "AppConnect PERSON outbound to TRIRIGA"
    2. Select 'HTTP' for Handler
    3. Click on 'Save End Point' on the left side under More Actions which will populate the Properties for the End Point
    4. Until the flows have a destination url, we can only fill in certain fields:
       - HEADERS: "Content-Type: application/json"
       - HTTPMETHOD: POST
    5. Save the End Point


### 8. Link the Publish Channels & Enterprise Services to the PLUSTTRIRIGA External System
 
  - On the External Systems page, switch over to the Publish Channels tab. One at a time, click New Row and select the Publish Channel for the integrations you just created. When you have finished, your linked Publish Channels should look like the table below:
 
  Channel | Description | Adaptor | End Point | User Defined | Enabled
  ---|---|---|---|---|---
  PLUSTMXASSETInterface| ASSETS | MAXIMO | PLUSTASSET | Yes | Yes
  PLUSTMXOPERLOCInterface | OPERATION LOCATION | MAXIMO | PLUSTLOCATION | Yes | Yes
  PLUSTMXPERSONInterface | PERSON | MAXIMO | PLUSTPERSON | Yes | Yes
 
  - Save the External System
  - Switch over to the Enterprise Services tab. One at a time, click New Row and select the Enterprise Service for the integrations you just created. When you have finished, your linked Enterprise Services should look like the table below:
 
  Service | Description | Adaptor | Operation | User Defined | Enabled | Use Continuous Queue?
  ---|---|---|---|---|---|---
  PLUSTMXASSETInterface| ASSETS | MAXIMO | Sync | Yes | Yes | Yes
  PLUSTMXOPERLOCInterface | OPERATION LOCATION | MAXIMO | Sync | Yes | Yes | Yes
  PLUSTMXPERSONInterface | PERSON | MAXIMO | Sync | Yes | Yes | Yes
 
  - Save the External System

### 9a. API Key (Maximo-X)
 
  - First, check to see if your version of Maximo comes with Maximo-X. Navigate to Administration -> Administration and a new tab/window should open with the Maximo-x application. If you are having trouble reaching this page or it is not installed, follow these steps in order to create an API key.
  - You should be on a page titled 'Integration'. Click on the tab at the top of the page that says API Keys and click on the button with the blue plus sign that reads 'Add API key'
  -  Select user 'MXINTADM' and click the Add button to generate an API key for this user. Securely store this API key for later use.
 
### 9b. API Key (No Maximo-X)
 
  - Follow the steps in [this documentation](https://www.ibm.com/docs/en/mam/7.6.1.2?topic=components-api-keys) to generate an API key for your user
 
### 10. Integration Controls
 
  - On the left side of the External Systems page, select Setup Integration Controls under 'More Actions'
  - There should be 6 Integration Controls listed with the following associations:
 
    Integration Control | MAXIMO Value | External Value
    ---|---|---
    PLUSTLOCSTATUS | ACTIVE | ACTIVE
    "" | INACTIVE | REVIEW IN PROGRESS
    "" | OPERATING | OPERATING
    PLUSTORG | TRIMAIN | IBM
    "" | TRIRIGA | TRIRIGA
    PLUSTORGEN | TRIRIGA | EAGLENA
    "" | TRIRIGA | IBM
    "" | TRIRIGA | MAXIMO ORG
    "" | TRIRIGA | TEST
    "" | TRIRIGA | TRIRIGA
    PLUSTPRIORITY | 1 | High
    "" | 2 | Medium
    "" | 3 | Low
    PLUSTSITEEN | TRIMAIN | BEDFORD
    "" | TRIMAIN | SPACE 01
    "" | TRIMAIN | TEST
    "" | TRIMAIN | TRIMAIN

  </details>
  
 <details><summary><b>TRIRIGA</b></summary>

You should have credentials and access to an instance of TRIRIGA

Navigate to Tools > Object Migration and import the [OM Package](/docs/APIConnector_v0.4.zip) containing TRIRIGA OSLC Resources for below APIs.
 
OM Package include below APIs and its dependents:

Object | Integration Direction 
---|---
triAPICAssetCF | TRIRIGA Inbound 
triAPICBuildingCF | TRIRIGA Inbound 
triAPICFloorCF | TRIRIGA Inbound 
triAPICPeopleCF | TRIRIGA Inbound 
triAPICPropertyCF | TRIRIGA Inbound 
triAPICServiceRequestCF | TRIRIGA Inbound 
triAPICSpaceCF | TRIRIGA Inbound 
triAPICWorkTaskCF | TRIRIGA Inbound 
triWorkTask - APIC - HTTP Post | TRIRIGA Outbound
triSpace - APIC - HTTP Post | TRIRIGA Outbound
triServiceRequest - APIC - HTTP Post | TRIRIGA Outbound
triProperty - APIC - HTTP Post | TRIRIGA Outbound
triPeople - APIC - HTTP Post | TRIRIGA Outbound
triFloor - APIC - HTTP Post | TRIRIGA Outbound
triBuildingEquipment - APIC - HTTP Post | TRIRIGA Outbound
triBuilding - APIC - HTTP Post | TRIRIGA Outbound

 
  </details>

 <details><summary><b>App Connect</b></summary>

### You should have access to an instance of App Connect with a deployed instance of a Designer

You will need to create two accounts from the 'Catalog' tab in order to connect the applications.

Once all of the connectors have loaded, type in 'http' to find the HTTP Application.
 
<img src="/Pics/App-Connect-Catalog.jpeg">

If this is your first account, select 'Connect' to begin setting up the initial HTTP account. If this is not your first account, make sure to take note if there are any other generic account names present because the number of the one you create will depend on what has already been created. App Connect creates an account with a generic name in sequential order (Example: if Account 1 and Account 2 are present, your new account will be Account 3).

See the below table for credentials:

Flow | Account Name | Username | Password | API key | API location | API key name
---|---|---|---|---|---|---
Max -> Tri | mxtririga | Your TRIRIGA Username | Your TRIRIGA Password | N/A | N/A | N/A
Tri -> Max | trimaximo | N/A | N/A | Your Maximo apikey | header | apikey 

Once you have connected the account, head back to the HTTP Application on the Catalog page and rename the new account according to the Account Name column in the above table.
  

</details>

These pre-requisites are assuming that all three applications are behind the same firewall. If any of these three applications do not share the same firewall, you will need to establish a secure connection between the applications. IBM does provide a product that accomplishes this- Secure Gateway. Learn more about getting started with Secure Gateway [here](https://cloud.ibm.com/docs/SecureGateway?topic=SecureGateway-getting-started-with-sg)

## Step 1 - Select an App Connect Flow for deployment

Locate the .yaml file for the direction you would like to deploy (MX2TRI or TRI2MX) based on your system of record and download to your local machine. From the previous example, you would use the MX2TRI flow in the Person row.

Asset | Maximo | TRIRIGA
---|---|---
Person | [MX2TRI](/docs/MAX2Tririga/PLUSTMXPerson2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRIPerson2MX.yaml)
Asset | [MX2TRI](/docs/MAX2Tririga/PLUSTMXAsset2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRIAsset2MX.yaml)
Location | [MX2TRI](/docs/MAX2Tririga/PLUSTMXLocation2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRISpace2MX.yaml)


## Step 2 - Import the Selected Flow in App Connect

Follow the below steps to import your flow.

<details><summary><b>Import Steps</b></summary>

From your App Connect Dashboard, click 'New' and select 'Import Flow' from the drop down menu.

<img src="/Pics/App-Connect-Dashboard.jpeg"> 

Either drag and drop or select the flow you'd like to import. In this example, we'll be using the MX2TRI Person flow.

<img src="/Pics/Uploaded_Flow.png" width=300>

Your flow should now be uploaded onto your App Connect instance. From this screen you can navigate using the 'Edit flow' button to see the individual nodes of this flow. Be sure to select the HTTP account that you configured for Maximo to TRIRIGA for the connector. 

<img src="/Pics/Completed_Flow.png">

Click 'Done' on the top right of the screen then click on the three dots in the top right corner and select 'Start API'.

<img src="/Pics/Start_API.png" width=250>

Go to the 'Test' tab once it shows that the flow is 'Running' and select the 'POST' option on the left side of the screen.
 
Click on 'Try It' and grab the url and security credentials from this screen for the next step.
 
<img src="/Pics/AppConnect-Config-Full.jpeg" >

</details>

## Step 3 - Configure Maximo and TRIRIGA instances with App Connect urls

Using the credentials from the end of Step 2, populate your instances of Maximo and TRIRIGA with the correct End Points of the flow you just created.

<details><summary><b>Maximo</b></summary>

From the main page of Maximo, click the menu icon on the top left and navigate to Integration -> End Points
 
<img src="/Pics/Maximo-EndPoint-Navigation.jpeg" >
 
Fill in the properties with the url, username, and password from Step 2
 
<img src="/Pics/Maximo-EndPoint-Properties.png" >
 
Click the 'Test' button at the bottom right of the screen and send a simple {"hello":"world"}. With the proper configuration, there will be an expected error that ends with 'Bad Request'. If there is a different error than Bad Request in the Response window, refer to the Troubleshooting section to debug.
 
</details>
 
 <details><summary><b>TRIRIGA</b></summary>

From the main page of TRIRIGA, click on Tools -> System Setup -> Integration -> Integration Object.
 
Under the 'Name' column, type in 'apic', and select the integration object that pertains to the record you're sending. 
 
<img src="/Pics/TRIRIGA-EndPoint.png">
 
Click on the object and fill in the credentials in the pop-up box.
 
</details>

## Step 4 - Configure WebSphere Certificates

This step makes a test connection to a Secure Sockets Layer (SSL) port and retrieves the signer from the server during the handshake.

<details><summary><b>Websphere</b></summary>

Login to your Websphere console that is hosting the Maximo server

<img src="/Pics/Websphere-Home.png">

Click on Security -> SSL certificate & key management. Under 'Related Items' click on 'Key stores and certificates'

<img src="/Pics/Websphere-Keystores.png">

Click on 'CellDefaultTrustStore' and on the next page under 'Additional Properties' click on 'Signer certificates'. 

<img src="/Pics/Websphere-Signercerts.png">

From this page, click on the button that says 'Retrieve from Port' and fill in the required fields using the table below

Field | Value
---|---
Host | The host from the url in Step 2
Port | 443
Alias | appconnect

Once all three have been entered in, click 'Retrieve signer information' and the information from the url will populate on screen. Click save in the box at the top and then repeat the process for 'NodeDefaultTrustStore'
 
 </details>

## Step 5 - Test the Flow

With these 4 steps completed, you can test the flow with a payload.

Head back to the 'Try It' page in your deployed flow where you collected your credentials and scroll down to the bottom of the page under Parameters.

<img src="/Pics/App-Connect-Test.jpeg" >

In here, you can generate a test payload and send it through the flow to monitor if the information populates in the desired application.

If you get a response other than 200 from the Test, refer to Troubleshooting.

## References
[Mapping Document](/docs/TRIRIGA_Maximo_Field_Mapping-Final.xlsx)

## Troubleshooting

<details><summary><b>Common errors that arise from App Connect</b></summary>

 
 ## Testing the whole flow
 
 - If you get a 404 Not Found error when trying to test your flow, this could mean that your flow is not running. Double check to make sure the flow shows a green dot and says 'Running' after you have made edits.
 
 - If you get a 400 Bad Request error when testing your flow, this could mean that you have the wrong account configurations. Double check to make sure the Accounts you set up in the App Connect pre-requisite section are correct.
 
</details>
<details><summary><b>Common errors that arise from Maximo</b></summary>
 
 ## Testing the End Point
 
 - If you get an error that reads "The response code received from the HTTP request from the endpoint is not successful.", this is related to the End Point that you configured. Double check and make sure that you have put in the correct values in 'End Points'. If you copy/pasted the values, make sure there are no accidental spaces at the beginning or end.
 
 
</details>

<details><summary><b>Common errors that arise from TRIRIGA</b></summary>
 
 - Clear OSLC Cache in TRIRIGA Admin Console in case the integrations do not work in intended manner.
 
</details>

