##Concluding Analysis of System Requirements and Specification

This section will give a summarisation of the technology stack chosen to implement this project. In lieu of the current methods being adopted to build chatbot applications several tooling considerations had to be made and as a result the appropriate tools for this project where chosen. 

(@)Heroku was chosen as the PaaS provider because of its ease-of-integration with GitHub and its flexible free tier. It provides 550 hours of up-time. This tier also allows for the app to 'sleep' when not being pinged with HTTP requests, therefore, not eroding the up-time quota of hours when the app is not is in an in-active state.

(@)Facebook Messenger was chosen because the availability of resources and documentation regarding integrating with their platform was extensive and well-documented. Facebook messenger is also language agnostic and this provided a freedom for the development team to use whatever server side language they were most comfortable/experience with.

(@)Api.ai was chosen as the NLP engine to use for parsing input that the server receives and returning the intent

(@)Node JS was chosen as the server-side programming language for several reasons. Perhaps the most notable factor is that the majority of literature and tutorials on designing and developing chatbots are all implemented with Node JS. Nodes single threaded execution model also lends itself to messaging applications because of its rigid enforcement of the ordering of requests. Node, built on top of the Google Chrome V8 Javascript engine and implemented in C and C++, provides fast execution times which is essential for a chat-based server.







