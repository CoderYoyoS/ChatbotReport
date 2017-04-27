## Training API.ai

Training the API.ai agent is an important vital component of ensuring that the right intents are triggered when interacting with the chat bot. API.ai provides a testing console for developers to perform test conversations with the agent on a web interface. The console displays the sentence entered by the tester, the response that the agent returns, the intent that was triggered based on the semantics extracted from the input, a context of the conversation that has been instigated, the appropriate action set in the intent triggered (if any) and the parameters name of the value extracted from the user input (See figure 7.1) .

![API.ai Testing Console. \label{API.ai Console}](04_assets/06_testing_evaluation/api_console.jpg)

As shown above, the correct response was given back to the user. No intents or contexts appear as the there is no follow up intents to succeed the response given. Also, a ```smalltalk.greeting``` action is from the SmallTalk domain that API.ai provides. 
In the case of a conversational flow, more attributes from developer defined intents will be shown. As seen in figure 7.2, in the case of a user going through a bus conversation, the ```dublin-bus``` and ```picked-blanch-side``` contexts can be seen. This allows for developers to verify that the right intent is triggered when an input is given to the agent.

![API.ai Testing Console 2. \label{API.ai Console}](04_assets/06_testing_evaluation/contexts.jpg)

In the case that the wrong intent is triggered from a certain input, API.ai provides a machine training tool that keeps a history of the tested inputs. When wrong intents were triggered, the training tool was utilised to ensure that the machine learning algorithms of API.ai can remember what intent to trigger should a similar input be given again. As shown in figure 7.3, a test input with the sentence "can i have gym closing times?" was entered. As there was no classification of what this sentence meant, there was no intent to trigger. Therefore, the ```gym-opening-times``` was assigned using the training tool. Through machine learning, the agent can remember this input and can handle a similar sentence should it be received again.

![API.ai Training Tool. \label{API.ai Console}](04_assets/06_testing_evaluation/training.jpg)
