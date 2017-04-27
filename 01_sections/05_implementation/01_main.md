## Chatbot Application

The structure of the main application entails a configuration file to set the ground work for the entire application. This file contains the configuration variables that are utilised across the main index file to gain access to the additional tools and services mentioned in the specification. Page access tokens provided by the Facebook messenger platform, developer defined verify token and application secret are used to authenticate access to the Messenger Send API. In addition to this, a verify token for API.ai is also stored to authenticate communication between the chat bot application and bot engine. Lastly, a ```SERVER_URL``` is provided to give a short-hand to accessing the main application host address.

```
    module.exports = {
        FB_PAGE_TOKEN: '<TOKEN>.',
        FB_VERIFY_TOKEN: 'secret',
        FB_APP_SECRET: '<SECRET>',
        API_AI_CLIENT_ACCESS_TOKEN: '<TOKEN>',
        SERVER_URL: "https://itbchatbot.herokuapp.com/",
    };
```

Ensuing the preliminary basis to the application follows the Node.JS modules that have been utilised within the project. These dependency injections aid in the implementation of the functionalities and provide additional services to the application. Initially, the ```apiai``` module is installed to allow the application to communicate with the API.ai natural language processing service. The ```body-parser``` module is used as middleware to parse then response bodies. ```Crypto``` is used to verify the secret located in the request header that has been sent from the Facebook. The ```express``` module is a framework for Node.js that provides JavaScript to be executed without the aid of a web browser. In order to make HTTP calls, the ```request``` module is used and lastly, the ```uuid``` module is utilised to generate a session ID.

The main server JavaScript file named ```index.js``` is the backbone of the chat bot application. This file contains all the logic for sending and receiving messages, as well as dealing with responses and JSON templates used to drive conversations.
Initially, the basic configurations for the server are set, this involves providing a port for the application to run on. This is allocated dynamically by Heroku. Also, a connection to the API.ai engine was then established.

```
    //Set the port of the app
    app.set('port', (process.env.PORT || 5000))

    const apiAiService = apiai(config.API_AI_CLIENT_ACCESS_TOKEN, {
        language: "en", requestSource: "fb" 
    });
```

A webhook route is set to receive notifications from the Facebook Messenger platform. This connection was achieved by inserting the webhook URL to the developer settings on Facebook. The following function authenticates the application and verifies access. In addition to authentication, condition handling was implemented in order to appropriately deal with messages being sent by the user. This higher-level message events delineate the type of messages being sent. For example, the message could be a regular text message, delivery confirmation, read receipt or postback etc.

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

To provide a easy means of understanding the functions in the source code, a three type naming convention was used. This approach was used to aid in comprehending how the code worked and what it dealt with. These three types of functions where used to handle communication between three end points. Functions beginning with the word "receive" are used to carry out operations when a request has been sent to the server webhook URL from Facebook. The functions that use the beginning word "handle" are functions used to manage payloads such as templates. Lastly, functions using the name "send" are used to administer data to other end points via the chat bot application. The following sections describes how these functions were implemented in addition with the most importantly used functions as examples.

### Receive functions

The primary function used in a regular use case is the ```receivedMessage``` function.  This takes in the message event as a parameter and then uses the data to initialise variable values to be used in the code. The most prominently used of these variables is the ```senderID``` with is needed to reply to the user who has interacted with the chat bot. This ID is unique for each Facebook user and is generously passed to other functions within the code.

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
The ```recievedPostback``` block is a significantly important function used to handle postbacks sent back from the user when a button or a quick reply is clicked. The postback is defined within the message event and is dealt with accordingly using a switch statement. When the condition is met, the suitable method is then called depending on what postback has been received. The following example illustrates how the chat bot would deal with a postback when a user initiates a conversation for the first time by clicking the ```Get Started``` button.

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

