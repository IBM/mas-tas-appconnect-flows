# App Connect Flows for MAXIMO/TRIRIGA Application Suites

### Summary

[graph goes here]

## Pre-requisites

### Maximo

You should have credentials and access to an instance of Maximo.

Within Maximo, make sure to configure these pre-requisites before you begin.

1. Create Organization - TRIRIGA
2. Create a dummy clearing account in Chart of Accounts
3. Uncheck validation options in Chart of Accounts
4. Create a new GL Account for TRIRIGA - (1001, Description: Testing)
5. Create a site TRIMAIN and set it to active

### TRIRIGA

You should have credentials and access to an instance of TRIRIGA

Download and import the [OM Package](/docs/APIConnector_v0.4.zip) containing TRIRIGA OSLC Resources for below APIs.

Business Object | Integration Type | Supported Integrations
---|---|---
People | Inbound & Outbound | Create and Retire
Building Equipment | Inbound & Outbound | Create and Retire
Space | Inbound & Outbound | Create and Retire

### App Connect

You should have access to an instance of App Connect with a deployed instance of a Designer 


## Step 1 - Select an App Connect Flow for deployment

Locate the .yaml file for the direction you would like to deploy (MX2TRI or TRI2MX) based on the system of the record and download to your local machine. For example, if you are looking to send a Person record from TRIRIGA to Maximo, be sure to use the TRI2MX flow in the Person row.

Asset | Maximo | TRIRIGA
---|---|---
Person | [MX2TRI](/docs/MAX2Tririga/PLUSTMXPerson2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRIPerson2MX.yaml)
Asset | [MX2TRI](/docs/MAX2Tririga/PLUSTMXAsset2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRIAsset2MX.yaml)
Location | [MX2TRI](/docs/MAX2Tririga/PLUSTMXLocation2TRI.yaml) | [TRI2MX](/docs/TRI2Maximo/PLUSTTRISpace2MX.yaml)


## Step 2 - App Connect Config

You will need to create two accounts from the 'Catalog' tab in order to connect the applications.

![Catalog icon](/Pics/Catalog.png)

Once all of the connectors have loaded, scroll down to the HTTP Application and select 'Add a New Account'.

![HTTP Application](/Pics/Connector-1.png)

App Connect creates an account with a generic name in sequential order (Account 1, Account 2, etc.). If this is not your first account, make sure to take note if there are any other generic account names because the number of the one you create will depend on what has already been created. (Example: if Account 1 and Account 2 are present, your new account will be Account 3)

![Connector](/Pics/Connector.png)

See the below table for credentials:

Flow | Username | Password | API key | API location | API key name
---|---|---|---|---|---
Max -> Tri | Your TRIRIGA Username | Your TRIRIGA Password | N/A | N/A | N/A
Tri -> Max | N/A | N/A | Your Maximo apikey | 'header' | 'apikey' 

Once you have connected the account, head back to the Catalog page and rename the account accordingly (mxtririga or trimaximo).


## Step 3 - Import and Test the Flows

### MX2TRI for Person

From your App Connect Dashboard, click 'New' and select 'Import Flow' from the drop down menu

![Import flow](/Pics/Import.png)

Either drag and drop or select the flow you'd like to import. In this example, we'll be using the MX2TRI Person flow.

![Uploaded_Flow](/Pics/Uploaded_Flow.png)

Your flow should now be uploaded onto your App Connect instance. From this screen you can navigate using the 'Edit flow' button to see the individual nodes of this flow. Be sure to select the HTTP account that you configured for Maximo to TRIRIGA for the connector. 

![Completed Flow](/Pics/Completed_Flow.png)

To test this flow, click on the three dots in the top right corner and select 'Start API'.

![Start_Flow](/Pics/Start_API.png)

Go to the 'Test' tab and grab the credentials from the 'POST' option on the left side of the screen

![Test the flow](/Pics/Test_Flow.png)

With these in hand, you can use a tool such as Postman along with a payload from the TRIRIGA pre-requisites to test if the flow is working properly. If there are no errors you should see your payload populate in TRIRIGA.

### TRI2MX for Person

Follow the same steps as above and import the TRI2MX Person flow instead of MX2TRI.

## References
[Mapping Document](/docs/TRIRIGA_Maximo_Field_Mapping-Final.xlsx)

## Troubleshooting


