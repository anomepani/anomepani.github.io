---
title: Make Batch Request With REST API In SharePoint Online, Sharepoint 2019, 2016
date: 2019-08-03 8:10:00 +0530
categories: [SharePoint, Tutorial]
tags: [SharePoint Online, SharePoint 2019, Batch API]
seo:
  date_modified: 2020-02-21 01:55:41 +0530
---

## Usage Of BatchUtils's GetBatchAll

SharePoint Online Supports Odata batch request in which we make upto 100 request in Single Batch Call and return result in order. Execution of request and order of request is independent of any other requests.

SharePoint Batch API is very powerful and useful for making multiple request to single request.

However, Create Request header and Payload for making successful Batch Request and Response Parsing is very tricky in SharePoint Online.

However using [`BatchUtils`](https://github.com/anomepani/sp-rest-util/blob/master/BatchUtils.ts) it is very easy for sharepoint developer to make bacth request easily in Browser and In SPFX Development.

As per code snippet Pass `rootUrl` and  `batchUrls` as array of request for GET Request.

```js
var arr=["https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(212)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(213)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(214)"];

BatchUtils.GetBatchAll({rootUrl:"https://brgrp.sharepoint.com",
batchUrls:arr}).then(r=>console.log(r))

```

You can skip passing `rootUrl` if you have already generated request digest as below.

```js
var arr=["https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(212)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(213)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(214)"];

getRequestDigest("https://brgrp.sharepoint.com").then(r=>{

BatchUtils.GetBatchAll({rootUrl:"https://brgrp.sharepoint.com",
batchUrls:arr,FormDigestValue: r.d.GetContextWebInformation.FormDigestValue}).then(r=>console.log(r))
});

```
> Make a not Batch Request doesn't work with Cross Site and Cross Sub Site.

## Usage Of BatchUtils's PostBatchAll

In SharePoint Online, SharePoint 2019 ,2016 , we can call Multiple Http POST, PATCH, DELETE Request in Single Batch Call

This BatchUtils Support ADD/UPDATE/DELETE Operation, it can be combined in single batch Requests.

Order of request doesn't matter but Response order will be same as request payload order in `BatchUrls`.


```js

// Prepare collection of request with requestUrl and payload data.
var arr=[{
reqUrl:"https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(212)"
,action:"UPDATE",
data:{__metadata:{type:"SP.Data.PlaceHolderListListItem"},Title:"Update Article_1"}},
{
reqUrl:"https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(213)"
,action:"UPDATE",
data:{__metadata:{type:"SP.Data.PlaceHolderListListItem"},Title:"Update Article_2"}},
{
reqUrl:"https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(214)"
,action:"UPDATE",
data:{__metadata:{type:"SP.Data.PlaceHolderListListItem"},Title:"Update Article_3"}},
{
reqUrl:"https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items"
,action:"ADD",
data:{__metadata:{type:"SP.Data.PlaceHolderListListItem"},Title:"Add Article_1"}}
,{
reqUrl:"https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(215)"
,action:"DELETE"}];

getRequestDigest().then(r=>{

BatchUtils.PostBatchAll({rootUrl:"https://brgrp.sharepoint.com",
batchUrls:arr,FormDigestValue: r.d.GetContextWebInformation.FormDigestValue}).then(r=>console.log(r))
})

```

> Make a not Batch Request doesn't work with Cross Site and Cross Sub Site.

BatchUtils will be very helpful for performance oriented tasks with Batch Operation and Bulk Operation in Sharepoint Online using Batch Request.