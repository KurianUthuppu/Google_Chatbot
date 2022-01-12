# Google_Chatbot
Creating a chatbot that extracts requisite data from Big-Query dataset and shares responses in google chat

### Requirements
* Valid Google account
* Browser - Chrome / Firefox

### Resources
- Google data-studio login - https://datastudio.google.com/  
- Google chat login - https://mail.google.com/chat/u/1/#chat/welcome  
- Big-Query/GCP platform login - https://cloud.google.com/?authuser=1  
- Google scripts - https://script.google.com/u/1/home/start  

### Setup
- Setup your project in GCP and load datatables to your Big-Query datasets
  - You may refer the following reference material for the same - https://github.com/KurianUthuppu/GCP_BigQuery.git
- Link the project to your billing account

### Chatbot code
#### Editing the Json code to enable big-query jobs
- In Google scripts homepage, click new project and give the requisite name
- Modify the code in appsscript.json tab with the highlighted code in the box below:
> {  
>    "timeZone": "Asia/Kolkata",  
>    "dependencies": {  
>      "enabledAdvancedServices": [{  
>        "userSymbol": "Drive",  
>        "serviceId": "drive",  
>        "version": "v2"  
>      }, {  
>>      "userSymbol": "BigQuery",  
>>      "serviceId": "bigquery",  
>>      "version": "v2"  
>>      }]  
>    },  
>    "exceptionLogging": "STACKDRIVER",  
>    "runtimeVersion": "V8",  
>    "chat": {  
>    }  
>  }    

#### Invoking the chatbot
- The main functions include:
```
# The first one delineates the response to be given when the user makes specific request via chat
function onMessage(event) {}

# The 2nd one is to display the requisite message when the chatbot is added to a space/ chat-room
function onAddToSpace(event) {}

# The last one is to display the requisite message when the chatbot is removed from the space/chat-room
function onRemoveFromSpace(event) {}
```
- The below is the standard template for the 2nd and the 3rd functions in the above list
```
function onAddToSpace(event) {
  var message = "";

  if (event.space.singleUserBotDm) {
    message = "Thank you for adding me to a DM, " + event.user.displayName + "!";
  } else {
    message = "Thank you for adding me to " +
        (event.space.displayName ? event.space.displayName : "this chat");
  }

  if (event.message) {
    // Bot added through @mention.
    message = message + " and you said: \"" + event.message.text + "\"";
  }

  return { "text": message };
}

/**
 * Responds to a REMOVED_FROM_SPACE event in Hangouts Chat.
 *
 * @param {Object} event the event object from Hangouts Chat
 */
function onRemoveFromSpace(event) {
  console.info("Bot removed from ",
      (event.space.name ? event.space.name : "this chat"));
}
```
