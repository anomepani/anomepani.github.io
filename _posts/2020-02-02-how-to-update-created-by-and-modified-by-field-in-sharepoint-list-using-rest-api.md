---
title: How to update created by and modified by field in sharepoint List using REST
  API
date: 2020-01-26 8:10:00 +0530
categories: [SharePoint, Tutorial]
tags: [SharePoint Online, SharePoint 2019, ValidateUpdateListItem, REST API, SPOHelper]
seo:
  date_modified: 2020-02-17 00:47:12 +0530
---

## How to update Author and Editor field in sharepoint list using REST API

If you are performing  Automated tasks using MS Flow or Power Automate or Background job for uploading file or inserting new list item to Sharepoint List using Service Credentials or Sharepoint App Only Token On Behalf of Some User then Author and Editor Field (  also known as Created By and Modified Field) are set as Service Credenetial or Sharepoint App Name.

In this type of scenario we need Way to update Author and Editor field for specific user in Sharepoint List and Document Library is very important functionality.

I have found way to Update Created By and Modified By Field in SharePoint List using REST API on Sharepoint Online.

I have used SPOHelper utility to Perform Sharepoint POST request with wrapped Request Digest.
Make Sure to pass correct Payload as per Below Sample Code snippet for file upload.

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

Hope, You find this article helpful for Updating Author and Editor Field in SharePoint  List.

