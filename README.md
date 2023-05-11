## Introduction

Managing AWS EC2 instances manually can be time-consuming and inefficient, especially when dealing with multiple instances. However, by leveraging the power of automation, you can significantly simplify and streamline this process. In this blog post, we will explore how to automate the management of AWS EC2 instances using AWS Lambda, EventBridge, and the Boto3 Python library.

## Creating the Lambda Function

**AWS Lambda** is a serverless compute service provided by Amazon Web Services (AWS). It allows you to run your code without provisioning or managing servers. With Lambda, you can focus solely on writing your application logic while AWS handles the underlying infrastructure, including scaling, patching, and availability.

To create an AWS Lambda function for checking and starting EC2 instances, follow these steps:

1. Sign in to the AWS Management Console.
2. Open the AWS Lambda service.
3. Click on "Create function" to create a new Lambda function.
4. Choose the "Author from scratch" option.
5. Provide a suitable name for your function (e.g., "EC2InstanceAutomation").
6. Select the desired runtime. In this case, choose "Python 3.10" or any other compatible runtime.
7. Click on "Create function" to create the function.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683783831312/a49121e0-94b7-4fff-9063-5f8c22e34a0c.png)

Let's create a new function:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683783963148/cf120605-79f7-4c8f-bfe6-a465dfbbd5ea.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683784096030/5a090ab4-4a0d-4836-83a9-7dfbadfab622.png)

Successfully created the function **EC2InstanceAutomation**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683784362139/33bdfe7c-2afc-4804-b9eb-9b2a3df9314d.png)

## Setting the Lambda permissions

In the function's configuration page, scroll down to the "Permissions" section.

Under "Execution role," click on the role name next to the "View the &lt;role_name&gt; role" link. This will open the IAM console in a new tab.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683784816728/12b35eed-3e9c-4bc0-b91e-4472029fe113.png)

Click on the "Attach policies" button to add the necessary permissions to the role.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683785380923/0abae2f7-c3af-4cde-a6e7-f88011852368.png)

In the policy details page, click on the "Edit policy" button.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683785998910/9130013f-d2a8-409a-9e43-5caf1f1bea66.png)

The policy document editor will open, displaying the JSON representation of the policy.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683786123032/8cc8b08a-7333-4293-a018-ac05e9bb427b.png)

Modify the policy document by adding the necessary EC2 permissions to the existing policy.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683786524040/4262b847-a780-4548-b2ab-4852cd358106.png)

```json
{
  "Effect": "Allow",
  "Action": "ec2:*",
  "Resource": "*"
}
```

Update the policy document as per your specific requirements, ensuring you include the necessary actions and any additional conditions or resource restrictions as needed.

Once you have made the required changes, click on the "Review policy" button to validate the policy syntax.

After reviewing the policy, set this as a new version(check the box) and click on the "Save changes" button to update the policy attached to the execution role.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683786738993/65ebc620-deae-478d-9852-2771466d1ce9.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683786682934/20e4f4d8-4f0e-460a-bc7b-67dc54435644.png)

Note: It's crucial to follow the principle of least privilege and grant only the specific permissions required by the Lambda function. Regularly review and audit the permissions granted to ensure they align with your security requirements.

Now that the execution role has been updated with the necessary EC2 permissions, now let's go back to the Lambda function.

## Writing Lambda with Python and Boto3

1. In the function's configuration page, scroll down to the "Function code" section.
2. Choose the inline code editor option.

   ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683787244217/b8abc95d-fccc-48ef-8c31-b34257815948.png)

   Better refer to this doc for a better understanding of how to use Boto3 to manage AWS services

   ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683787931563/a9873216-8b6c-4149-839d-54c8cc85ff9c.png)

   [https://boto3.amazonaws.com/v1/documentation/api/latest/index.html](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)

3. Replace the existing code in the editor with the following code snippet:

   ```python
   import boto3

   def lambda_handler(event, context):

       # create EC2 client
       ec2 = boto3.resource('ec2')

       for instance in ec2.instances.all():
           print(instance.state)
   ```

4. Click on "Deploy" or "Save" to save the Lambda function code.

   ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683788460853/bd440429-aecd-469e-8c62-b3cf7cb341d6.png)

In the function's configuration page, scroll down to the "Function overview" section.

Click on the "Select a test event..." dropdown and choose "Configure test events".

In the test events page, click on the "Create new test event" button.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683788570817/263be322-7df6-478c-bb76-c6f0fdfde3bd.png)