Second to the previous function is the ```handleApiAiAction``` function. Only if an action is defined then this function will be called. When a action is caught in the switch statement, the appropriate method is taken. In this case, functions to call external API's are invoked (which in will be touched upon in a later section).

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

### Send Functions

Send functions are used to send certain type of data to a particular end point and called when a certain content type is sent back from API.ai. These contain JSON templates to dispatch different response types ranging from images, button messages, quick replies, quick replies to other additional features likes sending read receipts or typing bubbles which was used to simulate human like characteristic to the chat. The most dominantly used of these functions is the ```sendTextMessage``` function, which provides JSON to reply with just a string of text. The function takes in the users unique ID and the text that was given back from API.ai and adds it to a JSON object accordingly. The ```callSendAPI``` function is then invoked, passing the JSON object.

```
function sendTextMessage(recipientId, text) {
	var messageData = {
		recipient: {
			id: recipientId
		},
		message: {
			text: text
		}
	}
	callSendAPI(messageData);
}
```

Another example of a send function would be ```sendToApiAi```. Initially the sender ID and the message is passed. The ```sendTypingOn``` function is invoked to display a typing bubble on the interacting user's Messenger interface. A request is then sent to API.ai containing the text and the session ID assigned to that user. It then waits for a response from API.ai and sends the received data to the ```handleApiAiResponse``` function. 

```
function sendToApiAi(sender, text) {

	//sends the typing bubble to the sender until 
	//a response is given
	sendTypingOn(sender);
	
	//Send message to API.ai
	let apiaiRequest = apiAiService.textRequest(text, {
		sessionId: sessionIds.get(sender)
	});

	//Wait for response to API.ai
	apiaiRequest.on('response', (response) => {
		if (isDefined(response.result)) {
			handleApiAiResponse(sender, response);
		}
	});

	apiaiRequest.on('error', (error) => console.error(error));
	apiaiRequest.end();
}
```

### API Call Functions

For each of the API's that have been developed, a function was made which is invoked from  ```handleApiAiAction```. A request is made using the ```options``` JSON object which contains the API server URL, applicable parameters and the HTTP method. When the request is made a callback function takes the response body and applies it to the ```messageData``` object, along with additional quick reply buttons to drive conversational flow. The ```callSendApi``` is then invoked to send the message back to the user. The example below displays a function that uses the bus API  that has been developed.

```
function getDublinBusTimes(recipientId, stopId, busNum){

	var options = {
		url: "https://aaronapi.herokuapp.com/bus/" + stopId + "/" + busNum + "/", 
		method : "GET"
	}
	//Make a request to the API
	request(options, function(error, res, body){

			var text = res.body;
			var messageData = {
				recipient: { 
					id: recipientId
				},
				message: {
					text: res.body,
					quick_replies:[
						{
							content_type :"text",
							title : "Pick another Bus?",
							payload : "Dublin Bus"
						},
						{
							content_type :"text",
							title : "Main Menu ",
							payload : "Main menu"
						},
						{
							content_type :"text",
							title : "No thanks",
							payload : "No thanks"
						}
					]
				}
			}
			callSendAPI(messageData);
	});
}
```

Lastly, the ```callSendApi``` function is the main function that connects the applicationn with the Facebook interface. It makes a request to the Facebook Graph API (See Appendix 1) using the page access token stored within ```config.js``` file. A post is made using the ```messageData``` JSON object which possesses the senders ID and the message to be sent. Additionally. 

```
function callSendAPI(messageData) {
	request({
		uri: 'https://graph.facebook.com/v2.6/me/messages',
		qs: {
			access_token: config.FB_PAGE_TOKEN
		},
		method: 'POST',
		json: messageData
	}, function (error, response, body) {
		if(error){
			console.log(error);
		}else{
			console.log("Message Sent successfully");
		}
		
	});
}
```

In conclusion, the main server application consists of a range of functions to handle and distribute data between external entities. It also incorporated intuitive naming for a better understanding of the code. For further code listing, see the repository link in Appendix A.
