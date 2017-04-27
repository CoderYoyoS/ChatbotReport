## API.ai

An agent is first created using the API.ai web interface named itbchatbot. A client access token and a developer access token are provided (Figure 6.1). The client access token was then stored to the ```config.js``` file to allow access to the remote API.ai agent from the chat bot server application. This agent is then used by the main application for the natural language processing and intent driving. Machine learning (ML) settings are then set to enforce iterative learn from data being sent to agent. Two options are given by the service: hybrid (Rule based and ML) or just start ML. In this case, hybrid machine learning was implemented as it. In addition, a ML classification threshold is set to 0.2.  The agent will only trigger an intent if it's confidence percentage is above this threshold value, otherwise a fallback intent will be invoked.

![API.ai Agent Interface. \label{API.ai agent}](04_assets/05_implementation/agent_screenshot.jpg)

Intents were created using the API.ai web interface for conversational driving. For each service that the application provides (bus, gym and library) the intents are named fittingly. The follow-up intents are named after the service name followed by the operation it performs. This is shown in the figure 6.2 as an example. As seen below, the base intent ```gym``` is the initial starting point for the gym conversation and is then followed by a number of other intents that can be triggered depending on how the user directs the conversation.

![Intent Structure. \label{API.ai intent}](04_assets/05_implementation/intents_structure.jpg)

To create a base intent, the first step is to set the output context of the follow-up intents. This context is given a default life span of five but can be changed depending on approximately how long the discussion should last. This output context is used to link intents with one another and decrement the lifespan value each time a follow up intent is triggered. Secondly, expected user input is given by the developer. This involved entering several sentences that have the same meaning. The more expected sentences entered the more likely that the API.ai agent will be able to accurately extract semantics from user text. As shown in figure 6.3, it can be seen how a number of sentences entered with a similar meaning. The NLP algorithms extract the meaning from these sentences and can understand a text that doesn't match any of the sentences already specified.

![Dublin Bus Intent. \label{API.ai agent}](04_assets/05_implementation/bus_intent.jpg)

Responses were created for when a certain intent has been triggered. API.ai provides a number of templates to use including a text response, images, card template, quick replies and custom payloads to apply payload values to be returned when the user interacts with a content type. The following JSON snippet illustrates a custom payload used to design a generic template in addition with a number of quick reply buttons.

```
{
  "facebook": {
    "attachment": {
      "type": "template",
      "payload": {
        "template_type": "generic",
        "elements": [
          {
            "title": "Which bus do you want?",
            "image_url": <IMAGE_URL>
          }
        ]
      }
    },
    "quick_replies": [
      {
        "content_type": "text",
        "title": "All Routes",
        "payload": "All Routes"
      },
      {
        "content_type": "text",
        "title": "220 to Ballymun",
        "payload": "220 Towards Ballymun"
      }

        . . .

    ]
  }
}
```

Entities are then made for extracting values from payload responses from the users interaction with the bot. Entities have been made for acquiring the bus areas, such as Blanchardstown Shopping Centre and Corduff,  and days for the gym classes. These entity attributes are sent back the main server application to pass these values to the external service APIs. Figure 6.4 shows the ```gym-days``` entity the attributes to be extracted from the users response.

![Gym Entity. \label{entityt}](04_assets/05_implementation/gym_entity.jpg)

When the user reaches the end of a conversation flow, no response from API.ai is set and an action must be implemented to be caught by the ```handleApiAiAction``` function in the main application code. This action indicates that a conversation has reach the end point and now must retrieve data from one of the three APIs. An action encompasses extracting a value from the users response and assigning a parameter name. This parameter name which is given an entity value, and the action name is sent back to the chat bot application.For instance, in an ending intent of a Dublin bus conversation, the action with the value of ```blanch-centre-side-route-picked``` is set. Additionally, the bus route picked is assigned to the ```bus_id``` parameter and no response is created (Figure 6.5).

![Dublin Bus Action. \label{entityt}](04_assets/05_implementation/bus_action.jpg)


