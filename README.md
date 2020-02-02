# Sharepoint 2013,2019,Online,REST API, Batch API Code Sample| Example |SPFX
In example contains Utility from [sp-rest-util](https://github.com/anomepani/sp-rest-util) for Sharepoint 2013/2016/2019/Online, Office 365 REST API Code Sample/Example which will using SP Rest utility [`SPRest.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SpRest.ts) or [`SPRest.js`](https://github.com/anomepani/sp-rest-util/blob/master/SpRest.ts)
Here utility library can be used with TypeScript in #Spfx and also work with most browsers.

[`SPOHelper.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SPOHelper.ts) is very useful and easy to use for Sharepoint Online, Sharepoint 2016, Sharepoint 2019 REST API Operation with minimal code.

As I have used `fetch` API which is not available in IE11 browser so you can use [polyfill](https://github.com/github/fetch)

## How to update created by and modified by field in sharepoint List using REST API
## How to update Author and Editor field in sharepoint list using REST API

If you are performing  Automated tasks using MS Flow or Power Automate and Background job for uploading file or inserting new list item to Sharepoint List using Service Credentials or Sharepoint App Only Token On Behalf of Some User then Author and Editor Field (  also known as Created By and Modified Field) are set as Service Crednetial  or Sharepoint App Name.

In this type of scenario Way to update Author and Editor field in Sharepoint List and Document Library is very important functionality.

Luckily, I have found way to Update Created By and Modified By Field in SharePoint List using REST API on Sharepoint Online.

I have used SPOHelper utility to Perform Sharepoint POST request with wrapped Request Digest.
Make Sure to pass correct Paylaod as per Below Sample Code snippet.

```js
import {SPPost} from "./SPOHelper";

// Prepare request Url to update Author or Editor field in SharePoint Using REST API as bellow
var rootUrl="https://tenant.sharepoint.com";
var reqUrl=rootUrl+"/_api/web/Lists/GetbyTitle('SPOList')/items(2)/ValidateUpdateListItem()";

// Prepare payload to update Author or Editor field in SharePoint Using REST API as bellow

var payload={"formValues":[
{"FieldName":"Editor"
,"FieldValue":"[{'Key':'i:0#.f|membership|normal@tenant.onmicrosoft.com'}]"
},
{"FieldName":"Author"
,"FieldValue":"[{'Key':'i:0#.f|membership|normal@tenant.onmicrosoft.com'}]"}]
};

SPPost({url:reqUrl,payload:payload}).then(r=>console.log(r));

```
As per above code once Request Executed you will see response in console as below. In Response `ErrorMessage` and `HasException` value indicate weather our request executed successfully or not.

```
{"value":[
{"ErrorMessage":null, "FieldName":"Editor",
"FieldValue":"[{'Key':'i:0#.f|membership|normal@tenant.onmicrosoft.com'}]"
,"HasException":false,"ItemId":2}
,{"ErrorMessage":null,"FieldName":"Author",
"FieldValue":"[{'Key':'i:0#.f|membership|normal@tenant.onmicrosoft.com'}]"
,"HasException":false,"ItemId":2}]}
```

#### Hope, You find this article helpful for Updating Author and Editor Field in SharePoint  List.

##  How to Add attachment or upload file to Sharepoint List or Document Library using REST API on SharePoint Online, 2016, 2019
### Posted Date : "2020-01-26T08:48:00.909Z"

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

##  SPOHelper - Light Weight CRUD Operation REST Utility For SharePoint Online in SPFX Framework
### Posted Date : "2020-01-12T08:48:00.909Z"
On Daily basis if we have to make REST API Request in Sharepoint Online Most of developer will be using `fetch` or `$.ajax`.
To use `$.ajax` it requires external depenedency `jQuery`, and `fetch` API Available in Modern Browsers and polyfill available as well.

However even if we are making Sharepoint REST API (CRUD) Request using `fetch` or `$.ajax` every time we need to setup Headers, 
Content Type, Credentials  and RequestDigest Headers and json data conversion for response.

To Remove this type of duplicate configuration and repetetive boilerplate code,  I have created [`SPOHelper.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SPOHelper.ts) for faster SPFX Development.

### How to Perform Sharepoint List | Library | all crud operations using REST API on Sharepoint Online, Sharepoint 2016, Sharepoint 2019.

If you are developing with Typescript first you need to import requried methods 

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

#### Sharepoint Online GET REST API Request Usage Example using SPOHelper SPGet method | Sharepoint crud operations

```js
//Get List By Title
SPGet("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')")
.then(r=>console.log(r));

//Get All ListItem
SPGet("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')/items")
.then(r=>console.log(r));
```

#### Sharepoint Online DELETE REST API Request Usage Example using SPOHelper SPDelete method | Sharepoint crud operations

```js
//Delete Sharepoint List 
SPDelete("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')")
.then(r=>console.log(r));

//Delete Sharepoint Listitem
SPDelete("https://tenant.sharepoint.com/sites/ABCSite/_api/Lists/getbytitle('SPO List')/items(1)")
.then(r=>console.log(r));
```

#### Sharepoint Online POST REST API Request Usage Example using SPOHelper SPPost method | Sharepoint crud operations

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

#### Sharepoint Online UPDAte REST API Request Usage Example using SPOHelper SPUpdate method | Sharepoint crud operations

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
##### Fill free to create [isssue](https://github.com/anomepani/sp-rest-util/issues) and reach out to me at [arvindmepani@gmail.com](mailto:arvindmepani@gmail.com)

## Sharepoint 2013, 2016, 2019, Online, List, ListItem CRUD Operation Example code Using REST API

### Note- Suggesting to use `SPOHelper` utils for CRUD Operaton using Sharepoint  REST instead of `SPRest`

```js
var util=new SPRest("https://brgrp.sharepoint.com");

// Get All Item from List item  
util.Utils.ListItem.GetAllItem({listName:"PlaceHolderList"}).then(function(r){  
console.log(r);  
// Response received. TODO bind record to table or somewhere else.  
});

//Get all selected column Data using full URL  
var reqUItemUrl="https://brgrp.sharepoint.com/_api/web/lists/getbytitle('PlaceHolderList')/items";
util.Utils.ListItem  
.GetAllItem({  
   url:reqUItemUrl+"?$select=Id,Title&$top=200"  
}).then(function(r){console.log(r);  
//Response Received  
});  
  
//Get all selected column data with listName and oDataOption  
  
util.Utils.ListItem.GetAllItem(  
   {"listName":"PlaceHolderList"  
      ,oDataOption:"$select=Id,Title&$top=200"  
   }).then(function(r){console.log(r);  
   //Response Received  
});  

// Get List Item By Id  
util.Utils.ListItem.GetItemById({listName:"PlaceHolderList",Id:201}).then(function(r){    
console.log(r);    
// Response received.   
});

// Add ListItem to Sharepoint List  
util.Utils.ListItem.Add({listName:"PlaceHolderList"
,data:{Title:"New Item Created For Demo",UserId:1,Completed:"true"}}).then(function(r){    
console.log(r);    
// Added New List item response received with newly created item  
}); 

// Update List item based on ID with new data in SharePoint List  
util.Utils.ListItem.Update({listName:"PlaceHolderList",Id:201
,data:{Title:"Updated List Item",UserId:1,Completed:"true"}}).then(function(r){    
// List Item Updated and received response with status 204  
console.log(r);  
}); 

// Delete List item based on ID  
util.Utils.ListItem.Delete({listName:"PlaceHolderList",Id:201}).then(function(r){    
// List Item Deleted and received response with status 200  
console.log(r);  
}); 
```

Reference link : https://www.c-sharpcorner.com/article/easy-sharepoint-listitem-crud-operation-using-rest-api-wrapper/

## Get or Generate RequestDigest in SharePoint 2013, 2016, 2019 , Online using REST API

```js
var getRequestDigest=(rootUrl)=>{
var _payloadOptions = {  method: "POST", 
                headers: {  credentials: "include",  Accept: "application/json; odata=verbose"
                ,"Content-Type": "application/json; odata=verbose" }  
            };  
  
//RequestDigest Request
return fetch(rootUrl+"/_api/contextinfo",_payloadOptions).then(r=>r.json())
}
```



## Upload file or Attachment to in SharePoint 2013, 2016,2019 Online Custom List or Document Library Using REST API

```js
//Get Digest first then create txt file
getRequestDigest("https://brgrp.sharepoint.com").then (r=>{
//Received Request Digest
var reqUrl="https://brgrp.sharepoint.com/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')"
fetch(reqUrl+"/Files/add(url='file_name.txt',overwrite=true)",
{method:"POST",headers:
{accept:"application/json;odata=verbose",
"Content-Type":"application/json;odata=verbose","X-RequestDigest":r.d.GetContextWebInformation.FormDigestValue }
,body:"Content Of Text File"}).then(r=>console.log(r))
})
```
## Read file or attachment from SharePoint Document Library Using REST API | Sharepoint Onine, 2013, 2016, 2019

In this example I have tried to Read Excel File from SharePoint Online document library using REST API

```js
 
        fetch("https://brgrp.sharepoint.com/sites/WMAMaster/_api/Web/GetFileByServerRelativePath(decodedurl='/sites/WMAMaster/Shared Documents/Book.xlsx')/$value", {
            headers: {
                "accept": "application/json;odata=verbose"
            }
        }).then(r => r.blob()).then(r => 
{
console.log("BLOB RESULT");
console.log(r);
            //Commented Converting  Of Blob to array buffer
            new Response(r).arrayBuffer().then(r=>{
console.log("ARRAY BUFFER RESULT");
console.log(r);
});
        });
```

## Copy file or attachement from SharePoint List | Document Library Using REST API | SharePoint Online, 2013, 2016, 2019 

In This example I have tried to read excel file from one of the sharepoint online document library and create copy of excel file in library

```js

//Generate Request Digest for Creating excel file in SharePoint Online Document Library
fetch("https://brgrp.sharepoint.com/sites/WMAMaster/_api/contextinfo", {
        method: "POST",
        headers: {
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",

        }
    }).then(r => r.json())
    .then(rd => {

        //Read Excel File from SharePoint
        fetch("https://brgrp.sharepoint.com/sites/WMAMaster/_api/Web/GetFileByServerRelativePath(decodedurl='/sites/WMAMaster/Shared Documents/Book.xlsx')/$value", {
            headers: {
                "accept": "application/json;odata=verbose"
            }
        }).then(r => r.blob()).then(r => {
            //Commented Converting  Of Blob to array buffer
            //new Response(r).arrayBuffer().then(r=>{
            //here "rd" is response from RequestDigest Request
            //here "r" is response from Reading Excel file from SharePoint in ArrayBuffer
            // Copy Excel file from SharePoint and Save it to Library

            fetch("https://brgrp.sharepoint.com/sites/WMAMaster/_api/web/GetFolderByServerRelativeUrl('/sites/WMAMaster/Shared Documents')/Files/add(url='copyexcel.xlsx',overwrite=true)", {
                method: "POST",
                headers: {
                    accept: "application/json;odata=verbose",
                    "Content-Type": "application/json;odata=verbose",
                    "X-RequestDigest": rd.d.GetContextWebInformation.FormDigestValue
                },
                body: r
            }).then(r => console.log(r));
            //Commented Converting  Of Blob to array buffer
            //});

        });
    });
```
## Using SystemUpdate, Update SharePoint List Item Without Increasing Its Item File Version Using SharePoint REST API | SharePoint Online, 2013, 2016, 2019


```js
//payload for request   
 body=  {"formValues":[{"FieldName":"Title","FieldValue":"Single Update Title with versioning__"}]
 ,bNewDocumentUpdate:true}  
  
 //Header data for sharepoint POST Request  
 var _payloadOptions = {  
                method: "POST",  
                body: undefined,  
                headers: {  
                    credentials: "include",  
                    Accept: "application/json; odata=verbose",  
                    "Content-Type": "application/json; odata=verbose"  
                }  
            };  
  
//Get RequestDigest First  
fetch("https://brgrp.sharepoint.com/_api/contextinfo",_payloadOptions).then(r=>r.json())  
.then(r=>  
                                   {  
_payloadOptions.headers["X-RequestDigest"]=r.d.GetContextWebInformation.FormDigestValue  
      
_payloadOptions.body=JSON.stringify(body);  
  
// Make REST API Call to update list item without increamenting version.  
fetch("https://brgrp.sharepoint.com/_api/web/Lists/GetbyTitle('Documents')/items(1)/ValidateUpdateListItem()",
_payloadOptions).then(r=>r.json()).then(r=>console.log(r))  
});

```

Reference link : 
https://www.c-sharpcorner.com/article/update-a-sharepoint-list-item-without-increasing-its-item-file-version-using-res/

## SharePoint Online, 2016, 2019 Batch Request REST API example using Batch Utils with 100 request in Single Batch call
BatchUtils can be found in [Here](https://github.com/anomepani/sp-rest-util/blob/master/BatchUtils.ts)

Here rootUrl required to Generate Request Digest Token as batch Request is POST request.

```js
var arr=["https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(212)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(213)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(214)"];

BatchUtils.GetBatchAll({rootUrl:"https://brgrp.sharepoint.com",
batchUrls:arr}).then(r=>console.log(r))

```
You can skip rootUrl if you have already generated request digest as below.

```js
var arr=["https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(212)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(213)"
, "https://brgrp.sharepoint.com/_api/Lists/Getbytitle('PlaceHolderList')/items(214)"];

getRequestDigest("https://brgrp.sharepoint.com").then(r=>{

BatchUtils.GetBatchAll({rootUrl:"https://brgrp.sharepoint.com",
batchUrls:arr,FormDigestValue: r.d.GetContextWebInformation.FormDigestValue}).then(r=>console.log(r))
});

```

##  SharePoint Online, 2016, 2019 Batch REST API example using Batch Utils with Multiple Http POST, PATCH, DELETE Request in Single Batch Call

SharePoint Batch API is very powerful and useful for making multiple request to single request.
This BatchUtils Support ADD/UPDATE/DELETE Operation, it can be combined in single batch Requests.
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

## Create a Communication Site In SharePoint 2019, Online Using REST API

### Previously, In On Premise version of SharePoint it will take a lots of time to create sites or site collection. But Nowadays, On SharePoint Online it is very easy to create sites.

In this example we will see sample code to create Communication Site in SharePoint Online using REST API.
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

## Create a Modern Site In SharePoint Online,2019 Using REST API

## As we have created Communication sites using REST API In SharePoint Online, We can create Modern Site In SharePoint Online using REST API.

To specify which type of site to create you use the WebTemplate attribute. Use one of the following templates to select which type of site to create:

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

### Changelog
Year 2019 - Created SharePoint Online REST and Batch Utility to reduce daily basis repetetive code and hosted website using GitHub Pages

12-Jan-2020 - Created general purpose another SPO Helper Utility library to make GET, POST, UPDATE and DELETE Operation in Sharepoint Online REST API Easily.

26-Jan-2020 - Updated heading and content, Fixed type mistake.
