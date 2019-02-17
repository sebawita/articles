# KinveyChat vs Common Chatbot Challenges 

We live in the times where more and more people prefer to communicate via text messaging apps than a direct call. Sure, text messaging doesn't have the same level of communication <!--(or connection)--> as a voice call, but it is also a lot simpler and more convenient.

The change of the expectations in the communication brought in new ways of communicating with customers, which opened the doors to the chatbot technology.

<!-- Should I include any research references and stats? -->

## Benefits

The emergence of chatbots brought in a whole new set of possibilities and ways in which companies could improve the customer service.

Chatbots can help reduce the common reception/call centre pain points.

How many customers abandon their call with Customer Service, because they had enough of listening to the same annoying melody on a loop, whilst waiting to speak to an agent? Chatbots never get tired, so you could provide a basic/medium level of service 24/7 and without any delay. As the result you will get a lower drop out rate.

Chatbots <!--never get bored and they--> are great at handling repetitive tasks. While the chatbots take care of the most common scenarios, your agents can use their skills for more challenging requests. By relieving you agents of some of the workload, it shortens the queue for customers that want to talk to a person. 

Perhaps the most impressive feature of a chatbot is the speed of communication. Chatbots can answer questions within moments, including answers that require getting data from a database. As a result  your customers can give you 5* a lot quicker.

## The Human Question

One of the biggest appeals of chatbots is their likeliness to a human interaction.

> Which inevitably raises the big question: 
> 
> "Should chatbots pretend to be human?"

Although our initial instinct might be to say *yes*, we should take a step back first, and think about the possible implications.

Besides the quality of the products and services, one of the biggest assets of any company/institution is their relationship with their customers.

With almost every conversation with another person we reveal a bit of ourselves. We make an effort to be polite, we speak with respect, and to form the communication channel we build a little "bridge".

Trying to make customers believe that they are building a bridge with a machine, could be considered a betrayal of their trust. Should they realise that their efforts to build the communication bridge was a waste of time, they might feel aggravated.

> It takes years to build reputation, and moments to loose it all.

With that picture in mind, the answer to the question: 

"Should chatbots pretend to be human?" is a straightforward **NO**.

However, this doesn't mean that the chatbot shouldn't mimic a human conversation. The user should be aware that they are talking to a machine, but still be able to talk to it like to a normal person.

## Challenges

Being able to mimic a human interaction is not easy though and a lot of existing chatbots struggle with various challenges.

Let's have a look at some of the most common challenges.

### Fixed decision trees

Majority of chatbots today force users to navigate a decision tree built by developers. There is no way for a user to say things concerning previous steps or change his intent midway through the conversation.

In simpler words, many chatbots will only work in a very specific order:
Steps: #1 => #2 => #3
> NEED A BAD CASE EXAMPLE HERE

**The Expectation:**
Allow users to provide your chatbot with the information, in the order that is convenient to them. But also allow them to change their previous answers. Then finally use prompts to guide users on the right path.
> NEED A GOOD CASE EXAMPLE HERE

### No understanding of business entities

In some cases chatbots have no knowledge of the key entities. Making it a lot more difficult to construct a conversation that is helpful.

Imagine talking to a receptionist in a hospital that doesn't know what doctors work there or where to find a specific treatment room. That wouldn't be very helpful, would it.

> User: "I want to see Doctor Sebastian"
> 
> Chatbot Understands: the patient wants to see a doctor
> 
> Chatbot: "Which doctor would you like to see?"
> 
> User: "eeerm... Doctor Sebastian"
> 
> Chatbot understands: Chatbots queries the database for: "eeerm... Doctor Sebastian"
> 
> Chatbot: "Couldn't find eeerm... Doctor Sebastian"

Your chatbot should be able to understand common entities such as Doctors, Offices, or Categories using natural language and return the object equivalent for these from your own business systems.

A chatbot that is aware of the key entities, can be more aware of the information provided by the user.

> User: "I want to see Doctor Sebastian"
> 
> Chatbot Understands: the patient wants to see a doctor, the doctor is Dr. Sebastian Witalec { id, name, speciality, visitingHours...}
> 
> Chatbot: "When would you like to see Dr. Witalec? He is usually available on {{doctor.visitingHours}}".

