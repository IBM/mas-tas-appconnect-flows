# App Connect Flows for MAXIMO/TRIRIGA Application Suites


### Step 1 - Obtain Maximo Application Suite

Within OpenShift, on the left side navigate to Operators -> OperatorHub. Search for IBM Maximo Application Suite and install it to your desired namespace


### Step 2 - Obtain TRIRIGA Application Suite


### Step 3 - Obtain App Connect

Download App Connect

### Step 4 - Download App Connect Flows

Depending on the flow you are looking to deploy, you can find them from [this repo](https://github.ibm.com/Watson-IoT/maximo-developer) and download the yaml files you need


### Step 5 - Pre-requisite steps in MAS

First configure the OpenShift environment with the pre-requisite applications, which can be found here: https://www.ibm.com/docs/en/mas85/8.5.0?topic=installation-system-requirements#mas-requirements


Once the operators for MongoDB, cert-manager, Service Binding Operator, Behavior Analytics Services, and Suite License Service are installed and configured, then you are ready to start the MAS build.

### Step 6 - Pre-requisite steps in TAS

Download the [OM Package](/docs/APIConnector_v0.4.zip) containing TRIRIGA OSLC Resources for below APIs.

Business Object | Integration Type | Supported Integrations
---|---|---
People | Inbound & Outbound | Create and Retire
Building Equipment | Inbound & Outbound | Create and Retire
Space | Inbound & Outbound | Create and Retire
