![alt text](/readmeAssets/header.jpg?raw=true "Hoselupf Header Image")

# Hoselupf
This documentation describes the contributions of team "Hoselupf" for the assessment of the master course "Digitalization of Business Processes" at the University of Applied Sciences and Arts Northwestern Switzerland. The following authors contributed to the successful creation of the corresponding artefacts and documentation: 
* Alexandre Miccoli
* Cedric Roggwiller
* David Räz
* Kim Locher
* Marco Zoccoletti

## Description of the use case
To address changing demands with non-scalable production plants can be a challenge for several industries. So as for the company Georg Utz AG which is operating in the field of producing logistic products out of plastic. In order to face this challenge the company associated with partners and suppliers who has similar tools to outsource production if needed. Unfortunately each outsourcing request is individual and is therefore processed in a manual way, especially the supplier selection. With the implementation of a new ERP System and the increasing uncertainty in the market (which leads to more outsourcing demands) the company realised the need of a structured and automated inquiry process. To conclude, the purpose of this case is to describe the decisions and tasks that have to be covered in the production planning before the request gets to the purchasing department and also how the selection of a suitable supplier looks like before placing the order.

### Detailed as-is process description including as-is process model
The process starts when the production planning department receives an input out of the ERP System, that the minimum quantity of a stock product is undercut. After that, they first figure out which mould they need to produce the product and check then, whether they have the capacity to produce internally or if the production is postponable. If none of those is possible the production has to be outsourced. But to which supplier can the request be sent to? This depends on the size of the mould. A larger mold requires a larger machine, which not every supplier has at their disposal. For that reason the production planning decides which supplier group is requested. With that input and some further machine requirements the purchasing department is able to create a request and send it out to the mentioned supplier group. Once the purchasing department has received the offer, the production planning checks the different offers and does a preselection. With the remaining suppliers the purchasing department will negotiate the price and send back the final price to the planning department. The situation is now different. Production planning knows the concrete costs and can now decide once again whether outsourcing is still attractive or if they rather accept delays in delivery and produce internally. Regarding the high quality expectation of the company, it is very likely that they outsource the production in order to meet the delivery time. In this case, the purchasing department sends tasks to all parties involved (material planners, mold makers, logistics) to inform them about the materials that are to be provided to the supplier. 
As it is difficult to the purchasing department to know, how long the preparation lasts, the order with the pick-up date of the provided materials is
sent when the purchaser receives the feedback of the production. With the sent order the process "request to order" ends. 

![alt text](/readmeAssets/as_is_process.png?raw=true "as is process")

## Identification of digitalization potential
During a brainstorming session based on the existing as-is process, the following digitalization potential has been identified:
- Inbound API: The process is to be started by an automatic inbound API call, which would be triggered by the inventory module of the ERP system (e.g., a batch job), as soon as the minimum inventory is undercut. The API call contains data like article, date, stock, reorder level, tools and sales order. This may be subject to change, when implementing the final solution.
- Outbound API: As the ERP system does not know about if there is capacity available to produce the order in house, there needs to be an Outbound API call to the Manufacturing & Execution System (MES), to get the information if the capacity is available or not.
- Decision table: The data from the inbound API as well as from the response of the outbound API are the input values of a new decision table, in which it is decided if the order can be produced in house and if not, a preselection of the manufacturing company can be done automatically based on the machine weight.
- Ticketing/Offer Management Tool: From here on, a combination of forms, notifications and overviews will provide the necessary functionalities to efficiently handle the collaboration between the production and purchasing employee as well with the supplier and production department.

![alt text](/readmeAssets/digitalization_of_process.png?raw=true "digitalization of process")

## Workflow modelling with user tasks, decision tasks and service tasks

![alt text](/readmeAssets/decisiontable.png?raw=true "decision table")


## Selection of iSaaS

