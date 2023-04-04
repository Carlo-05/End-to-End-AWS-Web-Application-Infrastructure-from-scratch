## End-to-End-AWS-Web-Application-Infrastructure-from-scratch
Let's make a simple web application that can perform base to the power math function.

The AWS Infrastructure should look like this:

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/Project%20Infrastraucture.png?raw=true)

# Step 1: Create/host first a test web page.

- Use the Amplify Hosting
- From your existing code: _select_ **Deploy without Git provider**
- Name: **BaseOfAPower**
- Environment name: **dev**
- Method: _select_ **Drag and drop**
  - Create a simple HTML file to verify your AWS amplify is working
    - Open notepad
    - Input this html code:
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Base of a Power!</title>
</head>

<body>
    Hello World!
</body>
</html>

```
  - Save as index.html
  - Zip the index.html
  - Drag the zip file into the **Drag and drop your project's build output directory or zip file here.**
  - Save and Deploy
- Click on the provide domain. It should look like this: **https://dev.d2*****nu***ib.amplifyapp.com**
- You should see a "Hello world" web page.

Now that we verify that the web page is working. We'll move to the next step.

# Step 2: Create a Lambda function for testing a base of a power math function.

- Create function
  - Create function: _select_ **Author from scratch**
  - Function name: **BaseOfAPowerFunction**
  - Runtime: **Python 3.9**
  - Create function
- On the code tab, input this code into the lambda\_function:
```
import json
import math

def lambda\_handler(event, context):
# Perform base of a power math function
  mathResult = math.pow(int(event['base']), int(event['exponent']))

# return as a formatted JSON object
  return {
  'statusCode': 200,
  'body': json.dumps('The result is ' + str(mathResult))
  }
```
- Select Deploy
- Select the Test
  - Test event action: _select_ **Create new event**
  - Event name: **Test**
  - Event JSON:
```
{
"base": 2,
"exponent": 3
}
```
  - Save
- Select the Test again
  - The test will produce an Execution result
  - The result should be:
    - Test Event Name: **Test**
    - Response:

**{**

**"statusCode": 200,**

**"body": "\"The result is 8.0\""**

**}**

Now that we test that your code is working. We'll move to the next step.

# Step 3: Create Amazon API Gateway

- Choose an API type: **REST API**
  - **Note:** There are two REST API in the selection. Select the one with the description "Develop a REST API where you gain complete control over the request and response along with API management capabilities."
- _Select_ **Build**
  - Choose the protocol: **REST**
  - Create new API: _select_ **New API**
    - API name: **BaseOfAPowerAPI**
  - Create API
- _Select_ **Actions**
  - _select_ **Create Method**
  
  ![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%201.png?raw=true)

  - _select_ **POST**
  
  ![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%202.png?raw=true)

  - click the check button
  
  ![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%203.png?raw=true)

- POST-Setup:
  - Integration type: _select_ **Lambda Function**
  - Lambda Function: **BaseOfAPowerFunction**
  - Save
- Enable CORS (Cross-origin resource sharing)

**Note:** We do this for our web application in AWS Amplify to have access to our Lambda Function.

  - Leave everything on default setting.
  - Enable CORS and replace exiting CORS headers
  - Confirm method changes: **Yes, replace existing values**
- Deploy API

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%204.png?raw=true)


  - Deployment stage: **[New Stage]**
  - Stage name: **dev**
  - Deploy
- Take note of the Invoke URL that's look like this:

**https://\*\*\*\*\*\*\*\*\*4.execute-api.us-east-1.amazonaws.com/dev**

- After deployment, select **Resources** on the left side of your screen under the **API: BaseOfAPowerAPI** to test our API to trigger our Lambda function.
- _Select_ **POST**

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%205.png?raw=true)

- _Select_ **Test**

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%206.png?raw=true)

- Request body:
```
{
"base": 2,
"exponent": 4
}
```
- Test
- After you execute the test, it should show you this:

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%207.png?raw=true)

# Step 4: Connect your web app in AWS Amplify into the AWS API Gateway

- Let us first update our index.html
  - Copy the and modify the code by inserting your API invoke URL under  **// make API call with parameters and use promises to get response**
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Base of a Power!</title>
    <!-- Styling for the client UI -->
    <style>
        body {
            background-color: #F5F5F5;
            font-family: Arial, sans-serif;
            font-size: 16px;
            line-height: 1.5;
            margin: 0;
            padding: 0;
        }
        header {
            background-color: #86C232;
            color: #FFFFFF;
            font-size: 32px;
            font-weight: bold;
            padding: 20px;
            text-align: center;
        }
        form {
            background-color: #FFFFFF;
            border: 1px solid #D8D8D8;
            border-radius: 5px;
            margin: 20px auto;
            max-width: 600px;
            padding: 20px;
        }
        label {
            color: #333333;
            display: block;
            font-size: 20px;
            margin-bottom: 5px;
        }
        input[type="text"] {
            border: 1px solid #D8D8D8;
            border-radius: 5px;
            font-size: 20px;
            padding: 10px;
            width: 200px;
        }
        button {
            background-color: #86C232;
            border: none;
            border-radius: 5px;
            color: #FFFFFF;
            cursor: pointer;
            font-size: 20px;
            font-weight: bold;
            padding: 10px 20px;
            transition: background-color 0.3s ease;
        }
        button:hover {
            background-color: #5C8E1A;
        }
    </style>
    <script>
        // callAPI function that takes the base and exponent numbers as parameters
        var callAPI = (base,exponent)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"base":base,"exponent":exponent});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("<API invoke URL>", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
</head>
<body>
    <header>Base of a Power!</header>
    <form>
        <label for="base">Base number:</label>
        <input type="text" id="base" name="base">
        <label for="exponent">...to the power of:</label>
        <input type="text" id="exponent" name="exponent">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('base').value,document.getElementById('exponent').value)">CALCULATE</button>
    </form>
</body>
</html>

```
- Zip the index.html
- Go to your AWS Amplify console and select **BaseOfAPower.**
- Drag the zip file into the **Drag and drop your project's build output directory or zip file here.** Wait for it to load.
- Click your domain address
- Test your web app. It should be working.

