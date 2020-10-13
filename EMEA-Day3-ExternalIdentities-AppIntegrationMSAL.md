# EMEA | External Identities | App Integration with AzureAD B2C and MSAL | October 2020 | Day 3

## B2C & App Integration with MSAL

**In this article**
- [Prerequisites](#prerequisites)
- [Lab 03: MSAL JS Angular Single-Page Application](#lab-03-msal-js-angular-single-page-application)
- [Lab 05: Create your own web app](#lab-05-create-your-own-web-app)
- [Lab 06: Create a blazor server web app calling your API](#lab-06-create-a-blazor-server-web-app-calling-your-api)
- [Lab 07: Create your own API](#lab-07-create-your-own-api)
- [Lab 08: ASP.NET (OWIN) sample](#lab-08-aspnet-owin-sample)

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

## Lab 01: MSAL JS Angular Single-Page Application

A Single-Page Application (SPA) calling a Web API.
The Web API is the MSAL Node JS Web API.
We will run both at the same time, in order to see the middleware library (Passport.js) validating the token. This will show the content learned early in the OAuth2.0 & OIDC sections.

### Run the web API

- `git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git`

- `cd active-directory-b2c-javascript-nodejs-webapi`

- Install Node.Js if you haven't already

- Install and update the Node dependencies

    `npm install && npm update`

- Run the Web API. By default, it runs on `http:localhost:5000`

    `npm start`

### Run the web app

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


## Lab 02: Create your own web app

### Install the Microsoft.Identity.Web templates in .NET Core

In this lab, we'll need to use the Microsoft.Identity.Web project templates (which are also available in .NET 5.0 from Preview 7). To install them in your system:

1. Download the [Microsoft.Identity.Web.ProjectTemplates-1.1.0](https://www.nuget.org/api/v2/package/Microsoft.Identity.Web.ProjectTemplates/1.1.0) NuGet package from NuGet.org, and save it locally
1. In a developer command prompt, type
   ```dotnetcli
   dotnet new -i Microsoft.Identity.Web.ProjectTemplates.1.1.0.nupkg 
   ```

This installs the templates, and displays which project templates are available. (You can also type `dotnet new --help` to see the list)

### Create a B2C web app that sign-in users

In a developer command prompt:

1. Create a folder, and cd into it.
   ```bash
   mkdir webapp
   cd webapp
   ```

1. Create the B2C web app
   ```dotnetcli
   dotnet new webapp2 --auth IndividualB2C ^
                     --aad-b2c-instance "https://fabrikamb2c.b2clogin.com" ^
                     --domain "fabrikamb2c.onmicrosoft.com" ^
                     --client-id "fdb91ff5-5ce6-41f3-bdbd-8267c817015d"
   ```

1. Edit the `webapp.csproj` file to replace `<TargetFrameworks>netcoreapp3.1; net5.0</TargetFrameworks>` by `<TargetFramework>netcoreapp3.1</TargetFramework>` (unless you have installed the .NET 5.0 preview framework)
1. Edit the `Properties\launchsettings.json` file so that in the IISExpress configuration, you use "sslPort": 44365, as this is one of the ports for which the redirect URIs are registered in the B2C application (5001 one is another one, and it's used by the other ASP.NET Core profile described in launchsettings.json file)
   ```Json
   {
    "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:48464",
      "sslPort": 44365
    }
   },
   // More here
   ```

1. Run your application (either from Visual Studio or from the command line with `dotnet run`)
1. Sign-in with one of your social identities or sign-up for a new local account.

> That's what it took to create a B2C application that signs-in users.
> Note that you don't need any client secret until your web app calls a web API.

### Bonus

1. In ASP.NET Core, web apps have several flavors, MVC, Razor, and Blazor Web server. You have created above a Razor assembly by using the **webapp2** template. You can also try the **mvc2** one.