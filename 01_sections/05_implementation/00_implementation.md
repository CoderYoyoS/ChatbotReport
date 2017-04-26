# Implementation

The following section will illustrate the implementation of the applications utilised within the project based on the system requirements and specifications and the system design that has been aforementioned. Firstly, a detailed description of the of primary chatbot server application will be provided followed by the implementation of the individual API's. Additionally, it will be explained how the separate applications communicate with one another in accordance to functionality and purpose. Code snippets shall also be given to demonstrate the algorithmic logic and connectivity.

## Chatbot Application

The structure of the main application entails a configuration file to set the group work for the entire application. This file contains the configuration variables that are utilised across the main index file to gain access to the additional tools and services mentioned in the specification. Page access tokens provided by the Facebook messenger platform in along with the developer defines verify token and application are used to authenticate access to the Messenger Send API. In addition to this, a verify token for API.ai is also stored to authenticate communication between the chatbot application and bot engine. Lastly, a ```SERVER_URL``` is provided to give a short-hand to accessing the main application host address.

```
    module.exports = {
        FB_PAGE_TOKEN: '<TOKEN>.',
        FB_VERIFY_TOKEN: 'secret',
        FB_APP_SECRET: '<SECRET>',
        API_AI_CLIENT_ACCESS_TOKEN: '<TOKEN>',
        SERVER_URL: "https://itbchatbot.herokuapp.com/",
    };
```

Ensuing the preliminary basis to the application follows the Node.JS modules that have been utilised within the project. These decencies injections aids in the implementation of the functionalities and provide additional services to the application. Initially, the ```apiai``` module is installed to allow the application to communicate with the API.ai natural language processing service. The ```body-parser``` module is used as middleware to parse then response bodies. ```Crypto``` is used to verify the secret located in the header that has been sent from the Facebook. The ```express``` module is a framework for Node.js that provides Javascript to be executing without the aid of a web browser. In order to make HTTP calls, the ```request``` module is used and lastly, the ```uuid``` module is utilised to generate a session ID.

The main server Javascript file named ```index.js``` is the backbone of the chat bot application. This file contains all the logic for sending and receiving messages, as well as dealing with responses and and JSON templates used in order to drive conversations.
Initially, the basic configurations for the server are set, this involves providing a port for the application to run on. This is allocated dynamically by Heroku.  A connection to the API.ai engine is then established.

```
    //Set the port of the app
    app.set('port', (process.env.PORT || 5000))

    const apiAiService = apiai(config.API_AI_CLIENT_ACCESS_TOKEN, {
        language: "en", requestSource: "fb" 
    });
```

A webhook route is set to receive notifications from the Facebook Messenger platform. This connection was achieved by inserting the webhook URL to the developer settings on Facebook. The following function authenticates the application and verifies access. In addition to authentication, condition handling was implemented in order to appropriately deal with messages being sent by the user. This higher level message events delineate the type of messages being sent. For example, the message could be a regular message, delivery confirmation, read receipt or postback etc.

```
    app.get('/webhook/', function (req, res) {
        if (req.query['hub.mode'] === 'subscribe' &&   
            req.query['hub.verify_token'] === config.FB_VERIFY_TOKEN) {
            res.status(200).send(req.query['hub.challenge']);
        } else {
            console.error("Verification was not valid.");
            res.sendStatus(403);
        }
    });

```

```
data.entry.forEach(function (entry) {

			// Iterate over each messaging event and handle accordingly
			entry.messaging.forEach(function (messagingEvent) {
				if(messagingEvent.message) {
					receivedMessage(messagingEvent);
				} 
				else if (messagingEvent.delivery) {
					receivedDeliveryConfirmation(messagingEvent);
				} 
				else if (messagingEvent.postback) {
					receivedPostback(messagingEvent);
				} 
				else {
					console.log("Unknown event type ..")
				}
			});
		});
```

To provide a easy means of understanding the the functions in the source code, a three type naming convention was used. This approach was used to aid in  comprehending how the code worked and what it dealt with. These three types of functions where used to handle communication between three endpoints. Functions beginning with the word "receive" are used to carry out operations when a request has been sent to the server webhook URL. The functions that use the beginning word "handle" are functions used to manage payloads such as templates. Lastly, functions using the name "send" are used to administer data to other end points via the chat bot application. The following sections describes how these functions were implemented in addition with the most importantly used functions as examples.

### Receive functions

The primary function used in a regular use case is the ```receivedMessage``` function.  This takes in the message event as a parameter and then uses the data to initalise variable values to be used in the code. The most mos prominently used of these variables is the ```senderID``` with is needed to reply to the user who has interacted with the chat bot. This ID is unique for each Facebook user and is generously passed in the code