# Step 5: Create NoSQL database using AWS DynamoDB.

- Create table
  - Table name: **BaseOfAPowerTable**
  - Partition Key: **ID**
  - Leave the rest as default
  - Create table
- Click on the table name, in our case it is called **BaseOFAPowerTable.**
- Click Additional info under General Information

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%208.png?raw=true)

- Copy the Amazon Resource Name (ARN)

# Step 6: Modify your Lambda function to log result into your DynamoDB

- Copy this code
```
import json
import math

# import the AWS SDK for Python
import boto3
# import two packages for dates and date formatting
from time import gmtime, strftime
import time


# create a DynamoDB object using the AWS SDK
dynamodb = boto3.resource('dynamodb')
# use the DynamoDB object to select our table
table = dynamodb.Table('BaseOfAPowerTable')



def lambda_handler(event, context):

# Perform base of a power math function
    mathResult = math.pow(int(event['base']), int(event['exponent']))

# Write result and time to the DynamoDB table
    response = table.put_item(
        Item={
            'ID': str(mathResult),
            'LatestGreetingTime':strftime("%a, %d %b %Y %H:%M:%S %z")
            })

# return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```
- Deploy
- On the configuration tab, click the role name under **Execution Role**

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%209.png?raw=true)

  - It will open a tab in IAM BaseOfAPoweFunction-role:
    - Create an inline policy
    - 
    ![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2010.png?raw=true)

    - Select the **JSON** tab
    - Copy pastes this code to give permission to Lambda Function to invoke DynamoDB actions. Insert the ARN of you DynamoDB table **BaseOfAPowerTable** into the Resources
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "BaseOfAPower",
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:Scan",
                "dynamodb:Query",
                "dynamodb:UpdateItem"
            ],
            "Resource": "<BaseOfAPowerTable ARN>"
        }
    ]
}
```
   - Review Policy
   - Name: **BaseOfAPower-role**
   - Create policy
- Let's test the code on our lambda console:

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2011.png?raw=true)

- The execution result should be like this:

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2012.png?raw=true)

- Check your **BaseOfAPowerTable.** And click **Explore table items.** You should see a result log from your Lambda Function:

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2013.png?raw=true)

# Step 7: Test your End-to-end web application

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2014.png?raw=true)

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2015.png?raw=true)

- New updated data in **BaseOfAPowerTable**.

![End-to-End AWS Web Application from Scratch](https://github.com/Carlo-05/hello-world/blob/main/picture%2016.png?raw=true)
