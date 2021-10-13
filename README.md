# App Connect Flows for MAXIMO/TRIRIGA Application Suites

### Summary

At the end of this process, you should be able to send multiple types of assets from Maximo to TRIRIGA and TRIRIGA to Maximo utilizing App Connect.

<img src="/Pics/TRI-MX-Architecture.jpeg">

## Pre-requisites

 <details><summary><b>MAXIMO</b></summary>

You should have credentials and access to an instance of Maximo.

Within Maximo, you will need to configure your instance to be ready to receive records from TRIRIGA. If these pre-requisites are not completed, the action will not be recorded.

1. Create an Organization named TRIRIGA
 
  - Navigate to the 'Organizations' page and click the blue + button on the top row.
  - Fill in the Organization name with TRIRIGA and the description as "TRIRIGA Organization".
  - Fill in the remaining required fields as such
    1. Base Currency 1: USD
    2. Item Set: SET1
    3. Company Set: COMPSET1
    4. Default Item Status: PENDING
    5. Default Stock Category: STK
  - Click Save Organization on the left side of the screen under Common Actions. We will come back later to set to Active once we have a clearing account.

2. Create a site TRIMAIN and set it to active
 
  - On the Organization page, click on the 'Sites' tab at the top of the page.
  - Click New Row under 'Sites' and enter TRIMAIN for Site and "MAIN Site" for Description. Set the site to Active.
  - Click Save Organization.

3. Create a Testing clearing account in Chart of Accounts
  
  - Navigate to Financial -> Chart of Accounts and click on the previously created TRIRIGA org
  - There should be no GL Accounts for TRIRIGA present
  -

4. Create the PLUSTTRIRIGA External System

  - Navigate to Integration -> External Systems and click on the blue plus button at the top of the page.
  - Under the System name fill in PLUSTTRIRIGA and in the Description fill in "To integrate Maximo with TRIRIGA"
  - Enable the System and then fill in the Queues on the right hand side as follows:
    1. Outbound Sequential Queue: jms/maximo/int/queues/sqout
    2. Inbound Sequential Queue: jms/maximo/int/queues/sqin
    3. Inbound Continuous Queue: jms/maximo/int/queues/cqin
  - Save the External System

5. Create the Publish Channels for each integration

  - Navigate to Integration -> Publish Channels and click on the blue plus button at the top of the page. Do this for each integration.
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

 6. Create the Enterprise Services for each integration
 
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

7. Create the End Points for each integration 
 
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


8. Link the Publish Channels & Enterprise Services to the PLUSTTRIRIGA External System
 
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

9. System Properties
  -
  -
  -  

10. Database Configuration
  -
  -
  -  

11. API Key
  -
  -
  -  


  </details>
  
 <details><summary><b>TRIRIGA</b></summary>

You should have credentials and access to an instance of TRIRIGA

Download and import the [OM Package](/docs/APIConnector_v0.4.zip) containing TRIRIGA OSLC Resources for below APIs.

Business Object | Integration Type | Supported Integrations
---|---|---
People | Inbound & Outbound | Create and Retire
Building Equipment | Inbound & Outbound | Create and Retire
Space | Inbound & Outbound | Create and Retire
  
  </details>
  

 <details><summary><b>App Connect</b></summary>

You should have access to an instance of App Connect with a deployed instance of a Designer

You will need to create two accounts from the 'Catalog' tab in order to connect the applications.

Once all of the connectors have loaded, type in 'http' to find the HTTP Application.
 
<img src="/Pics/App-Connect-Catalog.jpeg">

If this is your first account, select 'Connect' to begin setting up the initial HTTP account. If this is not your first account, make sure to take note if there are any other generic account names present because the number of the one you create will depend on what has already been created. App Connect creates an account with a generic name in sequential order (Example: if Account 1 and Account 2 are present, your new account will be Account 3).

See the below table for credentials:

Flow | Account Name | Username | Password | API key | API location | API key name
---|---|---|---|---|---|---
Max -> Tri | mxtririga | Your TRIRIGA Username | Your TRIRIGA Password | N/A | N/A | N/A
Tri -> Max | trimaximo | N/A | N/A | Your Maximo apikey | 'header' | 'apikey' 

Once you have connected the account, head back to the HTTP Application on the Catalog page and rename the new account according to the Account Name column in the above table.

</details>

## Step 1 - Select an App Connect Flow for deployment

App Connect is used to synchronize records in both Maximo and TRIRIGA. When there is a change on one application, this triggers a request to App Connect which updates or populates the records in the other application. For example, take the Maximo to TRIRIGA flow for a Person record:

<img src="/Pics/MX2TRI-Graph.png" >

In Maximo a record for an employee is updated to reflect a change in address. That triggers the App Connect flow and a request with the updated record to be sent to Maximo. The flow parses the JSON of the request, maps the fields from Maximo to TRIRIGA, and then the HTTP node sends a Post request to add or change the record in TRIRIGA.

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

To test this flow, click 'Done' on the top right of the screen then click on the three dots in the top right corner and select 'Start API'.

<img src="/Pics/Start_API.png" width=250>

Go to the 'Test' tab and select the 'POST' option on the left side of the screen.

<img src="/Pics/Test_Flow.png">
 
Click on 'Try It' and grab the url and security credentials from this screen for the next step.
 
<img src="/Pics/AppConnect-Config-Full.png" >

</details>

## Step 3 - Configure Maximo and TRIRIGA instances with App Connect urls

Using the credentials from the end of Step 2, populate your instances of Maximo and TRIRIGA with the correct End Points to establish the correct connection.

<details><summary><b>Maximo</b></summary>

From the main page of Maximo, click the menu icon on the top left and navigate to Integration -> End Points
 
<img src="/Pics/Maximo-EndPoint-Navigation.jpeg" >
 
Fill in the properties with the url, username, and password from Step 2
 
<img src="/Pics/Maximo-EndPoint-Properties.png" >
 
Click the 'Test' button at the bottom right of the screen and send a simple {"hello":"world"} to make sure the End Point returns a 200 status code. If it does not return a successful status code, refer to the Troubleshooting section.
 
</details>
 
 <details><summary><b>TRIRIGA</b></summary>

From the main page of TRIRIGA, click on Tools -> System Setup -> Integration -> Integration Object.
 
Under the 'Name' column, type in 'apic', and select the integration object that pertains to the record you're sending. 
 
<img src="/Pics/TRIRIGA-EndPoint.png">
 
Click on the object and fill in the credentials in the pop-up box.
 
</details>

## Step 4 - Test the Flow

With these 3 steps completed, you can test the flow with a payload.

Head back to the 'Try It' page in your deployed flow where you collected your credentials and scroll down to the bottom of the page under Parameters.

<img src="/Pics/App-Connect-Test.jpeg" >

In here, you can generate a test payload and send it through the flow to monitor if the information populates in the desired application.

If you get a response other than 200 from the Test, refer to Troubleshooting.

## References
[Mapping Document](/docs/TRIRIGA_Maximo_Field_Mapping-Final.xlsx)

## Troubleshooting


