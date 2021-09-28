# App Connect Flows for MAXIMO/TRIRIGA Application Suites


### Step 1 - Download App Connect Flows

Locate the .yaml file for the direction you would like to deploy (MX2TRI or TRI2MX) and download to your local machine. Wait until the end of Step 2 before importing into App Connect.

### Step 2 - App Connect Config

If you do not already have an instance of App Connect with a Designer Authoring up and running, please make sure to complete that first

You will need to create two accounts from the 'Catalog' tab in order to connect the applications

Once all of the connectors have loaded, scroll down to the HTTP Application and select 'Add a New Account'.

App Connect creates an account with a generic name in sequential order (Account 1, Account 2, etc.). If this is not your first account, make sure to take note if there are any other generic account names because the number of the one you create will depend on what has already been created. (Example: if Account 1 and Account 2 are present, your new account will be Account 3)

[screenshot of connector]

See the below table for credentials:

Flow | Username | Password | API key | API location | API key name
---|---|---|---|---|---
Max -> Tri | apiuser | 1Password* | N/A | N/A | N/A
Tri -> Max | N/A | N/A | [apikey] | header | apikey 

Once you have connected the account, head back to the Catalog page and rename the account accordingly (mxtririga or trimaximo). These are the only two connectors needed for the flows, so once you have them configured you can go to the Dashboard and import the flows. The flow should populate with the appropriate account.

[screenshot of complete flow]

### Step 3 - Pre-requisite steps in MAS

First configure the OpenShift environment with the pre-requisite applications, which can be found here: https://www.ibm.com/docs/en/mas85/8.5.0?topic=installation-system-requirements#mas-requirements


Once the operators for MongoDB, cert-manager, Service Binding Operator, Behavior Analytics Services, and Suite License Service are installed and configured, then you are ready to start the MAS build.

### Step 4 - Pre-requisite steps in TAS

Download the [OM Package](/docs/APIConnector_v0.4.zip) containing TRIRIGA OSLC Resources for below APIs.

Business Object | Integration Type | Supported Integrations
---|---|---
People | Inbound & Outbound | Create and Retire
Building Equipment | Inbound & Outbound | Create and Retire
Space | Inbound & Outbound | Create and Retire
