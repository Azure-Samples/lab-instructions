# EMEA | App Integration with AzureAD B2C and MSAL | Novemeber 2021

## B2C & App Integration with MSAL

## Pre-requisites
 - [B2C Test Tenant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant). You can configure your own B2C tenant and use that configuration, or use our client id and tenant below.
 - We'll be using Visual Studio 2022.

## Lab 01: Create your own web app

### Create a B2C web app that sign-in users

In a developer command prompt:

1. Create a folder, and cd into it.
   ```bash
   mkdir webapp
   cd webapp
   ```

1. Create the B2C web app in your tenant. Use the clientId, instance and domain for the app you created in the Azure AD B2C portal.
   ```dotnetcli
   dotnet new webapp --auth IndividualB2C ^
                     --aad-b2c-instance "https://fabrikamb2c.b2clogin.com" ^
                     --domain "fabrikamb2c.onmicrosoft.com" ^
                     --client-id "c7498b97-a168-4eef-8d4c-d45d86c95cc3"
   ```

1. Edit the `Properties\launchsettings.json` file so that in the IISExpress configuration, you use "sslPort": 44365, as this is one of the ports for which the redirect URIs are registered in the B2C application (5001 one is another one, and it's used by the other ASP.NET Core profile described in launchsettings.json file)
   ```Json
   {
    "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:5000",
      "sslPort": 5000
    }
   },
   // More here
   ```

1. Run your application (either from Visual Studio or from the command line with `dotnet run`)
1. Sign-in with one of your social identities or sign-up for a new local account.

> That's what it took to create a B2C application that signs-in users.
> Note that you don't need any client secret until your web app calls a web API.


### Event simpler

Alternatively to steps 2 and 3, you can also let the [msidentity-app-sync](https://github.com/AzureAD/microsoft-identity-web/blob/master/tools/app-provisioning-tool/README.md) tool create the application for you in the B2C tenants, based on the code.

   ```dotnetcli
   mkdir emea2021
   cd emea2021
   dotnet tool install --global msidentity-app-sync
   dotnet new webapp --auth IndividualB2C ^
                     --aad-b2c-instance "https://fabrikamb2c.b2clogin.com" ^
                     --domain "fabrikamb2c.onmicrosoft.com"
   msidentity-app-sync
   ```


### Bonus

1. In ASP.NET Core, web apps have several flavors, MVC, Razor, and Blazor Web server. You have created above a Razor assembly by using the **webapp** template. You can also try the **mvc** one.