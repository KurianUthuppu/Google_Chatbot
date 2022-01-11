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