```
function receivedMessage(event) {

	//Set variables from json
	var senderID = event.sender.id;
	var recipientID = event.recipient.id;
	var timeOfMessage = event.timestamp;
	var message = event.message;

	if (!sessionIds.has(senderID)) {
		sessionIds.set(senderID, uuid.v1());
	}

	//Set variables
	var isEcho = message.is_echo;
	var messageId = message.mid;
	var appId = message.app_id;
	var metadata = message.metadata;

	// You may get a text or attachment but not both
	var messageText = message.text;
	var messageAttachments = message.attachments;
	var quickReply = message.quick_reply;

	//check type of message
	if (isEcho) {
		handleEcho(messageId, appId, metadata);
		return;
	} else if (quickReply) {
		handleQuickReply(senderID, quickReply, messageId);
		return;
	}
	//Check if it's a text message
	if (messageText) {
		//send message to api.ai
		sendToApiAi(senderID, messageText);
	} else if (messageAttachments) {
		handleMessageAttachments(messageAttachments, senderID);
	}
}
```
The ```recievedPostback``` block is a significantly important function used to handle postback sent back from the user when a button or a quick reply is clicked. The postback is defined within the message event and is dealt with accordingly using a switch statement. When the condition is met, the suitable method is then called depending on what postback has been received. The following example illustrates how the chat bot would deal with a postback when a user initiates a conversation for the first time by clicking the ```Get Started``` button.

```
    function receivedPostback(event) {

	//Set variables
	var senderID = event.sender.id;
	var recipientID = event.recipient.id;
	var timeOfPostback = event.timestamp;

	// The 'payload' param is a developer-defined field which is set in a postback 
	// button for Structured Messages. 
	var payload = event.postback.payload;

	switch (payload) {

		//messages sent if get started button is clicked
		case 'GET_STARTED' :

                var messageData = {
                recipient: { 
                    id: senderID
                },
                message: {
                    text:"Hi, I am the ITB Chatbot",
                    quick_replies:[
                        {
                            content_type :"text",
                            title : "What can you do?",
                            payload : "What can you do?"
                        },
                        {
							content_type :"text",
							title : "Who made you?",
							payload : "Who made you?"
						}
					]
				}
			};
			callSendAPI(messageData);
			break;

            . . .
```

### Handle Functions

Handle functions are primarily used when dealing with responses by the API.ai bot engine. These responses are in a JSON format and require specific algorithmic logic depending on the payload received. One of the highest level of the handle functions is the ```handleMessage```, which checks the message type value passed as a parameter. This is then used to delegate which function should next be executed appropriately. These message types are defined by API.ai.  

```
function handleMessage(message, sender) {
	switch (message.type) {

		//If it is text
		case 0: 
			sendTextMessage(sender, message.speech);
			break;
		
		//if it a quick reply
		case 2: 
			let replies = [];
			for (var i = 0; i < message.replies.length; i++) {
				let reply =  {
					"content_type": "text",
					"title": message.replies[i],
					"payload": message.replies[i]
				}
				replies.push(reply);
			}
			//Send a quick Reply 
			sendQuickReply(sender, message.title, replies);
			break;

		//Handle Custom payloads
		case 4:
			var messageData = {
				recipient: {
					id: sender
				},
				message: message.payload.facebook
			};
			callSendAPI(messageData);
			break;
	}
}
```

Second to previous function is the ```handleApiAiAction``` function, this allows for conidtion checking to see if an "Action" (as mentioned in the system requirements and specifications) was sent by by API.ai. When a action is caught, the appropriate method is taken. In this case, functions to call external API's are invoked (whichn will be touched upon in a later section).

```
function handleApiAiAction(sender, action, responseText, contexts, parameters) {

	switch (action) {

		/*************** Dublin Bus Actions ******************/

		//Corduff bus stop
		case "corduff-route-picked" :
					var busNum = contexts[0].parameters.bus_id;
					getDublinBusTimes(sender,"1835", busNum);
			break;
		
                     . . . 

		/*************** Library Actions ********************/

		case 'library-pin-not-enrolled' :
				getLibraryInfo(sender, action);
		
                    . . . 
		
		/***************** Gym Actions **********************/

		case "gym-class-times-days-picked" :
				var dayPicked = contexts[0].parameters.gym_days;
				getGymInfo(sender, action, dayPicked);
			break;

                 . . . 
		/****************************************************/
		default:
			//unhandled action, just send back the text
			sendTextMessage(sender, responseText);
	}
}
```