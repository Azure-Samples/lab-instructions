# DevDiv Learning Day demo instructions

### Prerequisite
Visual Studio 2019

## Demo 1: Create a protected web API using Visual Studio
### Using the new experience in VS 2019.10+, create a web API

1. Select File | New Project
2. Choose ASP.NET Core Web API
3. For **Framework**, select .NET 5
4. For **Authentication Type**, choose Microsoft identity platform
5. The connected services window appears, and starts the Microsoft identity platform configuration. Click Finish in the Required components dialogue.
6. If this is the first time you use this experience, click **Configure** on **Microsoft identity platform** in the connected services page. If this is not the first time, the page will open automatically. You'll see the list of tenants where you are a user. Choose one from the list.
7. Click the Green plus sign to create a new app in the selected tenant. Give it a name, and Click Register. The app will be created in Azure AD.
8. Click Next. Then Finish.

### Run the application

Run the application from Visual Studio.
Look at the swagger.
Try to execute it. 401 unauthenticated; This is normal. It's a protected API.

Copy the command in swagger (for instance:"https://localhost:44359/WeatherForecast") and save in Notepad.

### Look at the code

1. Observe the appsettings.json.
2. Copy the TenantId to Notepad.
2. Observe the startup.cs (web API), and the controller (Authorize attribute, scope verification)

## Understand what the tool did

1. Open the app registration portal https://portal.azure.com
2. In Azure Active Directory | Application registration, find the application
3. Look at the Expose an API page, and copy the scopes (for instance: "api://4d36eb86-0d46-4090-9283-c42dc9f149ef/access_as_user")

**At this point, you should have three copied items, the URL to the web API from swagger, the scopes from the Azure Portal, and the TenantId Guid from the web API**

## Demo 2: Create a web app which calls the web API from Demo 1

### Create the web app from Visual Studio

1. In Visual Studio right click on the solution and Select **Open in terminal**

   `mkdir webApp`

   `cd webApp`

2. Create the web app by typing `dotnet new webapp --auth SingleOrg --called-api-url` followed by the URL of your web API, and followed by `--called-api-scopes` followed by the scopes of your web API. This will be something like this:
 
   ```Shell
   dotnet new webapp --auth SingleOrg --called-api-url https://localhost:44359/WeatherForecast --called-api-scopes api://4d36eb86-0d46-4090-9283-c42dc9f149ef/access_as_user
   ```
3. Still in the console add the new project to the solution using `dotnet sln (add)`:
   
   ```Shell
   dotnet sln ..\myWebAPI.sln add .\myWebApp.csproj
   ```

### Configure the web app

1. Install the [command line tool](https://github.com/AzureAD/microsoft-identity-web/tree/master/tools/app-provisioning-tool)

    `dotnet tool install --global msidentity-app-sync`

2. Register and create the web app in the Azure AD portal and update the code by providing the same TenantId from the web API, so they are both registered in the same tenant.

    `msidentity-app-sync --tenant-id testprovisionningtool.onmicrosoft.com`

### Look at the code

1. Observe the appsettings.json. See the client secret, right click on the project, it's under "Manage User Secrets".
2. and the startup.cs
3. and the index.cshtml.cs page which calls the web API.

### Run the web app and web API

1. Setup the startup project in the solution to start both projects
1. Run the solution. The web app signs-in the user and displays the result of the API.