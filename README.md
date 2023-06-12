                           Orchestrate Queue-based Microservices with AWS Step Functions and Amazon SQS
<p>&nbsp;</p>

# Architectural workflow:

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/a27813e9-ab25-4b16-b1ec-0b2b1eea2573)

                           
# Overview:

we have an e-commerce application that needs to verify the inventory for incoming orders as part of its order-processing workflow. Here, concept is to simulate this process using  Step Functions, which is used to orchestrate the workflow.It will send inventory verification requests to an Amazon Simple Queue Service (SQS) queue. SQS is a managed message queuing service provided by AWS that allows decoupling of components in a distributed system. To handle the inventory verification requests, an AWS Lambda function is introduced. Lambda is a serverless computing service that allows you to run code without provisioning or managing servers. This Lambda function acts as a microservice responsible for managing the inventory. Instead of directly processing the requests, it uses an SQS queue to buffer the incoming requests. The queue acts as a temporary storage for the requests, allowing the Lambda function to process them at its own pace.

When the Lambda function retrieves a request from the queue, it performs the inventory check based on the details provided in the request. Once the inventory check is complete, the Lambda function returns the result to Step Functions, indicating whether the inventory is available or not. When a task in Step Functions is configured this way, it is called a callback pattern. Callback patterns allow you to integrate asynchronous tasks in your workflow, such as the inventory verification microservice.This approach helps ensure that the inventory is checked efficiently and asynchronously, without blocking the order-processing workflow, resulting in a more scalable and responsive system.
<p>&nbsp;</p>

# Implementation:

Starting with Amazon SQS console, have created a Queue that stores orders that are placed on the e-commerce store. For this lab, followed basic confguration so, we won’t make any changes to the queue type and have implemented with "Standard" selected under Type and with name "Onlineorders" as shown below,

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/324f4a7f-a85a-4a54-8f60-f793a9514b00)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/cb75b167-5792-463c-83cb-230d30203c1f)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/7d115293-a6ae-4478-9f16-70bb1954d55f)
<p>&nbsp;</p>

Then have created Step Function with configuration selected as Write your workflow in code, then selected "Standard" Type.

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/2f012dc6-ecb5-45ef-966f-dc0a2444aa59)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/624ef478-1004-49af-a54a-276451cb6204)
<p>&nbsp;</p>

Replace the contents of the State machine definition window with the Amazon States Language (ASL) state machine definition below. Amazon States Language is a JSON-based, structured language used to define your state machine.This state machine uses a task state to put a message on an Amazon SQS queue. This task state is configured for a callback pattern. When we append .waitForTaskToken to our resource, Step Functions will add a task token to the JSON payload and wait for a callback. The microservice can return a result to Step Functions by calling the Step Functions API.
Copied the URL of Amazon SQS queue and pasted it into state machine definition.

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/6535bc68-9273-4fae-82d7-ef603cbca958)
<p>&nbsp;</p> 

Next,  StateMachine name given as "InventoryverifyMachine" and created new excecution role. Step Functions will analyze workflow and generate an IAM policy that includes permissions required by workflow Keeping all other parameters as default.

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/7fd3d5da-8d51-4f9a-b25c-6e5b98e66985)
<p>&nbsp;</p>
 
IAM role has been created with name "Inventoryrole" and attached AmazonSQSFullAccess and AWS StepFunctionsFullAccess policies as shown below,

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/039cc1cc-3cba-4232-a206-e84c1fc030db)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/9f064f6a-4a22-47cf-ae28-378b741ad856)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/eb9affe8-f182-4303-bbfe-41245d85d2da)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/91a61ae3-6c17-4e89-a06b-4b1a08f4df4e)
 <p>&nbsp;</p>
 
 Moving to next step, Lambda function has been created "Inventory". This  Lambda function retrieves messages from Amazon SQS and will return a message to Step Functions that represents the result of the request.  Runtime selected as "Node.js" and added created IAM role "Inventory role" to lambda function.
 
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/5c6c2ca7-d9e4-426f-84fe-704e0e27e0c1)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/f1af4e29-5222-47fe-94ac-03689bc6459f)
<p>&nbsp;</p>
 
 Under Configuration tab-->Triggers -->Add trigger and selected  the SQS trigger with other options Turn on the Activate trigger toggle for the Orders queue and created trigger.
 
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/7893652b-b991-431e-b181-42396eb459d5)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/cea0e156-e9d3-48d2-8c4f-6e8d17aec9f9)
<p>&nbsp;</p>

Finally, Will move to StepFucntion --> under StateMachine as created "InventoryverifyMachine" will Start Exectuion and a new execution dialog box appears, where we can enter input for state machine. This state machine does not depend on the input. we can use the default input.As workflow executes, each step will change color in the Visual workflow pane. Wait a few seconds for execution to complete. Then, in the Execution input and output pane, choose Input and Output to view the inputs and results of your workflow.

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/cb50e740-9c0d-4b74-b9ee-6f39bd53d7a9)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/97566c28-160b-4bdf-a9a6-7b5a38cd333b)
<p>&nbsp;</p>

Step Functions make us to inspect each step of workflow execution, including the inputs and outputs of each state. Select each task in workflow and expand the Input and Output fields under Step details. We can see that the input payload into the state machine is passed from each step to the next, and that the payload is updated as each step completes.

![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/fc4f89d0-806c-47f7-8bbd-9abc8e9915e0)
![image](https://github.com/intuiter/Aws-SQS-and-Step-Functions/assets/135228471/1a783f4d-2a6c-4576-b758-e81568b79567)
 
 Finally have successfully executed the task and Lambda function returned the result to Step Functions, as inventory is available of an order which in return callback pattern satisfied.








                           
