---
title: Create a Communication Site and Modern Site in SharePoint Online,2019 Using
  REST API
date: 2019-08-01 20:55:00 +0530
categories: [SharePoint, Tutorial]
tags: [SharePoint Online, REST API, SharePoint 2019]
seo:
  date_modified: 2020-02-17 00:47:12 +0530
---

## Create Communication Site using REST API

Previously, In the On-Premise version of SharePoint 2013 it will taking a lots of time to create sites or site collection. But Nowadays, On SharePoint Online/ SharePoint 2019 it is very easy to create sites within one and two minutes.

In this example we will see sample code to create Communication Site in SharePoint Online using REST API with fetch api.
Endpoint Url: https://brgrp.sharepoint.com/_api/sitepages/communicationsite/create

Method: POST
Metadata: {"type":"SP.Publishing.CommunicationSiteCreationRequest"}

```js
//Payload for Creating Sites using REST API
var body={
    "request":{
        "__metadata":{"type":"SP.Publishing.CommunicationSiteCreationRequest"},
        "AllowFileSharingForGuestUsers":false,
        "Classification":"IT",
        "Description":"Here is my communication site",
        // "SiteDesignId" - use either of the below 3 options
        //"SiteDesignId":"f6cc5403-0d63-442e-96c0-285923709ffc",
        //"SiteDesignId":null,
        "SiteDesignId":"6142d2a0-63a5-4ba0-aede-d9fefca2c767",
        "Title":"My Test Communication Site",
        "Url":"https://brgrp.sharepoint.com/sites/testcommunicationsite",            
        "lcid":1033
    }
};

// Separate Request for getting RequestDigest for POST Request 
 	fetch("https://brgrp.sharepoint.com/_api/contextinfo",{ method: "POST", headers:{
        "accept":"application/json;odata=verbose",
        "content-type":"application/json;odata=verbose",
        
    }}).then(r=>r.json())
	.then(r=>
									   {

fetch("https://brgrp.sharepoint.com/_api/sitepages/communicationsite/create",{
    method: "POST",
    body: JSON.stringify(body),
    headers:{
        "accept":"application/json;odata=verbose",
        "content-type":"application/json;odata=verbose",
        "X-RequestDigest": r.d.GetContextWebInformation.FormDigestValue
    }}).then(r=>r.text()).then(r=>console.log(r))
    }); 

```

## Create a Modern Site Using REST API

As we have created Communication sites using REST API In SharePoint Online, We can also create Modern Site In SharePoint Online/ SharePoint 2019 using REST API.

To specify which type of site to create you need to use the WebTemplate attribute.
Use one of the following templates to select which type of site to create:

* Communication Site: SITEPAGEPUBLISHING#0
* non-group associated Team Site: STS#3

Reference Link : https://docs.microsoft.com/en-us/sharepoint/dev/apis/site-creation-rest

Endpoint Url: https://brgrp.sharepoint.com/_api/SPSiteManager/Create

Method: POST

```js
//Paylaod for Creating modern site using SPSiteManager REST API
var body={
   "request":{"Title":"Contoso",
 "Lcid":1033,"ShareByEmailEnabled":false
 ,"Url":"https://brgrp.sharepoint.com/sites/mdrnsite"
 ,"Classification":""
 ,"Description":""
 ,"WebTemplate":"SITEPAGEPUBLISHING#0"
 ,"WebTemplateExtensionId":"00000000-0000-0000-0000-000000000000"
 ,"HubSiteId":"00000000-0000-0000-0000-000000000000"
 ,"Owner":null}};
 
 	fetch("https://brgrp.sharepoint.com/_api/contextinfo",{ method: "POST", headers:{
        "accept":"application/json;odata=verbose",
        "content-type":"application/json;odata=verbose",
        
    }}).then(r=>r.json())
	.then(r=>
									   {
// Create Modern Site using REST API without passing metadata
fetch("https://brgrp.sharepoint.com/_api/SPSiteManager/Create",{
    method: "POST",
    body: JSON.stringify(body),
    headers:{
        "accept":"application/json;odata=verbose",
        "content-type":"application/json;odata=verbose",
        "X-RequestDigest": r.d.GetContextWebInformation.FormDigestValue
    }}).then(r=>r.text()).then(r=>console.log(r))
    }); 
 ```

Hope this will help you to create site using REST API in Sharepoint Online