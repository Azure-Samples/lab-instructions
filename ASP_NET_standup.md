# Demo of the app provisioning tool

## Create and configure the web API in Visual Studio

### Create the web API

1. Select File | New Project
2. Choose web API
3. For Authentication, choose Microsoft identity platform
4. Observe the appsettings.jon. We need to configure the project
5. Observe the startup.cs (web api), and the controller (Authorize attribute, scope verification)

### Learn about the new tooling

1. Navigate the aka.ms link: https://aka.ms/dotnet-template-ms-identity-platform and learn:
   - how to install the tool (dotnet global tool)
   - how to install it in Visual Studio as an external tool

## Configure the web API by registering an app

Select the project
Make sure the appsettings.json is displayed
Select **Tools | msidentity-app-sync**
Observe the appsettings.json, the ClientId and TenantId were modified

## Run the application

Run the application from Visual Studio
Look at the swagger
Try to execute it. 401 unauthenticated; This is normal. It's a protected API

Copy the command in swagger (for instance:"https://localhost:44359/WeatherForecast")

## Understand what the tool did

1. Open the app registration portal https://portal.azure.com
2. In Azure Active Directory | Application registration, find the application
3. Look at the Expose an API page, and copy the scopes (for instance: "api://4d36eb86-0d46-4090-9283-c42dc9f149ef/access_as_user")

## Create and configure the web app

### Create the web app from dotnet core

1. In Visual Studio right click on the solution and Select **Open folder in file explorer**
2. In File explorer, New Folder, named **MyWebApp**
3. In File explorer, right click on the new folder and **Open a terminal command**
4. In the terminal command create the web app by typing `dotnet new webapp --auth SingleOrg --called-api-url` followed by the URL of your web API, and followed by `--called-api-scopes` followed by the scopes of your web API. This will be something like this:
 
   ```Shell
   dotnet new webapp --auth SingleOrg --called-api-url https://localhost:44359/WeatherForecast --called-api-scopes api://4d36eb86-0d46-4090-9283-c42dc9f149ef/access_as_user
   ```
5. Still in the console add the new project to the solution using `dotnet sln (add)`:
   
   ```Shell
   dotnet sln ..\myWebAPI.sln add .\myWebApp.csproj
   ```

### Look at the code

1. Back to visual studio, the solution was modified by adding the web app.
2. Observe the appsettings.json. See the client secret.
3. and the startup.cs
4. and the index.cshtml page which calls the web API.

### Run the tool

1. Select the web app project
2. Run the tool

### Run the web app and web APIs

1. Setup the startup project in the solution to start both projects
1. Run the solution. The web app signs me in and displays the result of the API.
