# web-backend (SAM Application)

- src - Code for the application's Lambda function.
- \_\_tests__ - Unit tests for the application code.
- template.yml - A SAM template that defines the application's AWS resources.
- buildspec.yml -  A build specification file that tells AWS CodeBuild how to create a deployment package for the function.

## Try the application out

The sample application creates a RESTful API that takes HTTP requests and invokes Lambda functions. The API has POST and GET methods on the root path to create and list items. It has a GET method that takes an ID path parameter to retrieve items. Each method maps to one of the application's three Lambda functions.

**To use the sample API**

1. Choose your application from the [**Applications page**](https://console.aws.amazon.com/lambda/home#/applications) in the Lambda console. (Make sure you're in the right region)
1. Copy the URL that's listed under **API endpoint**.
1. At the command line, use cURL to send POST requests to the application endpoint.

        $ ENDPOINT=<paste-your-endpoint-here>
        $ curl -d '{"id":"1234ABCD", "name":"My item"}' -H "Content-Type: application/json" -X POST $ENDPOINT
        {"id":"1234ABCD","name":"My item"}
        $ curl -d '{"id":"2234ABCD", "name":"My other item"}' -H "Content-Type: application/json" -X POST $ENDPOINT
        {"id":"2234ABCD","name":"My other item"}

1. Send a GET request to the endpoint to get a list of items.

        $ curl $ENDPOINT
        [{"id":"1234ABCD","name":"My item"},{"id":"2234ABCD","name":"My other item"}]

1. Send a GET request with the item ID to get a single item.

        $ curl $ENDPOINT/1234ABCD
        {"id":"1234ABCD","name":"My item"}

To view the application's API, functions, and table, use the links in the **Resources** section of the application overview in the Lambda console.

## Lint all '*.js' files

Requirements:

* Node.js - [Install Node.js 12.x](https://nodejs.org/en/), including the npm package management tool.

Use `npm` to install the [Jest test framework](https://jestjs.io/) and run lint.

```bash
$ npm run lint
```

## Unit tests and collect coverage

Requirements:

* Node.js - [Install Node.js 12.x](https://nodejs.org/en/), including the npm package management tool.

Tests are defined in the \_\_tests__ folder in this project. Use `npm` to install the [Jest test framework](https://jestjs.io/) and run unit tests.

```bash
my-application$ npm install
my-application$ npm run test
```

## Resources

For an introduction to the AWS SAM specification, the AWS SAM CLI, and serverless application concepts, see the [AWS SAM Developer Guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html).

Next, you can use the AWS Serverless Application Repository to deploy ready-to-use apps that go beyond Hello World samples and learn how authors developed their applications. For more information, see the [AWS Serverless Application Repository main page](https://aws.amazon.com/serverless/serverlessrepo/) and the [AWS Serverless Application Repository Developer Guide](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/what-is-serverlessrepo.html).
