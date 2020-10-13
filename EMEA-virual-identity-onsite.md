# EMA virtual identity onsite instructions

## Install the Microsoft.Identity.Web templates

```Shell
dotnet new -i Microsoft.Identity.Web.ProjectTemplates::1.1.0
```

You can uninstall them later by dotnet new -u Microsoft.Identity.Web.ProjectTemplates.

## Demo 1 - Sign-in users

### Register your app in the app registration portal

Register your app in the app registration portal, for instance using an ASP.NET Core web app quickstart.

1. Go to https://aka.ms/identityplatform
2. In the quickstart section in the TOC, choose [ASP.NET Core](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
3. Choose "Option 1" to Register and auto configure your app
4. Follow the instructions, "Make this change for me".
5. Don't download the code (it would work but is not using MIcrosoft.Identity.Web)
6. close the quickstart, but open the app you just created.
7. Note down:
- the URL to the app (will be useful to come back to it in next demos)
- in the **Overview** page,  note down the **Application (client) ID**, and the **Directory (tenant) ID** 

### Create the web app

```Shell
dotnet new webapp2 --auth SingleOrg --tenant-id youTenantId --client-id yourClientId
```

for instance you should have something like the following

```Shell
dotnet new webapp2 --auth MultiOrg  --client-id "86699d80-dd21-476a-bcd1-7c1a3d471f75" --tenant-id "7f58f645-c190-4ce5-9de4-e2b7acd2a6ab"
```

Look at the code:
- appsettings.json
- Startup.cs
- Index.cshtml.cs

### Try out the web app
- Sign-in a user
- Signout
- Sign-in another account for instance a personal account

## Demo 2 - now call Graph

### Update your app registration in the app registration portal

1. Add **API permission** for Graph ("user.read" should be there by default)
2. In **Certificates & secrets**, add an application secret, note it down

### Update your code

Since we have not changed any code, we are going to re-regenerate the app and override it (`--force`), and I'll show you the difference

```
dotnet new webapp2 --auth MultiOrg  --client-id yourClientId --tenant-id yourTenantId --calls-graph --force
```

for instance you should have something like the following

```
dotnet new webapp2 --auth MultiOrg  --client-id "86699d80-dd21-476a-bcd1-7c1a3d471f75" --tenant-id "7f58f645-c190-4ce5-9de4-e2b7acd2a6ab" --calls-graph --force
```

### Add the client secret in the appsettings.json

Add the client secret in the appsettings.json

### Run the app.

You should already be signed-in. The page now shows your display name

## Demo 3 - Call another Api

Same principle:
1. You would register a new app.
2. You create the code with the templates:

### register a web API app

In the app registration portal, create an app, and expose a scope

### Create the code

For instance:

```Shell
dotnet new webapi2 --auth SingleOrg  --client-id "a4c2469b-cf84-4145-8f5f-cb7bacf814bc" --tenant-id "7f58f645-c190-4ce5-9de4-e2b7acd2a6ab"
```

### Update the web app registration

Add an API permission to your web API

### Update the web app code

if you want to create it from scratch:

```Shell
dotnet new webapp2 --auth MultiOrg  --client-id "86699d80-dd21-476a-bcd1-7c1a3d471f75" --tenant-id "7f58f645-c190-4ce5-9de4-e2b7acd2a6ab" --called-api-url "https://localhost:5001/WeatherForecast" --called-api-scopes "api://a4c2469b-cf84-4145-8f5f-cb7bacf814bc/access_as_user" --force
```

and then add the Graph:
1. Add the Microsoft.Identity.Web.MicrosoftGraph NuGet package

2. In the Startup.cs, add .AddMicrosoftGraph

   ```CSharp
        public void ConfigureServices(IServiceCollection services)
        {
            var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

            services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)
                        .AddMicrosoftGraph()
                        .AddDownstreamWebApi("DownstreamApi", Configuration.GetSection("DownstreamApi"))
                        .AddInMemoryTokenCaches();

            services.AddAuthorization(options =>
            {
                // By default, all incoming requests will be authorized according to the default policy
                options.FallbackPolicy = options.DefaultPolicy;
            });
            services.AddRazorPages()
                .AddMvcOptions(options => {})
                .AddMicrosoftIdentityUI();
        }
   ```

3. In the index.cshtml.cs file, add the GraphServiceClient in the constructor of the Razor page

   ```CSharp
       public async Task OnGet()
        {
            var me = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["GraphResult"] = me.DisplayName ;

            using var response = await _downstreamWebApi.CallWebApiForUserAsync("DownstreamApi").ConfigureAwait(false);
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                var apiResult = await response.Content.ReadAsStringAsync().ConfigureAwait(false);
                ViewData["ApiResult"] = apiResult;
            }
            else
            {
                var error = await response.Content.ReadAsStringAsync().ConfigureAwait(false);
                throw new HttpRequestException($"Invalid status code in the HttpResponseMessage: {response.StatusCode}: {error}");
            }
        }
   ```

4. In the view, display the user

   ```html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }

    <div class="text-center">
        <h1 class="display-4">Welcome</h1>
        <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
    </div>

    <div>Api result</div>

    <div>@ViewData["GraphResult"]</div>

    <div>@ViewData["ApiResult"]</div>
   ```