### Lack of proper acknowledgements

Another important part of human-like interaction mechanism is two-way communication. When you talk to another person, often you will look for clues to see if the other person understood what you are talking about.

This is even more important when we interact with machines. When you tell a chatbot something like "I want to see a Dentist on Tuesday next week", you would hope that it understood two pieces of information here:

* you want to see a **Dentist**
* you want the appointment to be on **Tuesday**, but specifically on Tuesday **next week**

So, if the chatbot responds with:

"What time?"

You wouldn't be entirely sure, if the chatbot understands which day specifically you want the appointment for.

<!--The Solution is quite simple.-->
**Solution**:
Ensure that your chatbot communicates back the information it has identified during a conversation. Don’t leave your users to guess where they are in the process.

Now a communication like this would make the user a lot more comfortable with the whole process, and they are more likely complete the conversation:

> User: "I want to see a Dentist on Tuesday next week"
> 
> Chatbot: "Let me help you book a Dentist appointment for 5-Mar-2019. What time?"

### Use of Conversational UI

Some of the conversations require the user to make a choice from a limited number of options. It is usually not a good idea to make the user guess what these options are, as they might get frustrated or miss out on a choice that could be optimal for them. It is never fun when a conversations goes like this:

> User: "I want to book an appointment with Dr. Maria on Monday."
> 
> Chat: "What time?"
> 
> User: "Ideally at 11:00"
> 
> Chat: "Sorry, that slot is not available."
> 
> User: "How about 11:30?"
> 
> Chat: "Sorry, that slot is not available."
> 
> User: "This is going to take a while."
> 
> Chat: "Sorry, that slot is not available."

It’s always best to enable users to quickly select an option when there are a few predefined options. This can be achieved by extending the conversation mechanism with Conversational UI. This can be as simple as providing the user with a number of buttons for each available option, or even a pop-up with a list containing the items and some additional list of options.

The previous conversation could be a lot smoother with the use of Conversational UI:

> User: "I want to book an appointment with Dr. Maria on Monday."
> 
> Chat: "Here are some available slots. Select the time that works for you."
> 
> [12:30] [14:00] [15:30] [16:00]
> 
> User: (click) "[15:30]"
> 
> Chat: Excellent, I will book your appointment with Dr. Maria on Monday at 15:30."

### Dealing with ambiguities

The daily language we speak is full of ambiguous statements. Often you will come out from a meeting wondering wether the other person meant one thing or the the opposite.

Similarly, chatbots suffer from a level of confusion with ambiguous statements. They can even get confused when multiple domain entities are used in a sentence.

For example, chatbots might get confused with statements like:

> User: "I want to book an appointment. Dr. Adam said that I should see Dr. Maria.
> 
> Bot: "OK, let me book you an appointment with Dr. Adam"

So what do we do when we don't understand what someone else said in a meeting? ... We just wait in silence, because we don't want to be embarrassed. But what we should do - and what chatbots should also be doing - is to notify the other person/user that we are not sure what they meant exactly and ask to clarify.

This is how this conversation should look like:

> User: "I want to book an appointment. Dr. Adam said that I should see Dr. Maria.
> 
> Bot: "I am unsure here. Which doctor would you like to see?"
> [Dr. Adam]  [Dr. Maria]
> 
> User: (click) [Dr. Maria]
> 
> Bot: "OK, let me book you an appointment with Dr. Maria"

### No long-term memory

Unlike most humans, chatbots are capable of remembering any piece of information for as long as it is needed.
However, many chatbots out there seem to treat each conversation as a conversation with a **brand new** user.
<!--All the history and information provided in the past is not used to aid the conversation. This can be quite frustrating to the user.
-->
<!--However, many chatbots out there seem to forget about the information provided in the previous sessions, and they ask for the same information with each new conversation.--> 

