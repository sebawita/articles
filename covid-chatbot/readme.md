## Overview



With NativeChat you don’t need to be a developer to create a chatbot capable of answering all questions your employees might have.

Give control of the chatbot’s knowledge base to your HR team, and enable them to keep the questions and answers up to date, with no delays.

We have prepared a sample COVID-19 chatbot, which comes with a predefined set of Questions and Answers, which you could change to match the needs of your company.

Like this one:

///VIDEO
<video src="./media/01-Covid-Demo-fast-750-718.mp4?raw=true"></video>

## Set up the account

/// TODO: add a link to the registration page

If you are new to NativeChat, and you don't have an account with us yet, you can go to [registration page](), create a new account, which will automatically 

##Create a new project

If you don't have a project yet, you can create it yourself at [bots.nativechat.com](https://bots.nativechat.com).

Follow these steps:

1. Select the **Coronavirus FAQ** Sample

2. Give your chatbot a name: **Coronavirus FAQ**

3. Press the **Create bot** button



## Quick test of the project

On the right hand side, there is a test window, which allows you to play with the chatbot.

Try asking it some questions like:

* *"What is corona?"* 
* *"Can I work from home?"*

The chatbot should respond with an appropriate answer.



You can also say:

- *"I have a travel question"*

And the chatbot will respond with a list of available travel questions.


You chatbot conversation should look like this:

///VIDEO
<video src="/Users/administrator/github/articles/covid-chatbot/media/Covid-First-Test-630-720.mp4?raw=true"></video>

## Reviewing existing Questions and Answers

The chatbot comes with a predefined set of Question and Answers, which you can review at the **Question Answering** page.

> Note. You can find the **Question Answering** page by going to Dashboard then click on **Question Answering**.



At the **Question Answering** page, you should see a list of categories, each containing their own Q&As:

- Covid19QnA
- FinanceQnA 
- MeetingsQnA
- TravelQnA
- WFHQnA

> There is also a category called **SmallTalk**, which is a set of responses to expressions like: *"Hi"*, *"Thanks"*, *"Bye"*. You can ignore it for now.



If you click on **MeetingsQnA**, you should see 3 items:

- *Attending events*
- *Our events status*
- *In-person meetings*

When you click on each of them, you will see that each has a number of questions. This is because usually there are many ways to ask for the same thing. By adding multiple questions, you make it easier for the chatbot to realise when to provide this answer to the user.

Additionally, each item contains a **Group Name**, which is a good way to give the general idea of what this question is about.

Finally, you have the answer, which is what the chatbot will say to the user, when they ask this question.



> Hint. You can click on **Question Answering** to go back to the list of Q&A categories.

## Managing Questions and Answers

Although, the questions and answers that you get out of the box provide you with a good starting point, you need to update them to match your company's messaging.

## Updating an existing Q&A

Updating existing Q&As is really easy, you just pick one, change the values and press save.

Let's try to update the **Our Events Status** Q&A.



**Step 1** — add 2 more triggering questions

Select **Our Events Status** item.

In here you will see 4 different ways to trigger this Q&A.

<img src="./media/01-event-status-questions.png?raw=true" alt="01-event-status-questions" style="zoom:50%;" />



Click on the empty item and add the following text:

- *What happens to ACMEnext?*

Then press **Enter** (or the green plus button) — to add another item — and add this text:

- *What happens to Sales Kickoff?*



Your questions should look like this:

<img src="./media/02-event-status-new-questions.png?raw=true" alt="02-event-status-new-questions" style="zoom:50%;" />



Finally, save the changes by pressing the **Save changes** button.



**Step 2** — test the new questions

Now, you can test the changes by typing:

- *What happened to sales kickoff?*
- *Status of ACMEnext*

Your chatbot should respond with:

<img src="./media/03-event-status-chatbot-response.png?raw=true" alt="event-status-chatbot-response" style="zoom:50%;" />

> Note: the chatbot should be able to match to both of the above questions, even if they don't match exactly to any of the provided questions



**Step 3** — update the answer

Next, you should update the answer to match it to your needs.

You can find the answer section below the list of questions. Just scroll down and you will see it there.

Change the text of the Answer to:

- *All upcoming live events are cancelled.</newline>If you have arranged travel plans, please contact the travel team to get that cancelled.*

Save changes.



> Note. You can use **</newline>** to split the answer in multiple speach bubbles.
>
> You should use </newline> to break down your answers into more manageable chunks of information.



**Step 4** — test the updated answer

Now, you can test the changes by saying:

- *What is the status of the upcoming events?*

Your chatbot should respond with:

<img src="./media/04-event-status-chatbot-response.png?raw=true" alt="event-status-chatbot-response" style="zoom:50%;" />

### Adding a new Q&A

Now that we know how to edit an existing Q&A item, it is time to add a new Q&A to instruct your employees  about your video conferencing tools.



