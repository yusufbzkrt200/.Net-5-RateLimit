# .Net-5-RateLimit
How to add RateLimit .Net 5 

What is rate limit?
Normally, an API you create is capable of receiving unlimited requests. And Denial Of Service Attack(DOS Attack) can be done by malicious people to our APIs. For this reason, we should add Rate Limit to our project.

Run this command on Package Manager Console

NuGet\Install-Package AspNetCoreRateLimit -Version 3.0.5



<-------- Startup.cs ------------>

into ConfigureServices() method ==>

services.AddOptions();
services.AddMemoryCache();
services.Configure<ClientRateLimitOptions>(Configuration.GetSection("ClientRateLimiting"));
services.Configure<ClientRateLimitPolicies>(Configuration.GetSection("ClientRateLimitPolicies"));
services.AddSingleton<IClientPolicyStore, MemoryCacheClientPolicyStore>();
services.AddSingleton<IRateLimitCounterStore, MemoryCacheRateLimitCounterStore>();
services.AddHttpContextAccessor();
services.AddSingleton<IRateLimitConfiguration, RateLimitConfiguration>();
services.AddControllers();

into Configure() method ==>

app.UseIpRateLimiting();





<-------- appsettings.json ------------>
"ClientRateLimiting": {
    "EnableEndpointRateLimiting": true,
    "StackBlockedRequests": false,
    "HttpStatusCode": 429,
    "IpWhitelist": [],
    "EndpointWhitelist": [],
    "ClientIdHeader": "x-clientId",
    "ClientWhiteList": [],
    "GeneralRules": [
        {
            "Endpoint": "*:/User/login",
            "Period": "3m",
            "Limit": 15
        },
    ],
    "QuotaExceededResponse": {
        "Content": "{{ \"data\": null,\"success\": false, \"message\": \"Please tyr again after {2} second\"}}",
        "ContentType": "application/json",
        "StatusCode": 429
    }
},

//If you want to set custom rate rules

"ClientRateLimitPolicies": {
    "IpRules": [
        {
            "Ip": "::1",
            "Rules": [
                {
                    "Endpoint": "*",
                    "Period": "4s",
                    "Limit": 3
                }
            ]
        },
    ]
}

You can now send a limited number of requests to your Endpoints





