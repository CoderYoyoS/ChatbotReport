## APi.ai

An agent is first created using the API.ai web interface named itbchatbot. A client access token and a developer access token are provided (Figure 6.1). The client access token was then stored to the ```config.js``` file to allow access to the remote API.ai agent from the chat bot server application. This agent is then used by the main application for the natural language processing and intent driving. Machine learning (ML) settings are then set to enforce iterative learn from data being sent to agent. Two options are given by the service: hyrbrid (Rule based and ML) or just start ML. In this case, hybrid machine learning was implemented as it. In addition, a ML classification treshhold is set to 0.2.  The agent will only trigger an intent if it's confidence percentage is above this treshold value, otherwise a fallback intent will be invoked.

![API.ai Agent Interface. \label{API.ai agent}](04_assets/05_implementation/agent_screenshot.jpg)

Intents were created using the API.ai web interface for conversational driving. For each service that the application provides (bus, gym and library) the intents are named fittingly. The follow- up intents that are utilised after these use the service name followed by the operation it performs. This shown in the figure 6.2 using the gym intent as an example. As seen below, the base intent ```gym``` is the initial starting point for the gym conversation and is then followed by a number or other intents depending on how the user directs the conversation.

![Intent Structure. \label{API.ai intent}](04_assets/05_implementation/intent_structure.jpg)

To create a base intent, the first step is to set the output context of the follow-up intents. This context is given a default life span of five but can be changed depending on approximately how long the discussion should last. This output context is used to link intents with one another and decrement the lifespan value each time and follow up intent is triggered. Secondly, expected user input is given by the developer. This involved entering a number of sentences that have the same meaning. The more expected sentences entered the more likely that the API.ai agent will be able to accurately extract semantics from user text. As shown in figure 6.3, it can be seen how a number of sentences entered with a similar meaning. The NLP algorithms extract the meaning from these sentences and can understand a text that doesn't match any of the sentences already specified.

![API.ai Agent Interface. \label{API.ai agent}](04_assets/05_implementation/bus_intent.jpg)

Responses were created for when a certain intent has been triggered.