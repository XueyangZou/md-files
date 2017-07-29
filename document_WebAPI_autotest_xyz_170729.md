# Appropolis document: Auto-test Web API procedure

  
The implementation was carried out in Postman, a Javascript-based platform to perform automated WebAPI testing. It runs a collection of request in orders and enables adding handlers on each WebAPI features. The [official documentation](https://www.getpostman.com/docs/postman/collection_runs/starting_a_collection_run)is list in its official website.  
+ Step 1: set up  
    * Download Postman from its [official website](https://www.getpostman.com/pricing). Create a collection/folder from by navigating to the side panel on the left. ![Left panel](leftpanel.JPG)  
    * Create a folder for saving all the requests. Customize the request body and header the same way as sending single Postman requests.  
    * It is recommended to create an environment containing customized environment/global variables for a specific collection. This makes switching between scenarios easier. To do so, click on the gear icon, select *Manage Environment*, which opens the *Manage Environment* dialogue box.  
    * Next we need to set environment variables. You can either do it using embeded scripts or through GUI. Environment variables are key-value pairs enclosed in double-curly brackets. To set environment variables through GUI, in the *Manage Environment* dialogue box, click on *Add* button, this allows you to add a new environment. After that, in the *key-value pair table*, enter the name of your variable under the *Key* column, enter the values/texts that are supposed to be resolved by Postman under the *Value* column. Click *Update*.
    * Reference the *key-value pairs* using {{**Your key**}} in places you would normally put your values.
    * To create environment variables through script, simply put the following command in the *pre-request script* box:
    ``````
    postman.setEnvironmentVariable("key",value)
    ``````
+ Step 2: Write test scripts  
In Postman platform, there are two places you can put your scripts: *pre-request script* box and *tests* box. Scripts in *pre-script* box are executed before the request body, whereas those in *tests* box are executed after sending the request body. The following are the major aspects we would like to test about a WebAPI service:  
    1. For GET method, test if the JSON schema in the response body is exactly the same as the expected template.
    2. For POST method, test a). if the POST is successful; b). if the WebAPI is able to handle error input, including errors in the input field names and attributes, and throws proper error messages.
    3. For PUT method, test a). if the PUT is successful; b). if the WebAPI is able to handle error input, including keep immutable fields, correct error input types and throws proper error messages.
    4. For DELETE method, test a). if the DELETE is successful; b). if it was the designated device getting deleted and c). if the designated device was the only one withe unique DevEUI, which got deleted.  

    The following show the core commands for testing major features.  
    
    * Test if the response body contains certain field:
    ``````
    tests["Response body contains DevEUI"]= responseBody.has("DevEUI");
    ``````
    * Test if the request is successfully executed:
    ``````
    tests["Request executed successfully"]= responseCode.code == 200;
    ``````
    * Test if a specific field is succesfully updated:
    ``````
    var requestbody = JSON.parse(request.data);
    var responsebody = JSON.parse (responseBody);
    tests["Field DevEUI has been updated"] = requestbody.DevEUI == responsebody.DevEUI;
    ``````
    * Test if the deleted device has the designated (hard-coded) DevEUI:
    ``````
    var responsebody = JSON.parse(responseBody);
    tests["Deleted designated device"] = responsebody.deleteResult.DevEUI === {{DevEUI}};

+ Step 3: Run the collection
By default, the requests stored in the collection are executed by order. You can, however, write the conditional workflow by using the following command:  
``````
postman.setNextRequest("RequestName");
``````
or terminate the workflow by:
``````
postman.setNextRequst(null);
``````
Click the ">" icon beside the collection folder and hit *Run*. This will navigate to the *Run collection* page. Keep all the settings default and hit *Start Run*. Your collection is running while generating test results.