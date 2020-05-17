---
title: REST API with 
---

### IAM Setup

**Create Policy**

Create Policy with name `lambda_execute` with following JSON, which will allow to perform `lambda:InvokeFunction` on all resources

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "*"
        }
    ]
} 
```

Create an IAM role `lambda_invoke_function_assume_apigw_role` 

Edit Trust Policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "lambda.amazonaws.com",
          "apigateway.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

---

Creating Lambda Function

Services > Lambda
Enter name "Calc"
Choose Create Function.

Add following code,


```

console.log('Loading the Calc function');

exports.handler = function(event, context, callback) {
    console.log('Received event:', JSON.stringify(event, null, 2));
    if (event.a === undefined || event.b === undefined || event.op === undefined) {
        callback("400 Invalid Input");
    }
    
    var res = {};
    res.a = Number(event.a);
    res.b = Number(event.b);
    res.op = event.op;
    
    if (isNaN(event.a) || isNaN(event.b)) {
        callback("400 Invalid Operand");
    }

    switch(event.op)
    {
        case "+":
        case "add":
            res.c = res.a + res.b;
            break;
        case "-":
        case "sub":
            res.c = res.a - res.b;
            break;
        case "*":
        case "mul":
            res.c = res.a * res.b;
            break;
        case "/":
        case "div":
            res.c = res.b===0 ? NaN : Number(event.a) / Number(event.b);
            break;
        default:
            callback("400 Invalid Operator");
            break;
    }
    callback(null, res);
};

```

Under Execution role, choose Choose an existing role and enter the ARN for the role we just created earlier `lambda_invoke_function_assume_apigw_role`

Choose Save.

### Testing the Lambda Function

```
{
  "a": "2",
  "b": "5",
  "op": "+"
}
```

### Setup API Gateway

Services > API Gateway 

My Calculator

Under Resources Section, Click on the Actions > Create Resource.

Resource Name: calc
Resource Path: /calc

Select the /calc resource you just created. In the Actions drop down menu, choose Create Method.

In the method drop down menu, choose GET