The selection of iSaaS tools is based on an analysis of the tools and system landscape already in use by the company. In the sense of reuse, maintainability and the potential cost savings, we prefer software solutions already in place over totally new solutions. In this chapter, the main decisions are first briefly described. The proposed tools and interfaces for the steps to digitalize are then roughly presented individually.

- As Microsoft 365 (M365) is already in use at Georg Utz AG, we mainly build our digitalization solution on tools out of the M365 suite, along with technology support from Microsoft Azure services.
- A M365 test tenant has been created for the project team (hoselupf.onmicrosoft.com), which will be used as a playground and demonstration tenant. Thereby, any damages to productive systems and data can be avoided. The tenant currently provides one user with a Business Basic license as well as the premium version of Power Automate.
- To overlook the process, Camunda has been chosen as a workflow engine, since it offers a better overview and flow control than Power Automate when it comes to integrating software outside the M365 portfolio.
- If our solution is to be productively implemented by the company, Camunda might be hosted within the Azure cloud services. For the sake of the project, the workflow engine will be hosted in Heroku. Otherwise, additional costs would be generated, which the project team would have to cover. The same applies for any other third-party open source software, which the project team would use.
- When it comes to messaging, and only if a message queue is absolutely required, priority is given to [Azure Queue Storage](https://docs.microsoft.com/en-us/azure/storage/queues/storage-queues-introduction). An attempt to establish the connection out of Camunda will then be made by the team, if necessary. At a glance, it seems that the message queue solution would be sufficient, and no [Service Bus](https://azure.microsoft.com/en-us/services/service-bus/) is needed. Since there is no necessity for asynchronous interactions with other systems and no complex orchestration is required, such a message queue should not be of help. 
- For the management of lists (e.g., requests for quotation (RFQ), quotations), [SharePoint Online](https://docs.microsoft.com/en-us/sharepoint/introduction) is the preferred tool. If needed, user interfaces can be built on top (e.g., with Microsoft Power Apps).
- The interactions between SharePoint and external stakeholders (e.g., companies replying to an RFQ) shall happen either via email (with a certain degree of automation) or via custom forms. Any other solution (e.g., Microsoft Forms) is seen as a security issue, since it is almost impossible to confirm the identity of the user. Yet, for the purpose of showcasing the automation potential, Microsoft Forms will be used.
- As far as possible, the low-code paradigm is followed, allowing future "superusers" to maintain and adapt the solution without extensive coding knowledge.
- The primary data store for order-related information created/edited for the digitalized business process shall be SharePoint Online

### Draft of individual steps to digitalize

In each of the following subchapters, the individual steps of the digitalized process are roughly explained.

#### Starting the workflow

When the minimum inventory for a product is undercut, a message is posted to the message queue (MQ), which initializes the workflow in Camunda. This input from the ERP system is mocked by a Microsoft Forms entry, which is automatically forwarded to Camunda using Power Automate.

#### Requesting data from MES

To avoid parallel requests to the MES for the same materials, Camunda will ask production equipment availability in a synchronous manner via an API.  
This service will be mocked in the particular use case.  Therefore, the (mock) MES data will be stored in a SharePoint list.

#### Creating requirements profile

Depending on the result of the decision task, a RFQ profile needs to be created in SharePoint. This task can be handled synchronously. Therefore, the workflow engine triggers a Power Automate API, which creates the entry in SharePoint.
The purchasing department is automatically notified once such a new profile has been created.

#### Initiating a request for quotations

The purchasing department accesses the newly created profile via a Power App (alternatively, directly via the SharePoint Online UI). The purchaser can verify the entry and start the request to partners. Once started, the potential providers receive a unique link to deposit their quotation on a custom portal.  
For the purpose of the demonstration, the link will be identical for every supplier, and the input will be collected via Microsoft Forms.

#### Handing in quotations

As described above, quotations are entered/uploaded by the providers on a dedicated application. The data is automatically saved in SharePoint. In our showcase, the data input is made via Microsoft Forms. For security reasons, as well as for a better user experience, we recommend a custom solution (e.g., a PHP app communicating with SharePoint via the Microsoft Graph API) for the enterprise use.

#### Select a quotation and close the RFQ

Once all quotations arrived, or the deadline is reached, the purchasing department is notified and asked to select the next step (select a quotation, produce internally). Here, the purchaser can still edit the entries in the Power App. Once the RFQ is closed, all involved partners are notified automatically (acceptance/rejection of quotation).

#### Ask for inventory check

If it was decided to produce internally, an MS Planner task is created automatically for the warehouse employees to check availabilities.  

### Architectural Overview

The following image depicts a (highly) simplified overview of the services in use.

![alt text](/readmeAssets/architecture/service_integration_simplified_m365only_v0.jpg?raw=true "Simplified Architecture Overview")

In case not all services could be integrated with Power Automate and/or the need for messaging services arises, we would recommend such an extended implementation.

![alt text](/readmeAssets/architecture/service_integration_simplified_v0.jpg?raw=true "Extended Architecture Overview")

In such case, we added a custom application interface (API Hub), which would be used to collect and manage all APIs. This is to prevent having endpoints manged through different systems.

Alternatively, a solution such as [Azure API Management](https://azure.microsoft.com/en-us/services/api-management) from Microsoft might be evaluated. Thereby, all API endpoints could be managed centrally via Azure. Therefore, the complete domain services might have to be managed on Azure side.

## Service Integration with iSaaS

### Data structure
Before starting with the main Integration the data structure has to be defined. There is one input source (Microsoft Forms) which provides the necessary information to make a MES request and to automatically create a quotation request. Which data is needed and how the data structure looks like is shown in the image below:

![alt text](/readmeAssets/DataStructure.png?raw=true "Data structure")

## Process Modelling with Error Handling

## API Documentation

### Tooling
Please use [Swagger editor](https://editor.swagger.io/) to create/read/update the API definitions and corresponding YAML files.

### APIs
List of available API documentation files

#### Mock MES API to check tool/machine availability
With this API collection, manipulations on the MES-Reservations SharePoint Online List can be performed. The 'MES Availability Check' API is exposed via [Microsoft Power Automate](https://flow.microsoft.com) (formerly Flow). The API key can be found [directly in the flow editor](https://switzerland.flow.microsoft.com/manage/environments/Default-4459efe4-508b-4c52-8576-86316272511a/flows/033f8074-8526-454a-a0e7-46a2f09785a4/details). It is defined as `api_key` in the first condition/branching.

See [YAML File](apiDocs/mes-reservation-lookup-swagger.yaml)

#### RFQ Creation in SharePoint Online
With this API collection, data can be sent to SharePoint Online RFQ List. The 'Send data to SharePoint' API is exposed via [Microsoft Power Automate](https://flow.microsoft.com) (formerly Flow). The API key can be found [directly in the flow editor](https://switzerland.flow.microsoft.com/manage/environments/Default-4459efe4-508b-4c52-8576-86316272511a/flows/42f02ba0-3e63-4b90-aa04-a8fab14b7bb7). It is defined as `api_key` in the first condition/branching.

See [YAML File](apiDocs/send-data-to-sharepoint-swagger.yaml)

## Flow Documentation of PowerAutomate
The business logic and the business process were mapped with Camunda. Within this business process, there are various service tasks that require an external worker. Microsoft PowerSuite was used for this purpose.

The workflows are named after the activities in the business process. The corresponding workflows are listed below.

- Minimum inventory has been undercut
- Send data to Sharepoint
- MES avaialbility check
- Process supplier responses
- Check and complete requirements
- Place order
- Send rejection to suppliers
- Negotiations finished
- Finish supplier preselection
- Send requirements to purchasing
- Inform purchasing about material pickup date

### Short description of the Flows within PowerAutomate

#### Minimum inventory has been undercut
A PowerAutomate flow is started as soon as a new response is created to the following [Microsoft Forms:](https://forms.office.com/Pages/ResponsePage.aspx?id=5O9ZRItQUkyFdoYxYnJRGh4918CydulAnBAyB6X-hK1UNUlZMzRUWlVHNUFIWDI1V09SNUhFWVdKUC4u)

Afterwards these responses are written into an Excel to collect the responses and additionally passed as API Request to Camunda. For this purpose the API interface "Message" is used.

#### Send data to Sharepoint
A flow is required so that the data can be stored and processed in the sharepoint. For this purpose, an HTTP request with a corresponding payload is waited for. The data is entered from the payload into the respective sharepoint columns. By means of Fetch And Lock API Call this is passed on in Camunda. 

#### MES avaialbility check
It is checked whether orders are already reserved for a certain period of time - the ProductionDate and the ProductionDuration are decisive for this query. PowerAutomate waits for a HTTP request that performs this check with the entered values.

#### Process supplier responses
Wenn eine neue Quotation eingegeben wird über Microsoft Forms, dann wird dies via Microsoft Teams in den Production Planning Channel geposted und anschliessend via Message API Call wieder zurück ins Camunda geschickt, sodass der Prozess weiterlaufen kann.

#### Check and complete requirements
When a new quotation is entered via Microsoft Forms, it is posted to the Production Planning Channel via Microsoft Teams and then sent back to Camunda via Message API Call so that the process can continue.

#### Place order
In this Flow the RFQ ID and the Quotation ID is needed in Order to place the Order to the right supplier. An email is being generated an automatically sent to the selected supplier. In addition the Purchasing department is informed within their Microsoft Teams channel that the order was successfully placed. The task will be completed within Camunda wiht a API call.

#### Send rejection to suppliers
This flow is to select the appropriate supplier. First it checks if within the RFQ the production place is set to internal. If this is the case all the suppliers will be informed by email that their quotation was declined. If the production place is set to external the suppliers that did not pass the  the preselections will be informed that their quotation was declined. The supplier that passed the preselection will be informed to add the material pickup date. Also the Purchasing department will be informed within Microsoft Teams that the production was set to external and a supplier was selected. The task will be completed within Camunda wiht a API call. 


#### Negotiations finished
When the negotiations about the price was finished a notification to the Production Planning department will be sent via Microsoft Teams. The task will be completed within Camunda wiht a API call.

#### Finish supplier preselection
When the supplier preselection was finished a notification to the Purchasing department will be sent via Microsoft Teams. The task will be completed within Camunda wiht a API call.

#### Send requirements to purchasing
With this flow the requirements will be sent out from the Microsoft PowerApp to the Microsoft Teams channel of the Purchasing department. The task will be completed within Camunda wiht a API call. 

#### Inform purchasing about material pickup date
With this flow the Purchasing department will be informed when the Production Department entered a pickup date for a certain RFQ. This information will be sent via Microsoft Teams. The task will be completed within Camunda wiht a API call.


## Conclusion & Next Steps
In a next step, the workflow would have to be thoroughly tested and adapted to cover the user requirements best. The input to start the process might then also be replaced by a trigger from the ERP system, and the mock MES connector might be implemented as a real connector querying the manufacturing planning system.

For this flow demonstration, the premium version of Microsoft Power Automate has been used. Such a license would have to be purchased by the company, if the workflow has to be integrated as-is.

We would recommend to move the Camunda hosting to Microsoft Azure (preferably in the same region as Power Automate) to avoid having separate suppliers. Plus, the latency between the platforms in use (i.e., Camunda and Power Automate) might be reduced. With regards to latencies, a monitoring of the infrastructure might also be added to check the 'technical' performance of the implementation.



## How to Hoselupf

## Camunda Versioning and Licence Info

### DigiBP Camunda Template

[![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)
[![Deploy to Heroku](https://img.shields.io/badge/deploy%20to-Heroku-6762a6.svg?longCache=true)](https://heroku.com/deploy)

## Releases

### [7.16.0](https://github.com/DigiBP/digibp-camunda-template/tree/7.16.0)
- Updating Camunda to 7.16.0
- Updating Camunda Spring Boot Starter to 7.16.0
- Updating Spring Boot to 2.5.4

### [7.14.0](https://github.com/DigiBP/digibp-camunda-template/tree/7.14.0)
- Updating Camunda to 7.14.0
- Updating Camunda Spring Boot Starter to 7.14.0
- Updating Spring Boot to 2.3.1.RELEASE

### [7.13.0](https://github.com/DigiBP/digibp-camunda-template/tree/7.13.0)
- Updating Camunda to 7.13.0
- Updating Camunda Spring Boot Starter to 7.13.0
- Updating Spring Boot to 2.2.5.RELEASE

### [3.4.0](https://github.com/DigiBP/digibp-camunda-template/tree/3.4.0)
- Updating Camunda to 7.12.0
- Updating Camunda Spring Boot to 3.4.0
- Updating Spring Boot to 2.2.1.RELEASE

### [3.3.0](https://github.com/DigiBP/digibp-camunda-template/tree/3.3.0)
- Updating Camunda to 7.11.0
- Updating Camunda Spring Boot to 3.3.4
- Updating Spring Boot to 2.1.6.RELEASE
- Removing Maven Wrapper
- Removing Camunda Modeler Templates

### 3.2.3
- Adding `spring-boot-starter-jdbc` dependency, otherwise `spring:datasource` is ignored.

### 3.2.2
- Use of Camunda deployment procedure (embedded:deployment) instead of Spring Boot (embedded:app)
- Change of resource folder structure due to the use deployment procedure  

### 3.2.1
- Rearrange the order of the REST and Web Apps dependencies

### 3.2.0
- Updating Camunda Spring Boot to 3.2.0
- Updating Spring Boot to 2.1.1.RELEASE

### 3.1.0
- Updating Camunda to 7.10.0
- Updating Camunda Spring Boot to 3.1.0

### 3.0.0
- Updating Camunda to 7.9.0
- Updating Camunda Spring Boot to 3.0.0
- Updating Spring Boot to 2.0.2.RELEASE

### 2.0.7
- Default Maven goal `clean spring-boot:run`
- Camunda Modeler Element Template (not in archetype)

### 2.0.6
- A much nicer `deploy to Heroku` shield.

### 2.0.5
- Adding 'authorization: enabled: false' to application.yaml

### 2.0.4
- Adding application-local.yaml to .gitignore
- Updating application.yaml and application-heroku.yaml

### 2.0.3
- Adding a README.md file

### 2.0.2
- Adding a .gitignore file

### 2.0.1
- Adding a .gitignore template file

### 2.0.0
- Updating Camunda Spring Boot to 2.3.0
- Updating Spring Boot to 1.5.8.RELEASE

### 1.0.6

- Updating Camunda Spring Boot to 2.1.0
- Updating Spring Boot to 1.5.3.RELEASE
- Adding to application.yaml: `camunda:bpm:authorization:enabled:true`

### 1.0.5

- Updating Camunda Enterprise Edition to 7.6.4
- Uncommenting Camunda REST API
- Adding (uncommented) Spring Boot Data
- Adding (uncommented) Hibernate configuration to application.yaml

### 1.0.4

- Adding Camunda Enterprise Edition

### 1.0.3

- Fixing the Maven issue

### 1.0.2

- Fixing the Eclipse issue

### 1.0.1

- Fixing the basic package

### 1.0.0

- Initial version

## Maintainer
- [Andreas Martin](https://github.com/andreasmartin)

## License
- [Apache License, Version 2.0](https://github.com/DigiBP/digibp-archetype-camunda-boot/blob/master/LICENSE)
