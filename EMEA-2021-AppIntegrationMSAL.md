# EMEA | App Integration with AzureAD B2C and MSAL | Novemeber 2021

## B2C & App Integration with MSAL

## Pre-requisites
 - [B2C Test Tenant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant). You can configure your own B2C tenant and use that configuration.
 - We'll be using Visual Studio 2022.

## Lab 01: Create your own web app

### Install the Microsoft.Identity.Web templates in .NET Core

In this lab, we'll need to use the Microsoft.Identity.Web project templates (which are also available in .NET 5.0 from Preview 7). To install them in your system:

1. Download the [Microsoft.Identity.Web.ProjectTemplates-1.20.0](https://www.nuget.org/api/v2/package/Microsoft.Identity.Web.ProjectTemplates/1.20.0) NuGet package from NuGet.org, and save it locally
1. In a developer command prompt, type
   ```dotnetcli
   dotnet new -i Microsoft.Identity.Web.ProjectTemplates.1.20.0.nupkg 
   ```

This installs the templates, and displays which project templates are available. (You can also type `dotnet new --help` to see the list)

### Create a B2C web app that sign-in users

In a developer command prompt:

1. Create a folder, and cd into it.
   ```bash
   mkdir webapp
   cd webapp
   ```

1. Create the B2C web app. Use the clientId, instance and domain for the app you created in the Azure AD B2C portal.
   ```dotnetcli
   dotnet new webapp2 --auth IndividualB2C ^
                     --aad-b2c-instance "https://fabrikamb2c.b2clogin.com" ^
                     --domain "fabrikamb2c.onmicrosoft.com" ^
                     --client-id "fdb91ff5-5ce6-41f3-bdbd-8267c817015d"
   ```

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