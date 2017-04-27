## APi.ai

An agent is first created using the API.ai interface named itbchatbot. A client access token and a developer access token are provided (Figure ). The client access token was then stored to the ```config.js``` file to allow access to the remote API.ai agent from the chat bot server application. This agent is then used by the main application for the natural language processing and intent driving. Machine learning (ML) settings are then set to enforce iterative learn from data being sent to agent. Two options are given by the service: hyrbrid (Rule based and ML) or just start ML. In this case, hybrid machine learning was implemented as it. In addition, a ML classification treshhold is set to 0.2.  The agent will only trigger an intent if it's confidence percentage is above this treshold value, otherwise a fallback intent will be invoked.

![API.ai Agent Interface. \label{Design structure of API's}](04_assets/05_implementation/agent_screenshot.jpg)

