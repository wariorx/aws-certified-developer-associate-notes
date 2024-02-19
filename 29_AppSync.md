# AppSync
* Serverless GraphQL and Pub/Sub API service
* Encryption in flight
* Highly availalbe and fault tolerant
* You may create **Private APIs** within a VPC if needed, then access them using AWS Private Link similar to Private API Gateways

## Access Control
* Supports primary and secondary authorization forms
* Support several featues, similar to lambda:
* **API Key**: can be used to identify users and enforce throttling. Can be rotated. Last up to 365 days and can be extended
* **Lambda**: implement custom authorization logic. Only 1 lambda per API; though it can be combined with other forms:
  * IAM + LAMBDA: SigV4 cannot be used as the auth token for the lambda\
  * Lambda + OpenID_Connect OR Cognito: OIDC token cannot be used as the lambda auth token (IE Bearer scheme not allowed)
    * You may use prefixes or suffixes to still use sigv4 or OpenIdm then have the lambda remove them when needed
  * Must not retorn mroe than 5MB of contextual data for resolvers
  * `appsync.amazonaws.com` Principal must be allowed to call the lambda
    Example response - `isAuthorized` bool is the min required:
    ```json
        {
        "isAuthorized":true
        "resolverContext": {
            "banana":"very yellow",
            "apple":"very green" 
        }
     }
    ```
  * Responses will be cached bsed on the value of `ttlOverride`  in the response
  * Lambda will timeout in 10s
