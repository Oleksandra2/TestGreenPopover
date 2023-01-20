---
title: Use the Sample OData Service for Mobile Apps Test Green Pop-over Twenty updated
description: Use the sample OData service included with the SAP Mobile Services for development and operations account.
auto_validation: true
primary_tag: topic>abap-development
tags: [ tutorial>intermediate, topic>abap-development  ]
time: 10
---
## Prerequisites  
 - **Development environment:** Apple iMac, MacBook or MacBook Pro running Xcode 9 or higher
 - **SAP BTP SDK for iOS:** Version 2.0

## Details
### You will learn  
  - How to access the sample OData service that comes with the SAP Mobile Services for development and operations
  - How to generate sample data for use in your application


---

A sample OData service is available for developers to use during development and testing. Administrators can configure the sample service via the cockpit. You can view the root service and metadata URLs, and generate sample sales orders and purchase orders for multiple entity sets. You can also view the data for each entity in a separate text file, and reset the sample data.

> You can configure **only one** sample OData service per tenant.

The following roles are required to use this service:

| Role | Service URL | Description
|---|---|---|
| Developer | `<JAVA application URL>/mobileservices/SampleServices/ESPM.svc/` | Access the sample OData service |
| Administrator | `/mobileservices/Admin/ESPM.svc/` | Administrators configure an application in the cockpit to enable the service for the developer |

[ACCORDION-BEGIN [Step 1: ](Open Developer tab)]

In SAP Mobile Services for development and operations cockpit, navigate to **Developer**.

![Developer tab](fiori-ios-hcpms-sample-odata-service-01.png)

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 2: ](Examine the Service information)]

To view the service information, click the URL next to **Root URL**.

To view the schema metadata for the service, click the URL next to **Metadata URL**.


[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 3: ](Generate sample data)]

In order to use the sample service, you need to generate some sample data first.

1. Click the **Generate sample sales order** button to generate 10 sample sales orders.

2. Click the **Generate sample purchase order** button to generate 10 sample purchase orders

> Every click on each button will add another 10 sample orders to the Sample OData service.


[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 4: ](Configure the application definition)]

Navigate to **Applications**, and next to the `com.sap.tutorial.demoapp.Demo` application, click the **Action** button, and select **Configure**.


[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 5: ](Set Security Configuration)]

Under the **Information** tab, make sure **Security Configuration** is set to **Basic**:

![Developer tab](fiori-ios-hcpms-sample-odata-service-05.png)


[DONE]
[ACCORDION-END]

