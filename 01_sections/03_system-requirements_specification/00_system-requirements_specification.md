# System Requirements and Specification
This section will present the requirements that this projects application will require. As understood from the methodology section of this paper, decisions needed to be made by the project participants in regard to what feature set the application would have, what the preliminary functionality requirements are, what platform to deploy the application, what messaging platform to integrate with, how to version control the project and also, what natural language processing API to use.


## Preliminary Requirements Specification
A number of surveys have been carried out using the online survey development software, SurveyMonkey, to outline the direction of the project. From inception, this project was envisaged as an application that is specific to ITB and that the chatbot could respond to users looking for information about the college. As there is three participants in the project, it was decided to split the workload in three directions, which each team member conducting a survey in their chosen realm of responsibility. Theses categories where transportation, educational facilities and fitness facilities. Respectively, three surveys were created on three topics: Transportation survey, Educational Facilities survey and Fitness Facilities survey (See Appendix A). The results received were used to gain incite of the functionalities and features the application should possess, and what features may have revealed themselves as redundant and of little consequence. 

### Transportation Survey
 There was a total of 99 participants who have taken part in the transportation survey. In regards to this, it was stated that 49.5% of respondents chose Dublin Bus as their primary means of transport to college, this was followed by driving to college, coming to a sum of 40.4%. This aided in the motivation to implement a transport data service into the application for users to easily obtain information.

![Transportation: Question 1. \label{sample}](04_assets/03_system_requirements_specification/question_1.PNG)   

Secondly, the partakers where asked to specify their typical area for travelling home from college, and to state any alternative areas should they not be on the survey. A majority of the participants responded with 60.76% favour for Blanchardstown Shopping Centre, and succeeded by Corduff, with a high rate of 39.24%. A further understanding of features and attributes to incorporate was provided with these features, therefore it was conceived higher emphasis should be placed on transportation information for the Blanchardstown Shopping Centre.

![Transportation: Question 2. \label{sample}](04_assets/03_system_requirements_specification/question_4.PNG)   

Furthermore, it was asked to the average commuter of the shuttle bus provided by ITB whether they felt that the journey times for were difficult to locate on the college website. The results found that 13.27% found it difficult. A proportionally high fraction of commuters at 48.9% found it to be considerably difficult and a 37.76% of applicants found it to not be difficult. Although there is a high portion of participants that do not consider the shuttle bus times complicated to obtain, there is however a sum of 62.25% that may believe there is improvements that could be made. The evaluations of these results lead to considerations on providing journey times for the ITB shuttle bus.

![Transportation: Question 3. \label{sample}](04_assets/03_system_requirements_specification/question_2.PNG)   

### Educational Facilities Survey

For educational facilities, such as the library, four staff members were asked to undergo a survey based on their experience of working in library. Initially, they were asked to highlight the most frequently asked question that they receive from students when working at the student information desk. Laptop loans were the highest selected query, resulting in 50% of the overall given choice. Followed by a tying percentage of 25% between printing information and locating books within the library.

![Educational Facilities: Question 1. \label{sample}](04_assets/03_system_requirements_specification/Daire_q1.PNG)   

Moreover, the staff members where asked to indicate the most recurrent queries in regards to account passwords. Forgotten pin was a the most prominent queries asked according to the participants. Secondly was queries regarding information on how to use their online accounts.

![Educational Facilities: Question 2. \label{sample}](04_assets/03_system_requirements_specification/Daire_q2.PNG)   

<!--Book information queries received a dominant result of 100% for locating a book. -->

### Fitness Facilities Survey

The fitness facilities survey also provided an accurate gauge of the commonly asked questions that the staff members in the ITB gym facilities are consistently queried with. The survey was conducted and five staff members participated. Firstly, staff members were asked to highlight the most frequent questions they are consistently asked. These questions were narrowed down to broad categories. See fig{whatever} to demonstrate how this was done.

![Fitness Facilities: Question 1. \label{Fitness Facilities}](04_assets/03_system_requirements_specification/Brian_q1.PNG)    

Furthermore, the staff members were asked to complete a question isolated to a specific area mentioned in the previous question. For example, they were asked to identify commonly asked questions surrounding the area of opening times. See fig{whatever} to demonstrate how the question was delivered.

![Fitness Facilities: Question 2. \label{Fitness Facilities}](04_assets/03_system_requirements_specification/Brian_q2.PNG)   

The remainder of these survey questions were designed in a similar fashion in order to gain a better understanding of each area in isolation. These survey questions contributed to arriving at a viable and plausible feature set for the application. 


## Functional Requirements

The proposed chat bot application's main desirable functionality is to provide students with a number of informational conversations about the college in real time. This involves the implementation of an easy-to-use system that shall engage with the user and provide an intuitive flow of conversation. The chat bot shall also be given with the ability to participate in basic conversational pieces to provide a more "human-like" interaction for the user.

-------------------------------------------------------------
 Functional Requirements
----------- ---------------------- -------------------------
1                                  Instant responses

2                                  Conversational capabilities 

3                                  Natural language processing 

4                                  Intuitive conversation flow and
                                    ease of use
-------------------------------------------------------------

(@)  The chat bot application shall consist of the ability to response to a user almost instantaneously when a conversation is invoked. A user may begin a conversation with the application by using the ``` Get started ``` button or by simply messaging the chat bot. In turn, the chat bot shall respond instantly to provide the user with a quick and efficient means of obtaining information.
(@)  Conversation capabilities shall be incorporated in order to provide a "human-like" experience for the user. The chat bot shall respond using informal and natural phrases and expressions. An example of this use case would be a user saying "hello" to the chat bot. This should result in a natural reply in terms of language and structure. Additionally, the chat bot shall have multiple responses for the same input. To give a more of an organic interaction, the chatbot shall be able to respond in several ways for the same kind of message it has received. For instance, if a user asks "what's up?", the chatbot should be able to react with several meaningful responses.
(@) The application shall be able to handle the input of natural language by humans. The use of natural language process provides a means to extract meaning full data and contexts from human input and operate with large conversations. Additionally, it shall be able to understand the input from a human given the event of a human incorrectly spelling a word using the method of lexical analysis and evaluating the conversational discourse. A use case of this would be if a user typed "I am on the __bua__" instead of saying the word "bus", the chatbot shall still be able to understand the intent of the conversation.
(@) Lastly, the chat bot establishes an easy and intuitive conversational flow to guide the user through the dialogue to provide the most meaningful interaction as possible. This shall be achieved by providing the user with as much information as possible in order to prevent the user from being confused or disinterested with the interaction. This could be done through the use of buttons or menus.

## User Interface Specification
The system shall be comprised with an intuitive and clean design. Chat bot applications should give a means for the user to instantly interact without an unsolicited need to type every response they wish to give. To accommodate for this, the use of buttons should be implemented to allow for a user to interact quickly and efficiently. In regard to graphical user interface design, no GUI implementation is necessary as the interface is provided by Facebook's Messenger application. However, Facebook does provide a number of templates, menus and buttons that can be utilised to design a smooth and straightforward interaction with the application. These content types use raw JSON which is sent to the Messenger Send API to generate graphical components for a messenger chat conversation. The application makes use of a number of these content types in order to display the understanding and comprehension of using the assets that Facebook provides.