> User: "I would like my usual coffee"
>
> Chatbot: "Welcome to Coffee With Love. What coffee would you like?" [Americano] [Latte] [Cappuccino] [Cortado]
> 
> User: (click) [Latte]
> Chatbot: "Great, I will send you a Text Message when your coffee is ready. What is your phone number?"
> 
> User: Don't you have it. I gave it to you 10 times already :s
> 
> Chatbot: "Sorry that is an invalid format. What is your phone number?"

<!--
> Chatbot: "I will send the booking confirmation via a Text Message. What is your phone number?"
> 
> User: "Don't you have it. I gave it to you 10 times already :s"
> 
> Chatbot: "Sorry that is an invalid format. What is your phone number?"
-->
Chatbots are most suitable to automate repetitive business processes for users. The expectation is that a chatbot learns your preferences over time, and gives you an option to just confirm or update the existing info.

The previous conversation could be a lot smoother with a chatbot that knows the user:

> User: "I would like a coffee"
>
> Chatbot: "Welcome to Coffee With Love. Would you like your usual Latte?" [Yes] [No]
> 
> User: (click) [Yes]
> 
> Chatbot: "Great, I will send you a Text Message when your coffee is ready. Is this your number: 0123123123?"
> 
> [YES], [NO]
> 
> User: (click) [YES]
> 
> Chatbot: "Great, your coffee will be with you soon."

## Enters the Hero - KinveyChat

Implementing the solutions to the above challenges can be quite tricky. In many cases the implementation of the logic that drives the general functionality of the chatbot is mixed with the logic that drives individual conversations. Blurring the lines between what is the engine of the chatbot and what drives each conversation type.

With KinveyChat you don’t have to write a single if statement. Instead, you use a declarative language to construct a series of steps which define the conversation. This translates into a chatbot that will guide the user through the steps to achieve their goals.

KinveyChat comes with a number of great features that will put you ahead of the game.

### Natural Language Processing
The chatbot can understand more than 72 languages and have a natural conversation with the user.

### Acknowledgements
Acknowledgements make it easy for your users to understand what the chatbot has recognised.

### Flexible conversation flow
The users can provide the required information in the order that feels natural to them.

### Change of Intent
Allowing users to change their mind at every point in the conversation, without having to restart the conversation.

### Automatic Confirmations
Avoid misunderstanding with automatically generated summaries. Users confirm information before important steps.

### Conversational UI
Providing rich experience for your customers. Display list of choices in lists, carousels, maps etc.

### Build once deploy to many
Chatbots can be deployed to a Web app, Mobile app (built with NativeScript), Viber, Facebook Messenger with a few lines of code for each. 

### Frequently Asked Questions
Allows the user to ask various questions, without breaking the flow of the currently active session.
Can be used to decrease the chance of people abandoning a conversation if they have a relevant question to ask.

### Always Focused on Goal
Your chatbot will strive to help your customers complete what they have already started. 

### Value Suggestions
Based on previous conversations with this user stored in Long-term memory.

### Image Recognition
Helps you understand the content of images

### Data Source Connectivity
KinveyChat is excellent at connecting with enterprise and legacy data sources. With very little effort you get access to data in Salesforce, Oracle, SAP, SQL Server, and many other systems without having to write APIs.

### ￼Authentication
You can also connect your chatbot to authentication systems like Active Directory, SAML, or Oauth2. This means you can use KinveyChat to get information from one system, put data in a different system, and ensure only authenticated users can use the app.

### High productivity
The declarative nature of KinveyChat means that you could build a fully functioning chatbot - capable of communicating with a user, pushing the results to a backend - and deploy it in 15 minutes.

## Conclusions
The times are changing, we need to keep reinventing ourselves in order to stay at the top of the game. What worked in the past, might not be enough today. 

If the communication with your customers is critical to your business then introducing a chatbot might be the thing that will give you the edge over your competition.

With KinveyChat you have a one stop shop solution that allows you to deliver the best in breed chatbot your customers will love.

## Next steps
> This needs some work

Things to suggest:

* encourage to get started - point to a tutorial (I will create it soon)
* check out the documentation: need a new link here
* point to either the webinar at the end of March or a recording of it
* point to some examples
