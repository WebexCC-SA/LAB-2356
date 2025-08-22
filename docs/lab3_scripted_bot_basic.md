# Lab 3 - Import and Customize an AI Agent

## **Objectives**

In this lab you will build a scripted AI agent that can respond to both chat and voice channels:

- Know how to import an exported agent
- Know how to create Intents, Entities, and Responses
- Know how to return data back to the calling application
- Know how to use previous interactions to troubleshoot intent and response mapping

This agent will collect the order number and a user PIN from the user, then pass that information back to the Call Studio application. The studio application will then call a RESTful API to get order status and return this status back to the AI Agent. The AI Agent will play the order status back to the customer.

Explanation of formatting:

- *Italics* – This indicates a button, menu option, or object that should be selected.
- "Quoted Text" – This indicates that the text should be typed exactly as indicated.

## **Task 1. Import and Customize an Agent**

In this task, we will import an agent that we have configured for you ahead of time.

**Step 1.** Download the example agent and import into AI Agent Studio

a. Select the [Scripted AI Agent](./downloads/OrderingAIAgent.zip) to download the AI Agent to your desktop. Once you have done this, extract the zip file so that you have the JSON file ready.

b. In AI Agent Studio, select the *Import agent* button at the top, right side of the studio.

![Import Agent](./assets/L3T1S1-ImportAgent.jpg)

c. Select the *Upload* button, then navigate to where you extracted the Agent and select it to import. Provide an agent name that in unique. To do this, include your seat number in the format of, "S01 - My Order Fulfillment Agent". Note that the System ID will be automatically generated, accept the default. After validating that everything is correct, select the *Import* button at the bottom, right of the page.

![Completed Import Screen](./assets/L3T1S1c-FilledInImport.jpg)

**Step 2.** Add Intent and Response to collect required information

a. In the AI Agent configuration, navigate to ***Script*** -> ***Intents*** -> click on the ***Create intent*** button.

![Intent](./assets/L2-09.png)

b. Enter the Intent name as "Start Order Tracking" and enable the toggle button to ***Reset slots after completion***.

c. Under the ***Intent and Utterances*** section, type the phrase "Track my order" and click the ***Add** button.

![Utterance 1](./assets/L2-10.png)

d. Next, add a 2nd utterance by typing "track order number 123456 with pin 1234" and clicking the ***Add*** button.

- As soon as the 2nd utterance is added, the 6 digit number and the 4 digit pin will be highlighted and previously created entities of 'order_number' and 'user_pin' are added under the ***Slot filling*** section.
- In the Slot filling section, check the ***Required*** box for each of these slots.

 ![Utterance 2](./assets/L2-12.jpg)

> What is an entity?
>> Entities are the building blocks of conversations. They are essential elements that the AI agent extracts from user utterances. Entities represent specific pieces of information, such as product names, dates, quantities, or any other significant group of words. By effectively identifying and extracting entities, the AI agent can better understand user intent and provide more accurate and relevant responses.

> What is a slot?
>> The value collected for an entity is called a slot. Consider the following analogy, entity is a variable name and slot is the value of that variable.

> Why do I need to create a slot here? 
>> As part of the Intent, the bot needs to collect the basic info like order number and the user pin so that these 2 values can be passed to go look for the status of an order in the database.

e. Add the Response for missing order number by selecting the drop-down next to the order_number entity in the *Slot filling* section. In the search box, begin typing the phrase "order_number" and select the Custom response named, "order_number".

![Order Number Response](./assets/L3T1S2e-OrderNumberResponse.jpg)

Repeat this for the user_pin entity. The response name you need to find is "pin_missing". Reference the image below for the completed Slot filling section.

![Completed Slot Filling](./assets/L3T1S2e-CompletedSlotFilling.jpg)

f. On the "Start Order Tracking" Intent configuration page and under the ***Intent and utterances*** section, click on the ***Generate*** button.
 ![Utterance-G1](./assets/L2-21.png)

g. In the ***Generate variants*** dialog box, provide a description as described below to generate relevant training data and utterances.
```
Generate prompts to track an order. Five of those prompts include a 6-digit order number. Another five of the prompts include a 6-digit order number and a 4-digit pin.

```

h. In the ***Number of variants*** enter 20 and set ***Creativity*** to High. Lastly, click the ***Generate*** button.

![Utterance-G2](./assets/L2-51.png)

i. This is what your ***Intent and utterances*** configuration section should now look like. The ***order_number*** and ***user_pin*** slots should now be highlighted automatically. You can add more utterances if you like.

*Note: Hallucinations should be rare, but review the generated utterances to enure there are no hallucinations.*

![Utterance-G3](./assets/L2-23.png)

You can also add an utterance as a Regular Expression (RegEx)

- track\*.\*order
- order\*.\*track

![Utterance-RegEx](./assets/L2-23-1.png)

**Step 3.** Create Response to return data to Call Studio

a. In the ***Response*** section, select the drop down and click on ***Create new***.

![Response-I1](./assets/L2-24.png)

b. In the ***Create a response*** pop up windows, enter the response name as ***track_order_wait*** and then select the + symbol and click on ***Voice*** to add Voice channel.

c. In the Text section, add the text below as ***Variant 1***. Feel free to add more of these variants if you like.

```
Please wait while I track your order.

```

d. Now, click on the ***Custom Event*** button to the right to create a custom event and enter as shown below:

- **Event name:** 
```
custom_event
```
- **Event payload:** 
```
{
  "Execute_Request": {
    "Data": {
      "Params": {
        "order_number": "${entity.order_number}",
        "user_pin": "${entity.user_pin}"
      }
    },
    "Event_Name": "custom_event"
  }
}
```

e. Lastly, click ***Create*** and this will take you back to the "Start Order Tracking" Intent configuration page.

![Response-I2](./assets/L2-52.png)

> Why do we need to add Custom Event here?
>> Custom Event is being added here to pass the order_number and user_pin entity data collected by the AI Agent, back to the Call Studio app. This is coming up in the next lab guide.

**Step 6.**
Verify that you've completed all of the required configurations as seen in the screenshot below and click ***Create***.

![Verify Intent config 1](./assets/L2-26.png)

## **Task 3. Integrate to Call Flow**
