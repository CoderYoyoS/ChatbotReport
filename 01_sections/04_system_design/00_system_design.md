# System Design

The design of the system incorperates multiple technologies and platforms to meet the requirements of the application. As specified, the application must possess a means to commission data across a number of components. Many factors have been taken into consideration during the planning process of this project and the following section describes in detail how the technologies utilised interact with one another.

## Decentralised applications 
The approach of decentralised computing was applied using application program interfaces to provide information. These peripheral applications are autonomous to the central chat bot application and are hosted independently from the main application instance. Each API developed provides a specific type of information and runs on their own individual server hosted on Heroku. This proves highly beneficial in a case that if one application fails, it does not distrupt the rest of the system. Three applications have been implemented to provide information in correlation with the main chat bot application. Each of these administer data about gym information, library information and real time bus information.
Firstly, the bus API involves extracting parameterised values sent from the primary application. These values are then used to send a request to the Public transport predictions and schedules RTPI REST Web Services API and, in turn, composes the response data to a human-like response. The bus API then sends the data back in a JSON format to the central application. Suitably, no database is intergrated within this API as it uses dynamic data that is retrieved by the RTPI RESTful API.
In contrast, the gym and library API's use routing to specify the type of data needed. Each route is used to extract data from a NoSQL MonogoDB database hosted on mLab. These are static JSON documents that store information specific to the route specified in the API request from the main app.

![API Topology. \label{Design structure of API's}](04_assets/04_system_design/api_layout.jpg)

## GitHub and Heroku Intergration
In order keep track of code being written during the process of implementation, GitHub repositories and Heroku applications  were created for each of the API's and the central application. These repositories were configured to intergrate with the Heroku applications. Once a commit has been pushed from a team member's local machine to the remote respository respectively, the code is automatically deployed to the Heroku server. A build is then commisioned by Heroku, and should the build succeed, the application is released and hosted on the server. For the following intergrations, the Aaron_api (bus) repository was assimilated with the aaronapi application, the Brian_api (gym) repository with the brianapi application and lastly, the Daire_api (library) repository with the daireapi application. The main central application is intergrated using the Chatbot_Assignment repository in corresspondence with the itbchatbot application.

## API.ai

