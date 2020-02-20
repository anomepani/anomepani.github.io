---
title: SPOHelper - SharePoint Online REST API CRUD Operation Utility with Example
date: 2020-01-12 8:48:00 +0530
categories: [SharePoint, Tutorial]
tags: [SharePoint Online, SharePoint 2019, REST API, SPOHelper]
seo:
  date_modified: 2020-02-17 00:47:12 +0530
---

##  Introduction

On Daily basis, Most of developer are using `fetch` API or jQuery `$.ajax` for making Sharepoint Online REST API Request.

To use `$.ajax` it requires external depenedency `jQuery`, and `fetch` API Available in Modern Browsers and polyfill available as well for older browser like IE11.

However even if we are making Sharepoint REST API Request using `fetch` or `$.ajax` every time we need to setup `Odata` Headers like Accept, Content Type, Credentials  and RequestDigest Headers for each request based on GET, POST etc operation.

To Remove this type of duplicate header configuration and repetetive boilerplate code,  I have created light weight REST API Utility [`SPOHelper.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SPOHelper.ts) for faster SPFX Development.

## Usage of SPOHelper Utility

If you are developing with Typescript first you need to import requried methods as below.

```js
import {SPGet, SPDelete, SPPost, SPUpdate} from "./SPOHelper";
```

`SPGet` and `SPDelete` method accepts single parameter `url`

`SPPost(options)`, `SPUpdate(options)` method accepts single JSON object as parameter which have multiple json property.
`options.url` which is url required
`options.payload` is json object which have all required data for inserting `List` or `ListItem` without `metadata`.

In this `SPOHelper.ts` I have used `Accept` and `Content-Type` headers value `"application/json; odata=nometadata"` which doesn't 
require metadata while CRUD Operation and response payload is also minimal. You can explore [json-light-support-rest-sharepoint-api](https://www.microsoft.com/en-us/microsoft-365/blog/2014/08/13/json-light-support-rest-sharepoint-api-released/)

`SPPost(options)` This method used for Createing `List` or `List Item in SharePoint Online.
`SPUpdate(options)` This method used for Updating `List` or `List Item in SharePoint Online.

##  Usage of SPGet method

```js
//Get List By Title
SPGet("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')")
.then(r=>console.log(r));

//Get All ListItem
SPGet("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')/items")
.then(r=>console.log(r));
```

##  Usage of SPDelete method

```js
//Delete Sharepoint List 
SPDelete("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')")
.then(r=>console.log(r));

//Delete Sharepoint Listitem
SPDelete("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')/items(1)")
.then(r=>console.log(r));
```

##  Usage of SPPost method

```js
 //Create SharePoint List without passing metadata
 SPPost({url:"https://tenant.sharepoint.com/sites/ABCSite/_api/Lists"
 ,payload:{Title :"POC Doc"
 , BaseTemplate: 101
 ,Description: 'Created From SPOHelper' }
 })
 .then(r=>console.log(r));
 
 //Create SharePoint ListItem without passing metadata
 SPPost({url:"https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')/items"
 ,payload:{Title :"POST test",
 Number:123}
 })
 .then(r=>console.log(r));
 
```

##  Usage of SPUpdate method

```js
 //Update SharePoint List without passing metadata
 SPUpdate({url:"https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/GetByTitle('POC Doc')"
 ,payload:{Description: 'Updated Description From SPOHelper' }
 }).then(r=>console.log(r));
 
 //Update SharePoint ListItem without passing metadata
 SPUpdate({url:"https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')/items(1)"
 ,payload:{Title :"Uodate  test",
 Number:1234}
 }).then(r=>console.log(r))
 
```

Hope this SPOHelper Utility will be useful for faster Spfx Development with minimal code.