* **IAM**: enforces SigV4 signign process on the API
  * Example role:
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "appsync:GraphQL"
                ],
                "Resource": [
                    "arn:aws:appsync:us-west-2:123456789012:apis/{YourGraphQLApiId}/*",
                    "arn:aws:appsync:us-west-2:123456789012:apis/YourGraphQLApiId/types/Query/fields/<Field-1>"
                ]
            }
        ]
    }
    ```
* **OpenID Connect**: APpSync only requires the Issuer URL to be provided to AppSync to be able to verify the token 
* **Cognito User Pools**: enforces OIDC tokens provided by cognito User Pools, you may leverage users and groups for controlling access as this information will be encoded in the JWT


### Fine-Grained Access control
* **Directives**: you can configure AppSync to use a default authentication mode, then use directives to specify an override for additional access restrictions against individual **fields**:
    ```typescript
    type Query {
    getPost(id: ID): Post
    getAllPosts(): [Post]
    @aws_api_key
    }
    ```
* **Conditional checks** can be added to resolver logic to performa additional checks before performing an operation. For example, you could prevent users that are not authors from editing a post as follows:
    ```javascript
    import { util, Context } from '@aws-appsync/utils';
    import { put } from '@aws-appsync/utils/dynamodb';

    export function request(ctx) {
        const { id, ...item } = ctx.args;
        return put({
            key: { id },
            item,
            condition: { author: { contains: ctx.identity.username } },
        });
    }

    export const response = (ctx) => ctx.result;
    ```
* **Filtering information**: you canm also prevent certain information from being returned to users by filtering responses out as follows:
    ```javascript
    import { util, Context } from '@aws-appsync/utils';
    import { get } from '@aws-appsync/utils/dynamodb';

    export function request(ctx) {
        return get({ key: { ctx.args.id } });
    }

    export function response(ctx) {
        if (ctx.result.author === ctx.identity.username) {
            return ctx.result;
        }
        return null;
    }
    ```

## Data Source Access
* AppSync uses **IAM Roles and access policies** to authenticate itself against data sources. to do so, it must be able to assume the role through a trust policy like the following:
    ```JSON
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": {
                    "Service": "appsync.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
            }
        ]
    }
    ```

## Logging
* Integrated with **Cloud Trail**; track events, export events to S3 bucket 
* Calls to the APIs and calls to resolvers **are not** logges to Cloudtrail; only calls to AppSyncAPI

## GraphQL
* Single data serverless API that simplifies application develipment with GraphQL APIs 
* Securely query or update multiple datasources from a single secure endpoint
* Unify multiple datasources behind a single, serverless API
### Elements
* **Schema**: blueprint tha defines the shape of data; a contract between client and server that defines how data will be retrieved and/or modified
  * Written in **Schma Definition Language(SDL)**:
    * **Types**: defines the shape and behavior of data. Each type contains its own scope comprised of one or more fields that contain a value or logic
      * **Objects**: contains fields describing an entity; IE book contains string title. Strictly OUTPUT types
      * **Scalars**: primitive types, like int or string. Typically assigned to fields
        ```typescript
        type Person { 
            id: ID!
            name: String
            age: Int
            occupation: Occupation
        }
        ```
      * **Inputs**: allow you to pass a group of fields as an argument. Structured similar to objects, but can be passed in as arguments to special objects. Can only be INPUT types
        ```typescript
        input personInput { 
            id: ID!
            name: String
            age: Int
            occupation: occupationInput
        }
        ```
      * **Enums**: predefined lists of legal values. Can be bot INPUT and OUTPUT types
        ```typescript
        enum trafficSignals {
            solidRed
            solidYellow
            solidGreen
            greenArrowLeft
        }
        ```
      * **Unions**: unions allow you to return one or more things in a request depending on the data requested; IE book and author union Can only be OUTPUT types
        ```
        union SearchResult = Conference | Festival | Concert | Venue
        ```
          * **Type Resolution**: helps the GraphQL engine determine which type an object result of a Union or Interface is; defined by the `__typename` meta field. For Example, for the following data, a resolver could use the following logic to determine the Type:
            ```json
            {
            "version" : "2018-05-29",
            "payload":
                [
                    {
                        "id": "Venue-1",
                        "name": "Venue 1",
                        "address": "2121 7th Ave, Seattle, WA 98121",
                        "maxOccupancy": 1000
                    },
                    {
                        "id": "Festival-2",
                        "name": "Festival 2",
                        "performers": ["The Singers", "The Screamers"]
                    },
                    {
                        "id": "Concert-3",
                        "name": "Concert 3",
                        "performingBand": "The Jumpers"
                    },
                    {
                        "id": "Conference-4",
                        "name": "Conference 4",
                        "speakers": ["The Storytellers"],
                        "workshops": ["Writing", "Reading"]
                    }
                ]
            }
            ```
            ```
            #foreach ($result in $context.result)
            ## Extract type name from the id field.
            #set( $typeName = $result.id.split("-")[0] )
            #set( $ignore = $result.put("__typename", $typeName))
            #end
            $util.toJson($context.result)
            ```

      * **Interfaces**: sets of fields tha objects MUST implement. For example a Book interface that defines a title would enforce that upon its child Object novel. Can only be OUTPUT types
        ```typescript
        interface Event {
            id: ID!
            name : String!
            startsAt: String
            endsAt: String
            venue: Venue
            minAgeRestriction: Int
        }

        type Concert implements Event {
            id: ID!
            name: String!
            startsAt: String
            endsAt: String
            venue: Venue
            minAgeRestriction: Int
            performingBand: String
        }
        ```
    * **Fields**: exist within the scope of a type and hold the value that's requested from the GraphQL. They determine how the data is structured in the request/response process
        ```typescript
            type Person {                                  
                id: ID!
                name: String                                  
                age: Int
            },
            type Query {                                   
                people: [Person]
            },
            type Mutation {
                addPerson(id: ID!, name: String, age: Int): Person
            }
        ```
  * ***Mutation**: modifies data; IE add persons to the database
  * **Query**: retrieves data from the source; IE Persons
* **Data Sources**: contain the data that will is mapped by the schema
  * DynamoDB
  * Lambda
  * OpenSearch
  * HTTP
  * EventBridge
  * Relational Databases
  * No Source
* **Resolvers**: define how the schema and hte data sources interact; aka how each field of data is resolved when a request is made
  * Specify a **runtime** for the resolver:
    * APPSYNC_JS
    * Velocity Template Language (VTL)
  * Types:
    * **Unit Resolvers**: code that defines a single request and response handler to be executed against the data source
      * Request(): takes the context object and returns teh request payload for the datasource
      * Response(): receives the payload from teh datasource and transforms it into GraphQL
    * **Pipeline Resolvers** :
      * **Before step**: begin processing the request data for preprocessing operations before data is moved through the resolver
      * **Function(s)**: receives the result of the Before step and executes it against a data source. Contains its own request and response handlers. There can be more than one, in which case each function will receive the request data  and perform its own operations serially
      * **After step**: performs final operations on the final function's response before passing it ot the GraphQL response
    ```typescript
        export function request(ctx) {
             return {}
        }

        export function response(ctx) {
            return "Hello World"
        }
    ```
    The `ctx` parameter contains information on the auth credentials, data, errors, request meta data, etc. It's meant to be an easy way to manipulate data coming into and out of the resolver.

## Pub/sub
* Push data updates to subscribed client applications for real time use cases
* AppSync provides its own built-in local resolvers or your own custom ones
* Similar to GraphQL, Pub/sub APIs also use objects, queries, resolvers, etc,
    ```typescript
    type Channel {
        name: String!
        data: AWSJSON!
    }

    type Mutation {
        publish(name: String!, data: AWSJSON!): Channel
    }

    type Query {
        getChannel: Channel
    }

    type Subscription {
        subscribe(name: String!): Channel
            @aws_subscribe(mutations: ["publish"])
    }
    ```
* For clients to receive messages, they must be subscrived to  an inbox:
    ```typescript
    subscription SubscribeToData {
        subscribe(name:"channel") {
            name
            data
        }
    }
    ```
    ^ Subscribe to data on the channel named channel
  * The subscriber will receive messages wheneber the publish mutation is invoked as long as it's sent to the `channel` subscription
  * You may also **unsubscribe** a user forcibly in certain scenarios by invalidating their subscription, for example when they've been inactive for too long

### Filtering
* **Basic** based on client-defined arguments in the subscription query
* **Advanced** based on logic defined centrally in the AppSync backend on, for example, resolvers

