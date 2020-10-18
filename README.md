# QuiBiz

This app is used to build Quiz, which can be shared with anyone to test them on any particular topic.

#### Underlying Technologies

- AWS AppSync
- AWS Amplify
- React.js
- GraphQl
- AWS DynamoDb
- AWS Cognito

#### Resolver Concepts

AWS AppSync have a resolver concept which automatically converts the arguments defined from your GraphQL schema into DynamoDB operation (which is the data source in our case). AWS AppSync uses VTL to translate GraphQL requests from clients into a request to your data source

VTL is velocity template language and it has a lot of properties of a programming language. You can call functions on objects, you can perform loops, you can deal with data structures like maps and lists but VTL is designed to be very limited and can not be used as a full programming language. In VTL, input is all of the variables that you get such as $context and the output is a finished JSON or text document. As in the case of AppSync, we're always gonna be working with JSON documents because our GraphQL engine wants JSON out of our VTL resolvers.

#### Some example VTL Statements

Set new variable with contents "Hello!" ->  #set(\$newVar = "Hello!")

Make a new unique Id, great for creating new objects -> $util.autoId()

Error out of the template execution -> $util.error("Error Description Goes Here")

Reference Link: https://docs.aws.amazon.com/appsync/latest/devguide/resolver-util-reference.html

#### Schema

```graphql
input CreateQuestionInput {
	text: String!
	explanation: String
	answers: [AWSJSON]
}

type Mutation {
	createQuestion(input: CreateQuestionInput!): Question
}

type Query {
	getQuestion(id: ID!): Question
}

type Question {
	id: ID!
	text: String!
	explanation: String
	answers: [AWSJSON]
}
```

#### Resolver request Template

```vtl
{
    "version" : "2017-02-28",
    "operation" : "PutItem",
    "key" : {
        "id": $util.dynamodb.toDynamoDBJson($util.autoId())
    },
    "attributeValues" : $util.dynamodb.toMapValuesJson($ctx.args.input)
}
```

#### Resolver response Template

```vtl
$util.toJson($ctx.result)
```