But we don't have EC2 instances to test, let's create some EC2 instances

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789161444/9603e196-3670-4e50-afd9-d863d146e174.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789245577/c39eceb6-7ead-4068-b3b2-e967e71a9d7d.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789284732/15e23a87-cbda-4fcf-a49e-eb618a65e540.png)

Let's stop 2 instances and will do the test,

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789388867/1eebe0b1-76bc-4931-8f57-62e1936b0951.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789420325/4cbaaabf-619b-44c5-b950-f5013cb5d016.png)

Now go back to the code and test it,

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789615885/141a4d6c-8b12-4ebf-b884-a2c3b0b63514.png)

This is the test output:

```json
{'Code': 80, 'Name': 'stopped'}
{'Code': 80, 'Name': 'stopped'}
{'Code': 16, 'Name': 'running'}
{'Code': 16, 'Name': 'running'}
{'Code': 16, 'Name': 'running'}
{'Code': 80, 'Name': 'stopped'}
```

If the instance is stopped, let's start it again:

```python
import boto3

def lambda_handler(event, context):

    # create EC2 client
    ec2 = boto3.resource('ec2')

    for instance in ec2.instances.all():
        state = instance.state['Name']
        if state == 'stopped':
            instance.start()
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789930954/8636f63f-db40-4085-bb42-995baaa32235.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683789943732/11dfb771-71e9-4b61-9fa4-ae28ec0a5905.png)

Now refresh the EC2 management console tab, all stopped instances are started:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683790034072/66aea348-50a1-480b-b5a0-0754390d5e35.png)

```python
import boto3

def lambda_handler(event, context):

    # create EC2 client
    ec2 = boto3.resource('ec2')

    for instance in ec2.instances.all():
        state = instance.state['Name']
        if state == 'stopped':
            try:
                instance.start()
            except:
                print("Something went wrong")
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683790247961/31647172-1c4e-40e0-9961-01e32216bf26.png)

That's it our code is done 🎉

The Lambda function is done, we use boto3, and we can talk to the EC2 instance, now we need to trigger that lambda every hour.

## Creating the EventBridge cron

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683790648788/7689a350-e716-4eb4-af81-94bbc43ac124.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683790739343/72f72ae6-d27e-471b-8168-99fa30b0c35b.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683790944349/1fa74a4b-c03a-4df9-9c8f-596b46830993.png)

To write Cron expression you can refer to this site:

[https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683791011292/bc6066e8-50a5-4a07-b85d-f04749bd3eb8.png)

And now we have a trigger that executes on every hour

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683791118210/9d037f23-98d8-4e4c-86cb-dfffd2e66ebe.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683791126565/a1d363a6-4100-491c-bd9d-e77cb286875a.png)

## Remember to delete your EC2 instances

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683791294472/3777a4a5-af99-47d9-a571-83cabbd706e7.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683791319904/9758e4b1-2cdb-4690-acee-aadaaff202ce.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1683791344173/1e17baf2-7008-4a0a-b016-7b34a8fd67f6.png)

## Conclusion

In conclusion, we have discussed the process of automating AWS EC2 instance management using AWS Lambda, Python, and Boto3. Here's a summary of the steps involved:

1. Creating an AWS Lambda Function:

   - Use the AWS Management Console to create a new Lambda function.
   - Choose the appropriate runtime, such as Python 3.8.
   - Configure the function's name and execution role.

2. Writing Lambda Function Code:

   - Use the inline code editor in the AWS Management Console to write the Python code.
   - Utilize the Boto3 library to interact with the EC2 service.
   - Implement logic to check for stopped instances and start them if necessary.

3. Setting the Lambda Permissions:

   - Configure the execution role associated with the Lambda function.
   - Use the AWS Management Console's IAM service to edit the role's permissions.

4. Creating a Test Event:

   - Use the AWS Management Console to create a test event for the Lambda function.
   - Simulate a scheduled event or provide a sample payload.
   - Modify the event payload to include stopped instances for testing purposes.

By following these steps, you can automate the process of checking for stopped EC2 instances and starting them using an AWS Lambda function triggered by EventBridge. This automation helps ensure that your EC2 instances remain in the desired state and can be efficiently managed.

And don't forget to connect with us on social media to stay updated with the latest tips, tutorials, and guides:

- Connect with us on LinkedIn: [**Shaik Ahmad Nawaz**](https://www.linkedin.com/in/shaik-ahmad-nawaz-894425239/)
- Follow us on Twitter: [**@shaikahmadnawaz**](https://twitter.com/shaikahmadnawaz)

We also encourage you to check out our GitHub repository for more code samples and projects:

- Explore our GitHub: [**shaikahmadnawaz**](https://github.com/shaikahmadnawaz)
