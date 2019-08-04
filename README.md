# Sharepoint 2013/2016/2019/Online, Office 365 REST API Code Sample/Example
In example contains Utility from [sp-rest-util](https://github.com/anomepani/sp-rest-util) for Sharepoint 2013/2016/2019/Online, Office 365 REST API Code Sample/Example which will using SP Rest utility [`SPRest.ts`](https://github.com/anomepani/sp-rest-util/blob/master/SpRest.ts) or [`SPRest.js`](https://github.com/anomepani/sp-rest-util/blob/master/SpRest.ts)
Here utility library can be used with TypeScript in #Spfx and also work with most browsers.

As I have used `fetch` API which is not available in IE11 browser so you can use [polyfill](https://github.com/github/fetch)

## Sharepoint 2013, 2016, 2019, Online, List, ListItem CRUD Operation Example code

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

## Create SharePoint RequestDigest Utility method for reusability in Code

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

## Upload file or Create Text file in SharePoint 2013, Online Document Library Using REST API Call

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

## Update A SharePoint List Item Without Increasing Its Item File Version Using SharePoint REST API


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

## SharePoint Online Batch REST API example using Batch Utils with Multiple Http Get Request in Single Batch Call
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

## SharePoint Online Batch REST API example using Batch Utils with Multiple Http POST, PATCH, DELETE Request in Single Batch Call

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

## Create a Communication Site In SharePoint Online Using REST API

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

## Create a Modern Site In SharePoint Online Using REST API

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


