---
title: Upload multiple file attachments in sharepoint list using REST API On SharePoint
  Online
date: 2020-01-26 8:10:00 +0530
categories: [SharePoint, Tutorial]
tags: [SharePoint Online, SharePoint 2019, File Upload, REST API, SPOHelper]
seo:
  date_modified: 2020-02-16 18:53:26 +0530
---

## Add attachment or upload file to Sharepoint List or Document Library using REST API easily using SPOHelper

If you are developing with Typescript first you need to import requried methods from [`SPOHelper.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SPOHelper.ts)

```js
import {SPPost, SPFileUpload} from "./SPOHelper";
```
Using SPOhelper `SPPost` method, You can Upload only text file to Sharepoint List as per below code.
We are passing Url and payload for text file as plain text value.

```js
var reqUrl="https://tenant.sharepoint.com/_api/Lists/GetByTitle('SPOList')/items(1)/AttachmentFiles/add(FileName='abc3.txt')";
SPPost({
url:reqUrl
,payload:"This is text")
}).then(r=>console.log(r))
```
As we know most of case we are uploading PPT, Excel, PDF and Word File which are not Plain Text File.
To upload this types of files we need to pass payload as `Blob` type or `ArrayBuffer` which are binary format.
I have created `SPFileUpload` wrapper method to upload file to sharepoint with minimal efforts.

```js
var reqUrl="https://tenant.sharepoint.com/_api/Lists/GetByTitle('SPOList')/items(1)/AttachmentFiles/add(FileName='abc3.txt')"
SPFileUpload({
url:reqUrl
,payload:new Blob(["This is text"],{type:"text/plain"})
}).then(r=>console.log(r))
```
As per above sample example you have to pass url where you have to upload file and in payload pass arraybuffer or blob type value of file.

## Upload multiple attachments in sharepoint list using REST API easily using SPOHelper

Uploading single/multiple files or attachments to Sharepoint list is most used functionality. However uploading single attachment is easy you can use  `SPFileUpload` method, usage of this method already explained in previous article.

While uploading multiple attachments in sharepoint listitem you might have faced `Http Status Code 409 - Conflict Occurs`.

For Multiple File upload to List We need to process file uploading one by one programatically to avoid `409 Conflict issue`.

I have created `SPMultiFileUpload` helper method to upload multiple file in [`SPOHelper.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SPOHelper.ts), which internally process one by one file uploading process at a time in loop and returns result once all file are uploaded. 

Here is signature of method `SPMultiFileUpload({url :"baseReqUrl",files:[{fileName:"filename",data :Blobdata}]})`.

`SPMultiFileUpload` accepts baseRequestUrl like `https://tenant.sharepoint.com/_api/Lists/GetByTitle('SPOList')/items(4)/AttachmentFiles/` in which List and Listitem Id already is set,
`files` accept array of files json object with `fileName` and `data` field.

To upload files we need to pass `data` field as `Blob` type or `ArrayBuffer` which are binary format.

Let's see Below code snippet.

```js
import {SPMultiFileUpload} from "./SPOHelper";

var baseReqUrl ="https://tenant.sharepoint.com/_api/Lists/GetByTitle('SPOList')/items(4)/AttachmentFiles/";
//Here for testing purpose I have generated blob type object for text file , but you can prepare this array of files using file upload control

var files =[
{fileName: "1580650023814.txt", data: new Blob["Hello 1"]},
{fileName: "2580650023814.txt", data: new Blob["Hello 2"]},
{fileName: "3580650023814.txt", data: new Blob["Hello 3"]},
{fileName: "4580650023814.txt", data: new Blob["Hello 4"]}
];

//Alternatively to test multiple file upload functionality you can use below method `GenerateSampleFileArray(10)` to prepare  files array by passing number of files count as argument.
var files=GenerateSampleFileArray(4);

//Pass required paramters and Call method to upload files.
SPMultiFileUpload({url:baseReqUrl,files:files}).then(r=>console.log("All File Uploaded..",r));

```
Once all files are uploaded you will receive message in console log.