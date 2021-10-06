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

<img src="/Pics/Catalog.png" width=100>

Once all of the connectors have loaded, scroll down to the HTTP Application and select 'Add a New Account'.

<img src="/Pics/Connector-1.png">

App Connect creates an account with a generic name in sequential order (Account 1, Account 2, etc.). If this is not your first account, make sure to take note if there are any other generic account names because the number of the one you create will depend on what has already been created. (Example: if Account 1 and Account 2 are present, your new account will be Account 3)

<img src="/Pics/Connector.png" height=600>

See the below table for credentials:

Flow | Account Name | Username | Password | API key | API location | API key name
---|---|---|---|---|---|---
Max -> Tri | mxtririga | Your TRIRIGA Username | Your TRIRIGA Password | N/A | N/A | N/A
Tri -> Max | trimaximo | N/A | N/A | Your Maximo apikey | 'header' | 'apikey' 

Once you have connected the account, head back to the Catalog page and rename the account according to the Account Name column in the above table.

</details>

## Step 1 - Select an App Connect Flow for deployment

Locate the .yaml file for the direction you would like to deploy (MX2TRI or TRI2MX) based on the system of the record and download to your local machine. For example, if you are looking to send a Person record from TRIRIGA to Maximo, be sure to use the TRI2MX flow in the Person row.

Asset | Maximo | TRIRIGA
---|---|---
Person | [MX2TRI](/docs/MAX2Tririga/PLUSTMXPerson2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRIPerson2MX.yaml)
Asset | [MX2TRI](/docs/MAX2Tririga/PLUSTMXAsset2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRIAsset2MX.yaml)
Location | [MX2TRI](/docs/MAX2Tririga/PLUSTMXLocation2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRISpace2MX.yaml)


## Step 2 - Import the Selected Flow in App Connect

Follow the below steps to import your flow.

<details><summary><b>Import Steps</b></summary>

From your App Connect Dashboard, click 'New' and select 'Import Flow' from the drop down menu.

<img src="/Pics/Import.png" width=200> 

Either drag and drop or select the flow you'd like to import. In this example, we'll be using the MX2TRI Person flow.

<img src="/Pics/Uploaded_Flow.png" width=300>

Your flow should now be uploaded onto your App Connect instance. From this screen you can navigate using the 'Edit flow' button to see the individual nodes of this flow. Be sure to select the HTTP account that you configured for Maximo to TRIRIGA for the connector. 

<img src="/Pics/Completed_Flow.png">

To test this flow, click on the three dots in the top right corner and select 'Start API'.

<img src="/Pics/Start_API.png" width=250>

Go to the 'Test' tab and select the 'POST' option on the left side of the screen.

![Test the flow](/Pics/Test_Flow.png)
 
Click on 'Try It' and grab the url and security credentials from this screen for the next step.
 
<img src="/Pics/AppConnect-Config-Full.png" >

</details>

## Step 3 - Configure Maximo and TRIRIGA instances with App Connect urls

Using the credentials from the end of Step 2, populate your instances of Maximo and TRIRIGA with the correct End Points to establish the correct connection.

<details><summary><b>Maximo</b></summary>

[pictures of End Points in Maximo]
</details>
 
 <details><summary><b>TRIRIGA</b></summary>
<img src="/Pics/TRIRIGA-EndPoint.png">
</details>

## Step 4 - Test the Flow

With these 3 steps completed, you can test the flow with a payload from the TRIRIGA pre-requisites. If there are no errors you should see your payload populate in TRIRIGA.

## References
[Mapping Document](/docs/TRIRIGA_Maximo_Field_Mapping-Final.xlsx)

## Troubleshooting


