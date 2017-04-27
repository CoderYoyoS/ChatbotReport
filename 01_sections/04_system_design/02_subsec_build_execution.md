##Build Execution
This section will demonstrate how the code progresses from the developers machine to running live on the Heroku app servers. This process will highlight the strategy that the participating team members  took in order to implement a structured routine for development and also to see an overview of how the code migrates from machine to app-server. There are five steps in order to fulfil this journey-to-deployment and they are:

1. __Develop code locally__   

For feature implementation the code must be developed locally first on the team members laptop completely decoupled from the 'live' code that is being served. This is to avoid interfering with the current working build and to nullify uploading any 'breaking' code prematurely. The development environment on the team members machine should be set up in such a way where the application can be hosted from the machine and tested.

```
    api.get('/newroute',function(req,res) {
                message= "The new response"
                res.send(message);
        });
```

2. __Test locally__   

Local testing takes place whilst a new feature is being implemented. An example would be testing a HTTP GET request on a newly-implemented endpoint on the REST API that is being developed. This ensures that the business logic code is behaving as it should be, is returning the correct response and working as intended with the rest of the routes/endpoints.

```
    http://localhost:3333/api/newroute
    "The new response"
    Success! Response code 200!
```

3. __Push to GitHub repository__   

Once the new iteration has been tested locally and all code quality assurances have been made the newly updated software is ready to be pushed to the host repository on GitHub. This will trigger a deployment automatically to the Heroku app server.

```
git add fileThatWasChanged
git commit -m "added 'newroute' to the api, tested it, and it works"
git push origin <branch-im-working-on>
```
4. __Ensure Heroku has auto-deployed__ 

Check the Heroku dashboard for the application, ensure it has deployed without-error and running with no issues. 'Build succeeded' and 'Deployed' messages will appear on the dashboard as a result of successful deployment.

5. __Test deployed implementation__   

Finally, to make sure that the newly implemented feature works as intended now that it is deployed and live, testing the endpoint is carried out in order to verify the new features function.

```
https://myapp.herokuapp.com/api/newroute
"The new response"
Success! Response code 200!
```

A logical representation of how the code builds from development to app server is shown in figure 5.3. This diagram highlights the behaviours that take place once a new feature has been pushed to GitHub with reference to the project application. Each team member has the sole responsibility for their own API and each team member has the responsibility to develop the main application also.




![Build Execution. \label{Build Execution}](04_assets/04_system_design/buildexec.png)

