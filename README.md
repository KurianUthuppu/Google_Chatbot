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
- Useful videos - https://www.youtube.com/watch?v=QY1Yfk2JUJI&list=PL42xwJRIG3xCm6O85pd91tQxgxGcq82m4

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

# The code is very simple and self-explanatory - "Thank you for adding me to a DM <User-Name>" or "Thank you for adding me to <chat-room name> and you said <message>"
# "and you said <message>" will be displayed in case the user is entering any message upon adding the chatbot to the space
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
# Displays the message "Bot removed from <space-name>" upon removing the bot from the space
function onRemoveFromSpace(event) {
  console.info("Bot removed from ",
      (event.space.name ? event.space.name : "this chat"));
}
```

#### Adding code towards querying the data from big-query
- You may compare the user entries to specific word format to execute requisite data queries
- Example code used in one of my projects is shown below:
```
# The below code retrieves the basic personal details of the farmer being queried using his/her unique 9 digit id (UID) asssigned 
if (event.message.text.includes("@mU FB") || event.message.text.includes("FB")) {
        var input = event.message.text
        var UID = input.substring(input.indexOf("V"), input.length);
        
      # Checks whether the UID length is correct or not
      if (UID.length == 9) {

        var projectId = '<project-id in big-query (ex:foo-bar-123)';
        
        # Runs the below query in the project mentioned
        var request = {
          query: 'SELECT First_Name, Last_Name, Spouse___Father_name, Gender, Date_of_birth, Age, Marital_Status, Mobile_No, WHERE UID="' + UID + '"GROUP BY UID, VLCC_NAME, First_Name, Last_Name, Spouse___Father_name, Gender, Date_of_birth, Age, Marital_Status, Mobile_No LIMIT 1'
        };
        var queryResults = BigQuery.Jobs.query(request, projectId);
        var jobId = queryResults.jobReference.jobId;
      
        # Check on status of the Query Job.
        var sleepTimeMs = 500;
        while (!queryResults.jobComplete) {
          Utilities.sleep(sleepTimeMs);
          sleepTimeMs *= 2;
          queryResults = BigQuery.Jobs.getQueryResults(projectId, jobId);
        }
      
        # Get all the rows of results.
        var rows = queryResults.rows;
        while (queryResults.pageToken) {
          queryResults = BigQuery.Jobs.getQueryResults(projectId, jobId, {
            pageToken: queryResults.pageToken
          });
          rows = rows.concat(queryResults.rows);
        }
        
        # Formatting the message to be displayed
        if (rows) {
          var data = new Array(rows.length);
          var headers = ["First Name:", "Last Name:", "Spouse/Father Name:", "Gender:", "DOB (YYYY-MM-DD):", "Age:", "Marital Status:", "Mobile No:"];
          var message = "";
          for (var i = 0; i < rows.length; i++) {
          var cols = rows[i].f; data[i] = new Array(cols.length); 
          for (var j = 0; j < cols.length; j++) {
            data[i][j] = cols[j].v; 
            message = message + headers[j] + " " + data[i][j] + "\n";
            }
          }
        
        return { "text": message };
          
        }   
        
        # If query returns no results
        else {
          return { "text": "Sorry, no records found" };
        }   
      }
      
      # If the UID length isn't matching
      else {
        var name = "";
        name = event.user.displayName; 
        var message = "@" + name + ": Sorry, wrong/missing UID. Please type in the correct UID (Ex: V00100014) along with \"@mU FB \".\n(Ex:@mU FB V00100014)" ;
        return { "text": message };
      }    
    }
```
