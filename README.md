# mini-mock-api
Lightweight node.js API mock server, route calls to static JSON files

## What does it do?

Mini-Mock-API was written to mock a simple API in a few minutes. API calls are routed to static JSON files.
As an example if your mock directory looks like this: 
```
+ mock-files
  - cars.json
  - trains.json
  + oldschool
    - carriages.json
```

with cars.json
```
[
  {id: 1, name: 'Porsche 911'},
  {id: 2, name: 'Tesla S'}
]
```

`GET localhost:8080/api/v1/cars`

returns all cars inside cars.json

`GET localhost:8080/api/v1/cars/1`

returns one car with the id == 1

`GET localhost:8080/api/v1/oldschool/carriages`

returns all items from oldschool/carriages.json


## Installation

`npm install mini-mock-api`

## Usage

```
var API = require('mini-mock-api');
var myApi = new API({
  basePath: '/api/v1',
  mockPath: 'mock-files',
  idAttribute: '_id'
});
myApi.start();
```

To run the server go to the script directory (path options are relative to your execution path):
```
cd examples
node example.js
```

Open your browser and go to `localhost:8080/api/v1/todos`

## Options
```
var options = {
  mockPath: 'mocks', //directory to mock files relative to path of script
  basePath: '/api/v1',  //base path for api calls -> localhost:8080/api/v1/...
  port: 8080,
  idAttribute: 'uuid', //the id property to search for when requesting api/v1/cars/123
  sortParameter: 'sortOrder' //the GET parameter for sort orders e.g. api/v1/cars?sortOrder=+name
}
var myApi = new API(options);
```

## Custom Routes

If static JSON files are not sufficient you can also add custom routes by defining your own request handler functions. Custom routes are supported for GET, POST, PUT and DELETE methods:
```
myApi.post('custom/status', function(request, response){
  response.json({status: 'okay'});
});
```

`-> localhost:8080/api/v1/custom/status`

Please note that custom routes are relativ to your 'basePath' property. If you need a route outside of your API scope use the '[get|post|put|delete]FromRoot' method: 
```
myApi.postFromRoot('/ping', function(request, response){
  response.json({hello: 'there'});
});
```

`-> localhost:8080/ping`
## Decorate

All API responses can be decorated by defining a `decorate` function. In this example all returned collections are wrapped into `results` and a total count is added.

```
myApi.decorate = function(data){
  if(data.length){
    return {
      results: data,
      total: data.length
    };
  }
  return data;
};
```

## Can i use this in production?

Hell no!