**Step 1** — add a new Q&A 

From the **MeetingsQnA** category, press the **+ Add a question** button.



Add the following **Questions**:

- *Video conferencing*
- *What to use for video calls?*
- *How to make a conference call?*
- *Can I use Zoom?*
- *Can I use Microsoft Teams?*



> Note. It is best for the first question to be short and generic .
>
> This is because when you ask the chatbot to give you a list of available question it uses the first entry for each Q&A to display the options.
>



Set the **Group name**:

- video conferencing



Set the follwing **Answer**:

- *For all internal voice/video calls you should use Microsoft Teams.</newline>For external call, you should use Zoom or Microsoft Teams.</newline>You can find the setup instructions at www.mycompany.com/IT/conference-calls*

Your new Q&A should look like this:

<img src="./media/05-video-conferencing-settings.png?raw=true" alt="video-conferencing-settings" style="zoom:50%;" />

Finally, save the changes by pressing the **Save changes** button.



**Step 2** — test the new Q&A

Now, you can test the new Q&A by saying:

- *What to use for conference calls?*
- *Can I use zoom?*

Your chatbot should respond with:

<img src="./media/06-video-conferencing-response.png?raw=true" alt="video-conferencing-response" style="zoom:50%;" />

### Removing a Q&A

You can easily remove any Q&A from the list.
To do that just click on the trash can icon, and the whole Q&A will be gone.

<img src="./media/07-removing-qna.png?raw=true" alt="removing-qna" style="zoom:50%;" />

> Note. there is no undo button, so if you delete a Q&A it will be gone forever.
>
> Before you make any major changes, you can export the whole Q&A category to a csv file. Then if you want to go back to where you were, you can import it back.



## List of possible questions

The COVID-19 Chatbot comes with an interesting feature, which allows the users to type:

- *Covid Questions*
- *Travel Questions*
- *Finance Questions*
- *Meetings Questions*
- *WFH Questions*

Each expression will result in displaying a set of available interactive questions. For example:

<img src="./media/08-finance-question-list-demo.png?raw=true" alt="finance-question-list-demo" style="zoom:50%;" />

You are probably wondering: "how does the chatbot know what text to display for each item?"

If you open the **FinanceQnA** category, you will see a list of Q&As. 
The first question for each Q&A is the text used to display the available questions to the user.

<img src="./media/09-finance-expenses-settings.png?raw=true" alt="finance-expenses-settings" style="zoom:50%;" />

## Removing a Q&A Category

You may choose not to use one of the Q&A Categories provided by the COVID-19 Chatbot.

If this is the case for you, you can do that in the following simple steps:



**Step 1** — remove the unwanted category

First, you need to remove the unwanted category and all its' Q&As. 

Go to the **Question Answering** page.

Then press the trash can next to the category that you want to remove.



**Step 2** — remove the conversation trigger

Navigate to the **Conversation Triggers** tab

<img src="./media/10-conversation-trigger-navigation.png?raw=true" alt="conversation-trigger-navigation" style="zoom:50%;" />



Here, you need to remove the **Conversatoin Trigger** that show a list of available questions for the category that you are removing.



> Note. Conversation Trigger is a set of expressions that starts a conversation.
>
> For example **travel-qna** - shows a list of Travel Questions, when you say something that sounds like:
>
> - Travel questions
> - I have a Travel question

<img src="./media/11-conversation-trigger-travel-qna.png?raw=true" alt="conversation-trigger-travel-qna" style="zoom:50%;" />



Locate the conversation trigger that is no longer needed, and delete it by clicking the trash icon next to it.



**Step 3** — Test

You can try to ask questions that belonged to the removed category.

Additionally, you could ask to show a list of questions for that category.

In both cases, the chatbot should respond with: "*Ask me anything*" and a hint.



## Adding a new Question and Answers Category

If you want to add more questions and answers, but you feel like they don't fit any of the categories provided, you can add a new category.

For example let's add a category for **General** questions.



**Step 1** — Create a new category

Navigate to the **Question Answering** page, and press the **+ Add a category** button.

Call the category **GeneralQnA**. Like this:

<img src="./media/12-add-general-qna.png?raw=true" alt="add-general-qna" style="zoom:50%;" />

Press the **Create category** button.

> Note. The QnA at the end it not required, however, it is nice to have consistent naming that makes it clear what you are looking at.
> For any new category that you create, it is best to use this format: **NameQnA**

**Step 2**  — Add your own Q&As

Now you just need to add your own questions to the General category.

Let's add one to answer questions about plants in the office.

Press the **+ Add a question** button.

Add these questions:

- *Office plants*
- *Watering office plants*
- *Who is watering the office plants?*
- *Are the plants in the office OK?*

Set category name:

- *Office plants*

Answer:

- *The facilities team is looking after our beloved plants.*



The new Q&A should look like this:

