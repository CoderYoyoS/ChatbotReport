##Application Architecture
 
This section will present the architecture of the system in a logical view that will clearly outline each node/participant in the application. The application relies on each participant to fulfil a certain use case that contributes to the overall successful processing of an input. The main participants are:

1. The developers
2. The Version Control System/GitHub
3. The instant messaging platform/Facebook
4. The PaaS Provider/Heroku
5. The NLP engine/Api.ai
6. The database server/MLab

Whilst the version control system may be deemed an inconsequential part of the overall system architecture, because of Heroku's dependency to deploy directly from GitHub repositories, it is seen as an important participant, thus, it can be deemed as an appropriate inclusion to the proceeding system architecture diagram. Also, the inclusion of the developers in the system architecture is because of a consistent need of monitoring the applications performance via logging and configuration updates that may need to be run on the applications dashboard interface. See fig(whatever) for a logical view of the system architecture.

![High Level Architecture. \label{High Level Architecture}](04_assets/04_system_design/highlevelarch.png)

