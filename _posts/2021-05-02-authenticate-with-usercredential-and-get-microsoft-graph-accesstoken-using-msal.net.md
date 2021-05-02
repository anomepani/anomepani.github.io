---
title: Authenticate with UserCredential and Get Microsoft Graph AccessToken Using MSAL.NET
date: 2021-05-02 16:30:00 +0530
categories: [MSAL.NET, Tutorial]
tags: [MicrosoftGraph,MSAL.NET]
---

## Authenticate with UserCredential and Get Microsoft Graph AccessToken Using MSAL.NET

##  Introduction
MSAL.NET is Microsoft Unified Identity SDK which supports all Modern authentication.
In this article we will learn how to get microsoft graph access token using UserCredential flow with MSAL.NET in C#.

In some cases if some Microsoft Graph access only avaialable using Delegated Permission then we can use Username and password flow.

> Note: Username and password based authentication must be prevented for Best Practice and Security Reason

## Pre-requisite
I am assuming you have knowledge about how to do App Registration in Azure and Add Delegated permission and Admin Consent is given if requires based on Organization Policy.

I have used and tested functionality with Nuget package `Microsoft.Identity.Client` version **"4.4.0"** in Console App

## C# Code for Usernamr and password based authentication flow

Created Separate `CommonCredentials.cs` file for common configuration and reusability.
This Configuration must be read from App.config or Web.config, For Demo purpose I have written in code for simplicity.

```cs  CommonCredentials.cs
using System;

namespace Common
{
    public class CommonCredentials
    {
        public static string UserName = "anomepani@brgrp.onmicrosoft.com";
        public static string Password = "****";
        public static string ClientId = "57fbc2a2-1188-4ed0-aaf2-daca857d6957";
    }
}

```

`PublicAppUsingUsernamePassword.cs` file contains code for getting token using username and password.

We can implement TokenCache functionality for daemon application or background application in this file.

```cs PublicAppUsingUsernamePassword.cs
using Microsoft.Identity.Client;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security;
using System.Threading.Tasks;

namespace MSALUtils
{
    /// <summary>
    /// Security token provider using username password.
    /// Note that using username/password is not recommended. See https://aka.ms/msal-net-up
    /// </summary>
    public class PublicAppUsingUsernamePassword
    {
        /// <summary>
        /// Constructor of a public application leveraging username passwords to acquire a token
        /// </summary>
        /// <param name="app">MSAL.NET Public client application</param>
        /// <param name="httpClient">HttpClient used to call the protected Web API</param>
        /// <remarks>X  
        /// For more information see https://aka.ms/msal-net-up
        /// </remarks>
        public PublicAppUsingUsernamePassword(IPublicClientApplication app)
        {
            App = app;
        }
        protected IPublicClientApplication App { get; private set; }

        /// <summary>
        /// Acquires a token from the token cache, or Username/password
        /// </summary>
        /// <returns>An AuthenticationResult if the user successfully signed-in, or otherwise <c>null</c></returns>
        public async Task<AuthenticationResult> AcquireATokenFromCacheOrUsernamePasswordAsync(IEnumerable<String> scopes, string username, SecureString password)
        {
            AuthenticationResult result = null;
            var accounts = await App.GetAccountsAsync();

            if (accounts.Any())
            {
                try
                {
                    // Attempt to get a token from the cache (or refresh it silently if needed)
                    result = await (App as PublicClientApplication).AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException)
                {
                    // No token for the account. Will proceed below
                }
            }

            // Cache empty or no token for account in the cache, attempt by username/password
            if (result == null)
            {
                result = await GetTokenForWebApiUsingUsernamePasswordAsync(scopes, username, password);
            }

            return result;
        }

        /// <summary>
        /// Gets an access token so that the application accesses the web api in the name of the user
        /// who is signed-in Windows (for a domain joined or AAD joined machine)
        /// </summary>
        /// <returns>An authentication result, or null if the user canceled sign-in</returns>
        private async Task<AuthenticationResult> GetTokenForWebApiUsingUsernamePasswordAsync(IEnumerable<string> scopes, string username, SecureString password)
        {
            AuthenticationResult result = null;
            try
            {
                result = await App.AcquireTokenByUsernamePassword(scopes, username, password)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
            }
            return result;
        }
    }
}

```

```cs Program.cs
using Common;
using Microsoft.Identity.Client;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security;
using System.Text;
using System.Threading.Tasks;

namespace MSALUtils
{
    /// <summary>
    /// Reference From : https://github.com/Azure-Samples/active-directory-dotnetcore-console-up-v2
    /// </summary>
    class Program
    {
        static void Main(string[] args)
        {
            var ClientId =CommonCredentials.ClientId;
            var app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdMultipleOrgs).Build();
            string username = CommonCredentials.UserName;
           // Console.Write("Enter Password: ");
            string pwd = CommonCredentials.Password;
            SecureString password = new SecureString();
            foreach (char c in pwd)
                password.AppendChar(c);
            var Scopes = new string[] { "https://graph.microsoft.com/.default" };
            Console.WriteLine("** Making Request to get GraphToken Using MSAL.NET ** \n");
            //var result =app.AcquireTokenByUsernamePassword(Scopes, username, password).ExecuteAsync().GetAwaiter().GetResult();

            #region Store MS GraphToken In Memory Caching With Username and Password flow
            var App = new PublicAppUsingUsernamePassword(app);
             var result = App.AcquireATokenFromCacheOrUsernamePasswordAsync(Scopes, username, password).GetAwaiter().GetResult();
            #endregion

            if (result != null)
            {
                Console.WriteLine("### RECEIVED TOKEN GraphToken Using MSAL.NET ###  \n  \n ");
                Console.WriteLine(result.AccessToken);
                var spClient = new HttpClient();
                spClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                spClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                spClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
                var res = spClient.GetStringAsync("https://graph.microsoft.com/v1.0/me").Result;
                Console.WriteLine("\n** MS Graph Received Result **\n\n");
                Console.WriteLine(res);
            }
            else
            {
                Console.WriteLine("### NOT RECEIVED TOKEN ###");
            }
            Console.ReadKey();
        }
    }
}
```

## Conclusion
Microsoft have changed lots of improvement and biggest change in  `Microsoft.Identity.Client` Microsoft Authentication Library with complete re-write several times.

So It may be possible that latest version of `Microsoft.Identity.Client` may updated with new class which I didn't tested.

Hope this Code snippet will helpful to you.

Complete SourceCode you can find from [GitHub MSALUtils](https://github.com/anomepani/Microsoft365-Auth-Utility/tree/master/MSALUtils)