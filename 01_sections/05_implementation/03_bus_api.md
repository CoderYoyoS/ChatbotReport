## Application Program Interfaces

As previously touched upon in the system design, three application program interfaces were developed in order to provide additional information.  Each API was hosted on Heroku and run entirely autonomously from the main application. The following section provides a detailed explaination of the implementation of bus, gym and library APIs and shall be aided with code snippets to further exemplify their functionalities and capabilities.

### Bus API

The initial dependencies are imported to the ```server.js``` file. These include the ```express```, ```request``` and the ```body-parser``` node modules. Port configurations are declared to allow the API to run on Heroku with a dynamically allocated port number.

```
    var express = require('express');
    var bodyParser = require('body-parser');
    var request = require('request');

    var app = express();

        . . .

    app.set('port', (process.env.PORT || 5000))

    app.get('/', function (req, res) {
        res.send('This is the landing page for the dublin bus API for the chatbot...')
    })

    app.listen(app.get('port'), function () {
        console.log('Bus API server is running on port ', app.get('port'))
    })
```

Asformentioned in implementation of the chat bot application server code, the call to the bus API sends two parameters. The bus number parameter is extracted from API.ai and sent to the ```handleApiAiAction```. Also, depending on what action is caught, the bus stop ID of the corresponding bus area is also passed and added as a parameter to the URL.
```
	var options = {
		url: "https://aaronapi.herokuapp.com/bus/" + stopId + "/" + busNum + "/", 
		method : "GET"
	}
```
The API uses these values and stored them to a JSON object to be utilised within the program. Should the value of the bus ID be set to "All" then a Boolean value is set to true. This is used to get all the estimated bus times for all routes at that bus stop. Additionally, a check for the 39 or 39A bus is put in place, as there is two bus stops with separate stop IDs at the Blanchardstown Centre side bus stop. This will change the value of ```stopId``` accordingly.

```
app.get('/bus/:stop_id/:bus_num', function(req, res) {

        /**
         * Assign parameters passed in URL to 
         * a JSON object
         */
        var data = {    
            "bus": {
                "stop_id": req.params.stop_id,
                "bus_num": req.params.bus_num
            }
        };

        var message = "";

        //All routes button picked by user
        var all = false;

        /** 
         * Assign stop id and bus number to the values
         * passed by chat bot
         */
        var stopId = data.bus.stop_id;
        var busNumber = data.bus.bus_num;

        if(busNumber == "All"){
            all = true;
        }

        //Check because 39a and 39 are at a different stop
        if(stopId == "7026"){
            if(busNumber == "39"|| busNumber == "39A"){
                stopId = "7025";
            }
        }
```
A JSON object named ```options``` is then initialised with the URL for the RTPI REST Web Services API, the HTTP GET method and strictSSL set to false to allow responses from an uncertified source. Furthermore, a request is made and an error checking is present to log any errors should they occur. If there is no error, the body of the response is parsed to a JSON format. When the ```numberofresults``` parameter has a value of 0, this usually indicates that the bus services are not in service because of the time of day. The response message is given a value telling the user that it is too late to check the times for the bus. 


```
        var options = {
            url: 'https://data.dublinked.ie/cgi-bin/rtpi/realtimebusinformation?stopid=' + stopId + '&format=json',
            method : 'GET',
            strictSSL: false
        }; 

        /**
         * Make a request to for Dublin bus API and repond to chatbot
         */
        request(options, function(error, response, body) {
        if(error){
            console.log(error);
        }
        else{
            
            body = JSON.parse(body);
            //numberofresults will return as 0 if it past half 11
            if(body.numberofresults === 0){
                message = "It's too late for busses ";
            }
```

Should there be a number of results, the results set is traversed.  A guard to check if the ```route``` is the same as the one passed from the main application code or if the ```All``` variable is set to true. This is to ensure that only the bus route specified or all the bus routes are extracted. The value of the bus route and the amount of minutes until it is due is concatenated to a message string over each loop of iteration. Additional checks are put in place to verify that the message being sent back makes sense. For instance, should the ```duetime``` value be equal to one, then the message is changed from "due in X minutes" to "due in 1 minute".
Additionally, when the loop of the result set has finished executing, a counter for the results are checked. During each iteration, this value was incremented. If the value has a value of zero prior to the loop, this shows that there is no bus due at the bus stop specified. Lastly, the resulting message is then send back to the main application and dealt with accordingly.

```
    else{
        var resultCount = 0;
        //Display all the bus routes and due times available
        for( var i in body.results){
            if(body.results[i].route == busNumber || all == true){
                //If the bus is due now, dont display "due in due minutes"
                if(body.results[i].duetime === "Due"){
                    message +=  body.results[i].route + " to " +
                                body.results[i].destination + " due now\n";
                    
                }
                //Stop 1 minute appearing as "1 minutes"
                else if(body.results[i].duetime === "1"){
                    message +=  body.results[i].route + " to " + 
                                body.results[i].destination + " due in " + 
                                body.results[i].duetime + " minute\n";
                }
                else{
                    message +=  body.results[i].route + " to " + 
                                body.results[i].destination + " due in " + 
                                body.results[i].duetime + " minutes\n";
                }
                resultCount++;
            }
        }
        //Check if there is not times available
        if(resultCount === 0){
            message = "There is no times available for " + busNumber + " 😕";
        }

        //Send the result back to the requester
        res.send( message);
        }
    }
}); 
});
```
To conclude, the API successfully takes in values sent by the chat bot application and returns the appropriate real time information in a suitable and human like message.