<img src="./media/13-general-qna-configuration.png?raw=true" alt="general-qna-configuration" style="zoom:50%;" />



Finally, save the changes by pressing the **Save changes** button.



**Step 3** — test the new question

Now, you can test the new Q&A by saying:

- *Is anyone watering the plants?*

Your chatbot should respond with:

<img src="./media/14-general-qna-demo.png?raw=true" alt="general-qna-demo" style="zoom:50%;" />


## Bonus - helpers for lists of questions

When you add a new Category for Q&As, you will notice that when you ask *"General Questions"*, the chatbot doesn't give you a list of available questions for that category.

This is because we have to tell instruct the chatbot how to do that and what to display.

This section is a bit more advanced, however, if you carefully follow the steps below, you should be fine to do it. 😇

### Add Conversation Code

First, we need to add the code for a conversation that will display a list of available questions.

Don't worry, you will be given the code you need, and you will just need to make a few tiny modifications.

> Note. If you get lost at any point of this section, at the end of this set instructions, there is a video-recording showing how to complete this task.



**Step 1** — go to the **Cognitive Flow** page

Click on **Cognitive Flow** tab, which will take you to the Cognitive Flow page. 

> Note. Cognitive Flow is how we tells our chatbot how to handle various conversations.

**Step 2** — find the last conversation (wfh-qna)

On the right hand side, there is a panel that lists all conversations in this chatbot project. Find the last one before **Settings** — it should be **wfh-qna** and click on it.

<img src="./media/15-cognitive-flow-list-of-conversations.png?raw=true" alt="cognitive-flow-list-of-conversations" style="zoom:25%;" />



This will take you to wfh-qna conversation code.

Here you should notice the two higlighted curly brackets **{ }** . 

<img src="./media/16-cognitive-flow-wfh-qna.png?raw=true" alt="cognitive-flow-wfh-qna" style="zoom:50%;" />

**Step 3** — add a new conversation code

Click on the right hand side of the highlighted closing curly bracket (should be at line 156).

Then copy and paste the following code in there.

```json
,"name-qna": {
  "type": "goal",
  "steps": [
    {
      "type": "question",
      "entity": "q",
      "entity-type": "NameQnA",
      "messages": [
        [
          "Check the Name answers for:",
          "Hint: To stop repeating questions, say: \"no more questions\" "
        ]
      ],
      "display": {
        "type": "quick-reply"
      }
    }
  ]
}
```



Next, we need to change the 3 mentions of the word **name** to the name of the Category you want to add.
In the case of this example, we are replacing **name** with **general**, like this:

<img src="./media/17-cognitive-flow-general-qna.png?raw=true" alt="cognitive-flow-general-qna" style="zoom:50%;" />

It is worth explaining each of the items that you had to update

- `"general-qna"`
  — is the name of this conversation. This is used when you need to add a **Conversation Trigger** for this **Conversation**. Take note of it, as we will need it in the next step
- `"entity-type": "GeneralQnA"`
  — tells the chatbot where to get all the Q&A questions to display to the user. It is important to know that this has to match exactly the name you gave to your Q&A Category (in the Question Answering page)
- `"Check the General answers for:"`
  — provides the chatbot with a text to display to the user, so feel free to adjust it to your needs



**Step 4** — save

Press the save button, and we should be good to go.



**Video**

Here is a video of how to complete this whole task:

///VIDEO
<video src="./media/03-Add-general-qna-1300-1040.mp4?raw=true"></video>

### Add Conversation Trigger

The final piece of the puzzle 🧩  is to tell the chatbot when to trigger our new **general-qna** conversation.

This is done from the **Conversation Triggers** page.



**Step 1** — go to **Conversation Triggers** page

Click on the **Training** tab and then click on **Conversation Triggers**.



**Step 2** — add a new conversation trigger

Click on the **+ Add new** button.

Then set the **Conversation Name** to:

- **general-qna** 

> Note. It is important that the conversation name in here matches exactly to the name you provide in the Cognitive Flow page.

Trigger Expressions:

- *General Questions*
- *I have a general question*

The new trigger should look like this:

<img src="./media/18-conversation-trigger-general-qna-configuration.png?raw=true" alt="conversation-trigger-general-qna-configuration" style="zoom:50%;" />

**Step 3** —save

Save the new trigger by pressing the **Save changes** button.



**Step 4** — test

Finally, you should be able to test the new conversation in action, by saying

- *I have a general question*

Your chatbot should respond with:

<img src="./media/19-general-qna-demo.png?raw=true" alt="general-qna-demo" style="zoom:50%;" />

## Final word

Congratulations!!! 🎉🥳

You have just created your first chatbot. I am sure this chatbot will be very useful to your employees, and over time you will add more questions and change some of the existing ones.

In order to make this chatbot available to your employees, you will need to get it published in your internal website. Here is our documentation on [Publishing to Web](https://docs.nativechat.com/docs/1.0/publishing/web.html).