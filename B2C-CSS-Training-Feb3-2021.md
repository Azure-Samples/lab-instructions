# B2C CSS Training | Implicit/Authorization Code Grants and MSAL | Feb 3, 2020

**In this article**
- [Prerequisites](#prerequisites)
- [Demo 02: MSAL JS Angular Single-Page Application](#lab-02-msal-js-angular-single-page-application)
- [Demo 03: B2C web app calls web API](#lab-03-b2c-web-app-calls-web-api)
- [Extra: Project Templates](#project-templates)
- [Links to Documentation](#links-to-documentation)

## Prerequisites

Install [Node](https://nodejs.org/en/download/).

Have your favorite text editor open and ready. We'll be using Visual Studio 2019.

Ideally, install Visual Studio 2019, checking **ASP.NET and web development**, and **.NET Core cross-platform development**, or otherwise, install [ASP.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)

We'll be walking through the following pre-configured MSAL samples, if you want to `git clone` and have them ready:

[MSAL JS Single-Page Angular Application](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa)

[MSAL node JS web API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

**Optional**
The samples are pre-configured and will work "out of the box", but if you have time and interest, you can configure your own B2C tenant and use that configuration instead:
[B2C Test Tenant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant)

> The lab titles (ex. Lab 02) match the title slides in the PowerPoint given with this workshop.

## Lab 02: MSAL JS Angular Single-Page Application

A Single-Page Application (SPA) calling a Web API.
The Web API is the MSAL Node JS Web API.
We will run both at the same time, in order to see the middleware library (Passport.js) validating the token. This will show the content learned early in the OAuth2.0 & OIDC sections.

### Run the Web API

- `git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git`

- `cd active-directory-b2c-javascript-nodejs-webapi`

- Install Node.Js if you haven't already

- Install and update the Node dependencies

    `npm install && npm update`

- Run the Web API. By default, it runs on `http:localhost:5000`

    `npm start`

### Run the Web app

- `git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa.git`

- `cd active-directory-b2c-javascript-angular-spa`

- Install and update the Node dependencies

    `npm install && npm update`

- Open `src/app/app-config.ts`
- Edit Line 49:

    **replace**: `webApi: "https://fabrikamb2chello.azurewebsites.net/hello"`

    **with**: `webApi: "http://localhost:5000/hello"`
- Hit Save

- Run the Web app.

    `npm start`

    `Listening on port 6420...`

Visit `http://localhost:6420` and perform the following actions:

1) Click the Login button to start the Azure AD B2C sign in or sign up workflow.
1) Once signed in, you can click the Call Web API button to have your display name returned from the Web API call as a JSON object.
1) Click "Logout" to logout from the application.

## Lab 03: B2C web app calls web API

For the demo, we'll be using a dev app that references Microsoft Identity Web directly, but you can use a similar sample, which references Microsoft Identity Web as a nuget package.

- `git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2.git`

- `cd 4-WebApp-your-API/4-2-B2C`

Set both the Client and the TodoListService as start up projects in Visual Studio.

The sample will run as-is out of the box, but you can configure it with your own B2C tenant and policies.

## Project templates

See the Microsoft Identity Web [wiki](https://github.com/AzureAD/microsoft-identity-web/wiki/web-app-template) for directions on how to create a variety of project templates, which are great for testing customer scenarios.

We offer MVC, Razor, Blazor server, Balzor web assembly...and coming soon gPRC and Azure Functions templates.

We also have web API templates available [here](https://github.com/AzureAD/microsoft-identity-web/wiki/web-api-template).

## Links to documentation
[Overview of the Microsoft identity platform](https://aka.ms/aaddev)
[App scenarios and authentication flows](https://docs.microsoft.com/en-us/azure/active-directory/develop/authentication-flows-app-scenarios)
[Microsoft identity platform samples](https://docs.microsoft.com/en-us/azure/active-directory/develop/sample-v2-code)
[MSAL overview](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-overview)
[Microsoft Identity Web overview](https://docs.microsoft.com/en-us/azure/active-directory/develop/microsoft-identity-web)