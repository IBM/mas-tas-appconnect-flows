# App Connect Flows for MAXIMO/TRIRIGA Application Suites

### Summary

At the end of this process, you should be able to send multiple types of assets from Maximo to TRIRIGA and TRIRIGA to Maximo utilizing App Connect.

<img src="/Pics/TRI-MX-Architecture.jpeg">

## Pre-requisites

 <details><summary><b>MAXIMO</b></summary>

You should have credentials and access to an instance of Maximo.

Within Maximo, make sure to configure these pre-requisites before you begin.

1. Create Organization - TRIRIGA
2. Create a dummy clearing account in Chart of Accounts
3. Uncheck validation options in Chart of Accounts
4. Create a site TRIMAIN and set it to active

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

[graph of flow]

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


