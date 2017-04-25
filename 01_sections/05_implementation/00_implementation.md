# Implementation

The following section will illustrate the implementation of the applications utilised within the project based on the system requirements and specifications and the system design that has been afformentioned. Firstly, a detailed description of the of primary chatbot server application will be provided followed by the implementation of the individual API's. Additionally, it will be explained how the seperate applications communicate with one another in accordance to functionality and purpose. Code snippets shall also be given to demonstrate the algorithmic logic and connectivity.

## Chatbot Application

The structure of the main application entails a configuration file to set the groupwork for the entire application. This file contains the configuration variables that are utilised accross the main index file to gain access to the additional tools and services mentioned in the specification. Page access tokens provided by the Facebook messenger platform in along with the developer defines verify token and application are used to authenticate access to the Messenger Send API. In addition to this, a verify token for API.ai is also stored to authenticate communication between the chatbot application and bot engine. Lastly, a ```SERVER_URL``` is provided to give a short-hand to accessing the main application host address.

```
    module.exports = {
        FB_PAGE_TOKEN: 'EAAZA70jtCZAh4BAKdB2uUEaubMa ...',
        FB_VERIFY_TOKEN: 'secret',
        FB_APP_SECRET: '8e3c62def3af61939a9c4ba4628517f3',
        API_AI_CLIENT_ACCESS_TOKEN: '5c09189e70614b58a8ad579c364bbf3b',
        SERVER_URL: "https://itbchatbot.herokuapp.com/",
    };
```

Ensuing the preliminary basis to the application follows the Node.JS modules that have been utilised within the project. These depencies injections aids in the implmentation of the functionalities and provide additional services to the application. Initially, the ```apiai``` module is installed to allow the application to communicate with the API.ai natural language processing service. The ```body-parser``` module is used as middleware to parse then resposne bodies. ```Crypto``` is used to verify the secret located in the header that has been sent from the Facebook. The ```express``` module is a framework for Node.js that provides Javascript to be executing without the aid of a web browser. In order to make HTTP calls, the ```request``` module is used and lastly, the ```uuid``` module is utilised to generate a session ID.

The main server Javascript file named ```index.js``` is the backbone of the chat bot application. This file contains all the logic for sending and receiving messages, as well as dealing with responses and and JSON templates used in order to drive conversations.
Initially, the basic configurations for the server are set, this involves providing a port for the application to run on. This is allocated dynamically by Heroku.  A connection to the API.ai engine is then established.

```
    //Set the port of the app
    app.set('port', (process.env.PORT || 5000))

    const apiAiService = apiai(config.API_AI_CLIENT_ACCESS_TOKEN, {
        language: "en", requestSource: "fb" 
    });
```

A webhook route is set to receive notifications from the Facebook Messenger platform. This connection was achieved by inserting the webhook URL to the developer settings on Facebook. The following function authenticates the application and verifies access.

```
    app.get('/webhook/', function (req, res) {
        if (req.query['hub.mode'] === 'subscribe' &&   
            req.query['hub.verify_token'] === config.FB_VERIFY_TOKEN) {
            res.status(200).send(req.query['hub.challenge']);
        } else {
            console.error("Verification was not valid.");
            res.sendStatus(403);
        }
    })

```