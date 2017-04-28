##Concluding Analysis of System Requirements and Specification

This section will give a summarisation of the technology stack chosen to implement this project. In lieu of the current methods being adopted to build chatbot applications several tooling considerations had to be made and as a result the appropriate tools for this project where chosen. 

(1) Heroku was chosen as the PaaS provider because of its ease-of-integration with GitHub and its flexible free tier. It provides 550 hours of up-time. This tier also allows for the app to 'sleep' when not being pinged with HTTP requests, therefore, not eroding the up-time quota of hours when the app is not is in an in-active state.

(2) Facebook Messenger was chosen because the availability of resources and documentation regarding integrating with their platform was extensive and well-documented. Facebook messenger is also language agnostic and this provided a freedom for the development team to use whatever server side language they were most comfortable/experience with.

(3) Api.ai was chosen as the NLP engine to use for parsing input that the server receives and returning the intent

(4) Node JS was chosen as the server-side programming language for several reasons. Perhaps the most notable factor is that the majority of literature and tutorials on designing and developing chatbots are all implemented with Node JS. Nodes single threaded execution model also lends itself to messaging applications because of its rigid enforcement of the ordering of requests. Node, built on top of the Google Chrome V8 Javascript engine and implemented in C and C++, provides fast execution times which is essential for a chat-based server.

(5) MongoDB was chosen as the primary data model for the application. This is because of its ability to shard easily and evolve in-line with the data model changing. It is difficult to preempt exactly the data format during development because naturally the database will have to expand whilst the application is being used by end-users. There is also a lot of tutorials on developing chatbots with the MongoDB database client and this was also a contributing factor to its selection.


(6) Api.ai was chosen as the NLP engine for this application because its accessibility to developers and its powerful integration capabilities with the big messaging platforms. 









