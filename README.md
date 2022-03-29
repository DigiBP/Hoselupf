# Hoselupf
This documentation describes the contributions of team "Hoselupf" for the assessment of the master course "Digitalization of Business Processes" at the University of Applied Sciences and Arts Northwestern Switzerland. The following authors contributed to the successful creation of the corresponding artefacts and documentation: 
* Alexandre Miccoli
* Cedric Roggwiller
* David Räz
* Kim Locher
* Marco Zoccoletti

## Description of the use case
To address changing demands with non scalable production plants can be a challenge for several industries. So as for the company Georg Utz AG which is operating in the field of producing logistic products out of plastic. In order to face this challenge the company associated with partners / suppliers who has similar tools to outsource production if needed. Unfortunately each outsourcing request is individual and is therefore processed in a manual way, especially the supplier selection. With the implementation of a new ERP System and the incresing uncertainty in the market (which leads to more outsourcing demands) the company realised the need of a structured and automated inquiry process. To conclude, the purpose of this case is to describe the decisions and tasks that have to be covered in the production planning before the request gets to the purchaising department and also how the selection of a suitable supplier looks like before placing the order.

### Detailed as-is process description
The process starts at the moment, when the production planning department recieves an input out of the ERP System, that the minimum quantity of a stock product is undercut. After that they first fiqure out which mould they need to produce the product and check then, whether they have the capacity to produce internally or if the production is postponable. If none of those is possible the production has to be outsourced. But to which supplier can the request be sent to? This depends on the size of the mould. A larger mold requires a larger machine, which not every supplier has at their disposal. For that reason the production planning decides which supplier group is requested. With that input and some further machine requirements the purchasing department is able to create a request and send it out to the mentioned supplier group. Once the purchasing department has received the offer, the production planning checks the different offers and does a preselection. With the remaining suppliers the purchaising department will negociate the price and send back the final price to the planning department. The situation is now different. Production planning knows the concrete costs and can now decide once again whether outsourcing is still attractive or if they rather accept delays in delivery and produce interally. Regarding the high quality expectation of the company, it is very likely that they outsource the production in order to meet the delivery time. In this case, the purchasing department sends tasks to all parties involved (material planners, mold makers, logistics) to inform them about the materials that are to be provided to the supplier. 
As it is difficult to the purchasing department to know, how long the preparation lasts, the order with the pick-up date of the provided materials is d
sent when the purchaser receives the feedback of the production. With the sent order the process "request to order" ends. 
## As-Is Process
![alt text](https://github.com/DigiBP/Hoselupf/blob/master/as_is_process.png?raw=true "as is process")

## Identification of digitalization potential
The following digitalization potential has been identified:
- The process is to be started by an automatic inbound API call. The information about article, date, stock, order level, tools and sales order.
- Implementation of service task where an outbound API call is triggered with the information about the production date target, and if capacity is availabe. This is the basis for the decision table
- A decision table should be implemented where a preselection of the manufactoring company can be done automatically based on the machine weight
- The result should be filled automatically in a HTML form to double check

## Workflow modelling with user tasks, decision tasks and service Tasks

## Selection of iSaaS

## Process Modelling with 

## Service Integration with iSaaS

## Process Modelling with Error Handling

## Conclusion & Next Steps

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
