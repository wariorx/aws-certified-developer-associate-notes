# Amplify
* Full web and mobile app development suite designed to accelerate app development and deployments
* Create new applications or import from existing Git repos.
* Generate **serverless** architecture in the background; developers need only be concerned with code.
* Uses **awscloudformation** as a provider to create AWS resources required
* Managed CI-CD and application development lifecycle

## Components
* **CLI**
* **Libraries / SDK** build common features such as Auth, data and file storage, and connecting your app to a backend service
* **Studio**: visual IDE
* **UI components** facilitate building application
* **Hosting**: AWS service that provides a git-based workflow to CI-cD and hosting apps
  

## Backend
* GraphQL and REST APis
* Authentication
  * Cognito
  * OAuth
  * IAM
* Storage
* Server-Side Rendering
* AWS resources
* Storage
* Analytics, Geo, PubSub and many more utilities

## Web
* JS, React, Angular, Vue, Next.js
* Setup:
1. Install in local: `npm install -g @aws-amplify/cli` and configure `amplify configure` to sign into AWS console
2. Create an IAM user or role `AdministratorAccess-Amplify` permissions 
3. Create a new app and init amplify `amplify init`
   * Creates a new directory called amplify to store **backend** definition
   * It creates an `amplifyconfiguration.js` in the source directory to hold the configuration for services created with Amplify, This allows the JS client library to get the necessariy information to conenct
   * Modifies .gitignore
4. Add amplify to dependencies `npm install aws-amplify` and configure
   ```javascript
    import { Amplify } from 'aws-amplify';
    import amplifyconfig from './amplifyconfiguration.json';
    Amplify.configure(amplifyconfig);
   ```
5. Add an API `amplify add api` and push to create resources in AWS `amplify push`


## Mobile
* Android, iOS, React Native, Ionic, Flutter
  
## Hosting existing projects