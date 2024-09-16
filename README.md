
# Backstage JBBKJ.cloud

### Setting up the project

1. Create a backstage app using the following command.
```
npx @backstage/create-app@latest
```
```
Enter y to proceed
```
```
Enter a name for your app and hit enter
```
You will see an interface like this

<img width="532" alt="Backstage App Set up" src="https://github.com/user-attachments/assets/3541f913-8439-498c-bc66-72923c07b691">


2. To run backstage app
```
cd my-backstage-app
```
```
yarn dev
```
Folder structure
```
app
├── app-config.yaml
├── catalog-info.yaml
├── package.json
└── packages
    ├── app
    └── backend
```

### Integrating github apps

1. Setting up a github apps.
```
yarn backstage-cli create-github-app <github org>
```
After running this command you will see an interface like this

![Github Apps Set Up 1](https://github.com/user-attachments/assets/afbc7a6a-7bbb-4a39-9dab-aff6df6fd7af)

Make sure to select all options using the enter and arrow keys to scroll up and down.

`
After selecting all permission hit enter 
`

You will be redirected to browser where it may ask for your github password to create github apps. Enter your password.

After entering password, it will ask you to enter the name for the github app. Make sure name to be unique globally

![Github Apps Setup 2](https://github.com/user-attachments/assets/dcf8f18b-d070-4676-a6d0-49b9812a1259)


`
Hit create github app for <Your-Org-Name>
`

After that it will ask you to install github app on <your-org-name>
Remain everything default and hit install.

![Github Apps Setup 3](https://github.com/user-attachments/assets/f25183b5-6882-4398-b574-546311b2a584)

After successfully installing app it will show an interface like this 

![Github Apps Setup 4](https://github.com/user-attachments/assets/7d8773c7-056f-4cf8-881d-b661ea3522ba)

Click on App Settings 

It will redirect you to general page of your github app.

Now we need to app the Homepage url and callback url

```
Homepage URL: http://localhost:3000
Callback URL: http://localhost:7007/api/auth/github/handler/frame
```

![Github Apps Setup 5](https://github.com/user-attachments/assets/a12cd9b1-c079-4c20-a8e0-ac5819355b4b)

After that click on the `save changes` button.

Now go to `Permissions and Events` page by clicking on the menu present on left.

We need the following permission for integrating github apps for backstage

```
Reading software components:

Contents: Read-only
Commit statuses: Read-only

```
```
Reading organization data:

Members: Read-only
```

```
Publishing software templates:

Administration: Read & write (for creating repositories)
Contents: Read & write
Metadata: Read-only
Pull requests: Read & write
Issues: Read & write
Workflows: Read & write (if templates include GitHub workflows)
Variables: Read & write (if templates include GitHub Action Repository Variables)
Secrets: Read & write (if templates include GitHub Action Repository Secrets)
Environments: Read & write (if templates include GitHub Environments)
```
Give all these permissions

![Github Apps Setup 6](https://github.com/user-attachments/assets/e0d08fa4-105a-4185-a1bc-5adcf235586b)

After that click on the `save changes` button.

Now go to settings of <your-org>

 ` Settings  -> Third-party Access -> Github Apps`

It will ask you to review request for the updated permission

![Github Apps Setup 7](https://github.com/user-attachments/assets/31d6cf2c-a7da-4d16-ac7d-f5f4ff64776f)

Click on the `review request`.

![Github Apps Setup](https://github.com/user-attachments/assets/992f4ef5-f079-46d3-852e-0aec9fd856bd)

Click on `Accept new permissions`.

2. Setting up Github Apps in backstage
 
 In your backstage project go to app-config.yaml

Inside the app-config.yaml, go to integrations section and remove the integrations section

```
integrations:
  github:
    - host: github.com
```

 And replace it with this 

 ```
 integrations:
  github:
    - host: github.com
      apps:
        - appId: ${AUTH_ORG_APP_ID}
          clientId: ${AUTH_ORG_CLIENT_ID}
          clientSecret: ${AUTH_ORG_CLIENT_SECRET}
          privateKey: ${AUTH_ORG1_PRIVATE_KEY}
          webhookSecret: ${AUTH_ORG_WEBHOOK_SECRET}
 ```
 
Create a .env file containing the these environment variables.

```
export AUTH_ORG_APP_ID=<your-app-id>
export AUTH_ORG_CLIENT_ID=<your-client-id>
export AUTH_ORG_CLIENT_SECRET=<your-client-secret>
export AUTH_ORG1_PRIVATE_KEY=<your-private-key>
export AUTH_ORG_WEBHOOK_SECRET=<your-webhook-secret>
```

You will find the values of these inside the `github-app-your-app-name-credentials.yaml`

Make sure to make the private key into a one line key by adding the `\n` instead of line break. 

 ```  
 export AUTH_ORG1_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
        MIIEpAIBAAKCAQEAw2vUdpBG65coOTbXj240xJKjJRAHF7lMSz4ECWuNvfZhNYtQ
        QwrcVzRyYqfYEJCCwL+W29PePCj8AK2tsQS2Wcj/kB+Dc1TSdUhPU65ZIgSqwoac
        s7UNLA6LqfazcEZjN57ky3lFlGBNACR8ypaua9IQQfPLO92oX2LDVMgCo2nagefM
        tQ1O0JESo7+zkg62fNSdbl3mXuZkl+QTvxlFeDlmUD9z7Qkbdq961DEalKimkNyp
        -----END RSA PRIVATE KEY-----" 
```

It should be like this 

```
export AUTH_ORG1_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\nMIIEpAIBAAKCAQEAw2vUdpBG65coOTbXj240xJKjJRAHF7lMSz4ECWuNvfZhNYtQQwrcVzRyYqfYEJCCwL+W29PePCj8AK2tsQS2Wcj/kB+Dc1TSdUhPU65ZIgSqwoacs7UNLA6LqfazcEZjN57ky3lFlGBNACR8ypaua9IQQfPLO92oX2LDVMgCo2nagefMtQ1O0JESo7+zkg62fNSdbl3mXuZkl+QTvxlFeDlmUD9z7Qkbdq961DEalKimkNyp\n-----END RSA PRIVATE KEY-----"
```

3. Setting up Authentication in backstage

Inside the app-config.yaml -> `auth`


```
auth:
  # see https://backstage.io/docs/auth/ to learn about auth providers
  providers:
    # See https://backstage.io/docs/auth/guest/provider
    guest: {}

```

Replace it with this 

```
auth:
  environment: development
  providers:
    github:
      development:
        clientId: ${AUTH_ORG_CLIENT_ID}
        clientSecret: ${AUTH_ORG_CLIENT_SECRET}
        ## uncomment if using GitHub Enterprise
        # enterpriseInstanceUrl: ${AUTH_GITHUB_ENTERPRISE_INSTANCE_URL}
        signIn:
          resolvers:
            # Matches the GitHub username with the Backstage user entity name.
            # See https://backstage.io/docs/auth/github/provider#resolvers for more resolvers.
            - resolver: usernameMatchingUserEntityName
```

Now we need to setup backstage to fetch org data to check whether sign in user belong to a particular org or not.

#### To integrate backstage to fetch org data.

Inside app-config.yaml -> `catalog`

Below import section Add this 

```
providers:
    githubOrg:
      id: development
      githubUrl: https://github.com
      orgs: ['<your-org-name>']
      schedule:
        initialDelay: { seconds: 30 }
        frequency: { minutes: 10 }
        timeout: { minutes: 10 }
```

Go to `packages -> backend -> src -> index.ts`

Add following statements

```
backend.add(import('@backstage/plugin-auth-backend-module-github-provider'));
backend.add(import('@backstage/plugin-catalog-backend-module-github-org'));
```

We need to add `@backstage/plugin-catalog-backend-module-github-org` as dependency to `packages/backend`

```
cd packages/backend
yarn add @backstage/plugin-catalog-backend-module-github-org
```
#### Adding sign-in page 

Go to `packages -> app -> App.tsx`

Inside the `app -> components `

Replace  existing `SignInPage` with this

```
SignInPage: props => (
      <SignInPage
        {...props}
        auto
        provider={{
          id: 'github-auth-provider',
          title: 'GitHub',
          message: 'Sign in using GitHub',
          apiRef: githubAuthApiRef,
        }}
      />
    ),
```

Add this import statement at the top.

```
import { githubAuthApiRef } from '@backstage/core-plugin-api';
```

We are good to go. Test your app by using the below command.

```
yarn dev
```
