## What is SharePoint Online REST API ?

## HTTP Verbs : GET, POST, DELETE, MERGE

## Understandd SharePoint Online CRUD(Create, Read, Update, Delete) Operation using REST API

## Repeated code in CRUD Operation Demo for below code

```js
// Example : getListData ("https://brsupport.sharepoint.com/_api/Lists").then(r=>console.log(r))
var getListData = function(reqUrl) {
  //GET Request using Fetch API
  return fetch(reqUrl, {
    method: "GET", //Http Verb
    credentials: "include", //Send Cookie and other credential while make request
    headers: {
      Accept: "application/json; odata=nometadata" // Requesting data in specific this format
      //"Content-Type": "application/json; odata=verbose" //Indicate that we are passing data in this format to server
    } //Headers
    //body: undefined
  }).then(r => r.json());
};


// Example : getRequestDigest("https://brsupport.sharepoint.com").then(r=>console.log(r))
var getRequestDigest = function(rootUrl) {
  //RequestDigest Request using Fetch API
  return fetch(rootUrl + "/_api/contextinfo", {
    method: "POST", //Http Verb
    credentials: "include", //Send Cookie and other credential while make request
    headers: {
      Accept: "application/json; odata=nometadata", // Requesting data in specific this format
      "Content-Type": "application/json; odata=nometadata" //Indicate that we are passing data in this format to server
    }, //Headers
    body: undefined
  }).then(r => r.json());
};

//Example : CreateNewList ("https://brsupport.sharepoint.com/_api/Lists","SPList").then(r=>console.log(r))

var CreateNewList = function(reqUrl, listName) {
  return getRequestDigest(reqUrl.split("/_api")[0]).then(function(digest) {
    console.log("Received Request Digest",digest.FormDigestValue);
    //RequestDigest Request using Fetch API
    return fetch(reqUrl, {
      method: "POST", //Http Verb
      credentials: "include", //Send Cookie and other credential while make request
      headers: {
        Accept: "application/json; odata=nometadata", // Requesting data in specific this format
        "Content-Type": "application/json; odata=nometadata", //Indicate that we are passing data in this format to server
        "X-RequestDigest":digest.FormDigestValue //Requestdigest Required for POST, UPDATE and DELETE Operation
      }, //Headers
      body: JSON.stringify({Title :listName
 , BaseTemplate: 100})
    }).then(r => r.json());
  });
};

//Example : UpdateList ("https://brsupport.sharepoint.com/_api/Lists/GetByTitle('SPList')","SPList1").then(r=>console.log(r))
var UpdateList = function(reqUrl, listName) {
  return getRequestDigest(reqUrl.split("/_api")[0]).then(function(digest) {
    console.log("Received Request Digest",digest.FormDigestValue);
    //RequestDigest Request using Fetch API
    return fetch(reqUrl, {
      method: "POST", //Http Verb
      credentials: "include", //Send Cookie and other credential while make request
      headers: {
        Accept: "application/json; odata=nometadata", // Requesting data in specific this format
        "Content-Type": "application/json; odata=nometadata", //Indicate that we are passing data in this format to server
        "X-RequestDigest":digest.FormDigestValue, //Requestdigest Required for POST, UPDATE and DELETE Operation
    "IF-MATCH": "*",
        "X-HTTP-Method": "MERGE"
      }, //Headers
      body: JSON.stringify({Title :listName
    })}).then(r => r); // Update Operation doesn't return any data so need to convert it to JSON then(r => r.json());
  });
};

//Example : DeleteList ("https://brsupport.sharepoint.com/_api/Lists/GetByTitle('SPList1')").then(r=>console.log(r))
var DeleteList = function(reqUrl) {
  return getRequestDigest(reqUrl.split("/_api")[0]).then(function(digest) {
    console.log("Received Request Digest",digest.FormDigestValue);
    //RequestDigest Request using Fetch API
    return fetch(reqUrl, {
      method: "POST", //Http Verb
      credentials: "include", //Send Cookie and other credential while make request
      headers: {
        Accept: "application/json; odata=nometadata", // Requesting data in specific this format
        "Content-Type": "application/json; odata=nometadata", //Indicate that we are passing data in this format to server
        "X-RequestDigest":digest.FormDigestValue, //Requestdigest Required for POST, UPDATE and DELETE Operation
    "IF-MATCH": "*",
        "X-HTTP-Method": "DELETE"
      }, //Headers
     // body: JSON.stringify({Title :listName //DELETE Operation doesn't need body parameter
 }).then(r => r); // Delete Operation doesn't return any data so need to convert it to JSON then(r => r.json());
  });
};
```


    ## Sharepoint Online REST API Filters, Select, Sorting using OData queries

