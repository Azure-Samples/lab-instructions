# Azure AD | OAuth2.0/OIDC deep dive with MSAL SDKs, Web Apps, and Web APIs - Sept 2020 - Advanced Training Center | Day 3

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

Ideally, install Visual Studio 2019, checking **ASP.NET and web development**, and **.NET Core cross-platform development**.

We'll be walking through the following pre-configured MSAL samples, if you want to `git clone` and have them ready:

[MSAL JS Single-Page Angular Application](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa)

[MSAL node JS web API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

**Optional**
The samples are pre-configured and will work "out of the box", but if you have time and interest, you can configure your own B2C tenant and use that configuration instead:
[B2C Test Tenant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant)

> The lab titles (ex. Lab 03) match the title slides in the PowerPoint given with this workshop.

## Lab 03: MSAL JS Angular Single-Page Application

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

## Lab 05: Create your own web app

### Install the Microsoft.Identity.Web templates in .NET Core

In this Lab and the following, we'll need to use the Microsoft.Identity.Web project templates (which are also available in .NET 5.0 from Preview 7). To install them in your system:

1. Download the [Microsoft.Identity.Web.ProjectTemplates-0.4.0-preview](https://www.nuget.org/api/v2/package/Microsoft.Identity.Web.ProjectTemplates/0.4.0-preview) NuGet package from NuGet.org, and save it locally
1. In a developer command prompt, type
   ```dotnetcli
   dotnet new -i Microsoft.Identity.Web.ProjectTemplates.0.4.0-preview.nupkg 
   ```

This installs the templates, and displays which project templates are available. (You can also type `dotnet new --help` to see the list)

### Create a B2C Web app that sign-in users

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
> Note that you don't need any client secret until your web app calls a web API

### Bonus

1. In ASP.NET Core, Web apps have several flavors, MVC, Razor, and Blazor Web server. You have created above a Razor assembly by using the **webapp2** template. You can also try the **mvc2** one. We'll use the Blazor web server one later in [Lab 06: Create a blazor server web app calling your API](#lab-06-create-a-blazor-server-web-app-calling-your-api)

1. You can also create a web app with the coordinates you have registered during Day 1. In that case use `--auth SingleOrg` 

## Lab 06: Create a blazor server web app calling your API

### Goal

Create a .NET Core 3.1 (or 5.0) Blazor server (Web app) that will exercise the web API that you'll create later. For the moment, to make it easier, it will call the Web API you used in Day 1 and which is deployed in Azure

### Build the Blazor server Web App

1. Pre-requisite: you'll have installed the Microsoft.Identity.Web dotnet core templates
1. In a developer command prompt, generate a Blazor Web app calling Microsoft Graph:

   ```dotnetcli
   mkdir YourAliasCarpoolBlazorWebApp
   cd YourAliasCarpoolBlazorWebApp
   dotnet new blazorserver2 --auth SingleOrg ^
     --client-id "508ea87c-0ded-4259-ab7c-3ead66006f7b" ^
     --tenant-id "fb76fa85-e896-4a3b-b915-99764ea778ed" ^
     --domain "M365x670600.OnMicrosoft.com" ^
     --called-api-url "https://carpoolapiforatc.azurewebsites.net/" ^
     --called-api-scopes:  "api://b6128bb8-a1e3-4c69-984c-30da32c3ff0d/Rides.Read api://b6128bb8-a1e3-4c69-984c-30da32c3ff0d/Drivers.Read api://b6128bb8-a1e3-4c69-984c-30da32c3ff0d/Riders.Read"
    ```

1. Open the project in your favorite editor or Visual Studio
1. Add the client secret (ask for it) in `appsettings.json`
1. Rename `CallApi.razor` into `Riders.Razor`
1. In `Riders.Razor`
   - Change the first line to be `@page "/drivers"`
   - Change the call to CallWebApiForUserAsync() to the following:

     ```CSharp
     response = await downstreamAPI.CallWebApiForUserAsync(
                    "DownstreamApi",
                    options =>
                    {
                      options.RelativePath = "api/riders";
                      options.Scopes = "api://b6128bb8-a1e3-4c69-984c-30da32c3ff0d/Riders.Read";
                    });
     ```

1. In Shared\NavMenu.Razor, changed the CalledApi entry to be Riders:

   ```html
      <li class="nav-item px-3">
          <NavLink class="nav-link" href="riders">
              <span class="oi oi-list-rich" aria-hidden="true"></span>Riders
          </NavLink>
      </li>
   ```

### Exercise your Blazor server Web App

Just run the project, and when navigating to the "Riders", the app will sign you in, and you'll see the JSon generated by the API.

### More:

- You can add more pages for **Drivers** and **Rides** which will call the corresponding web API endpoints.

- You can change the BaseUrl to point to a deployed Web API. For this you'll need to change the `"BaseUrl"` in the `appsettings.json` to have:

  ```Json
     "BaseUrl": "https://localhost:44383" // or "https://localhost:5001" 
  ```

## Lab 07: Create your own API

### Goal

- Create the .NET Core 3.1 (or 5.0) web API that you used in Day1 (and which is also deployed at:  https://carpoolapiforatc.azurewebsites.net)
- You can see the specs for the API at: https://carpoolapiforatc.azurewebsites.net/swagger
  - `/api/drivers` (Scope  `"Drivers.Read"`), which really gets the graphs in the tenant from users, and exposes their display name and phone number (displayName, phoneNumber)
  - `/api/rides`, (Scope  `"Rides.Read"`), which returns a collection of strings ["Seattle-Redmond" ]

### Have at hand:

- `ClientIdOfTheWebApiYouCreatedInDay1` : the client ID (application ID) of the web api that you registered in Day 1 of the training.
- the client secret that you registered for this web API.

### Generate your web API

1. In a developer command prompt:
   ```@dotnetcli
   dotnet new webapi2 --auth SingleOrg --calls—graph ^
     --client-id ClientIdOfTheWebApiYouCreatedInDay1 ^
     --tenant-id "fb76fa85-e896-4a3b-b915-99764ea778ed" ^
     --domain "M365x670600.OnMicrosoft.com" ^
     --default-scope "Riders.Read"
   ```

1. Open the generated project with your preferred editor (we use Visual Studio)
1. In the `appsettings.json` file
   1. Add the client secret
   1. Change the `Scopes` to be `"User.ReadBasic.All"`
1. In the `.csproj` Replace `<TargetFrameworks>netcoreapp3.1; net5.0</TargetFrameworks>` by `<TargetFramework>netcoreapp3.1</TargetFramework>`
1. Rename `WeatherForecast.cs` to `Stakeholder.cs` and let Visual Studio rename the `WeatherForecast` class to `Stakeholder`. 
   - Replace the properties in the `WeatherForecast` class by two strings (`DisplayName` and `PhoneNumber`)
1. Rename `Controllers\WeatherForecastController.cs` to `RidersController.cs` (and let Visual Studio rename the class)
1. In `Controllers\RidersController.cs`:
   - delete `Summaries`
   - Change the signature of the `Get` method to return `IEnumerable<Stakeholder>` and delete the lines that were about the Weather forecast.
   - Return stakeholders from the users (select the `DisplayName` and `MobilePhone` properties):
  
     ```CSharp
      var users = await _graphServiceClient.Users.Request().GetAsync();
      return users.Select(u => new Stakeholder
                                { DisplayName = u.DisplayName, PhoneNumber = u.MobilePhone });
     ```

### Try out your Web API

Either from Visual Studio, or from the command line (dotnet run, and open a browser at `https://localhost:5001`)
- See the swagger for your web API
- To exercise your API, you'll need an access token. You learned in Day 1 how to get one. You can also exercise it with your Blazor app

### Add more endpoints

You can add more controllers for **Drivers** and **Rides**, based on the specification in the swagger

### Have your Web API be called by your Blazor app

To exercise your Web API from your Blazor web server application, you will need to:
- Update the appsetting.json for your Blazor web server application, to use:
  - the client ID of the web app you created in Day 1
  - the scopes and client ID of your Web API created in Day 1
  - the endpoint of your web API created in the previous lab.
- Update the Razor pages to use the right scopes (api://clientIdYourWebApi/XXX)

The equivalent command to create the blazor web server app with your app coordinates is:

```@dotnetcli
dotnet new blazorserver2 --auth SingleOrg ^
     --client-id ClientIdOfTheWebAppYouCreatedInDay1 ^
     --tenant-id "fb76fa85-e896-4a3b-b915-99764ea778ed" ^
     --domain "M365x670600.OnMicrosoft.com" ^
     --called-api-url "https://localhost:44383" ^
     --called-api-scopes:  "api://clientIdYourWebApi/Riders.Read api://api://clientIdYourWebApi/Drivers.Read api://clientIdYourWebApi/Rides.Read"
```

## Lab 08: ASP.NET (OWIN) sample

### Go to the samples page

Go to https://aka.ms/aaddevsamplesv2  (all the samples)

### Find the web app sample

Search for the ASP.NET web app and go to the sample repo ([dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2))

### Try out the sample

Follow the Readme.md, try out the sample.
