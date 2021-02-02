[![Version](https://img.shields.io/npm/v/@jollie/fetchjson)](https://www.npmjs.com/package/@jollie/fetchjson)
[![Licence](https://img.shields.io/npm/l/@jollie/fetchjson)](https://en.wikipedia.org/wiki/MIT_license)
[![Build](https://img.shields.io/travis/thejellyfish/fetchjson)](https://travis-ci.org/github/thejellyfish/fetchjson)
[![Coverage](https://img.shields.io/codecov/c/github/thejellyfish/fetchjson)](https://codecov.io/gh/thejellyfish/fetchjson)
[![Downloads](https://img.shields.io/npm/dt/@jollie/fetchjson)](https://www.npmjs.com/package/@jollie/fetchjson)

__*for internal use only - Just draft idea to easily fetch API in our apps*__

# fetchjson
Fetch wrapper to easily request an API, simply create a native fetch initialized with :
- header `Content-Type=application/json`
- default hostname & authorization credentials params
- optional method prefix

### Install

```bash
yarn add @jollie/fetchjson
```
or
```bash
npm install @jollie/fetchjson
```
### Usage

```javascript
import fetchjson from '@jollie/fetchjson';

// Init params
const init = {
 hostname: 'https://fake-api.io',
 authorization: 'Bearer API_KEY',
};

// Fetch (https://fake-api.io/v1/users?limit=10)
fetchjson('v1/users', { limit: 10 }, init)
  .then(payload => console.log(payload));

// Using by a custom wrapper
const myfetch = (url, data, params) => fetchjson(url, data, { ...params, ...init });

// Create
const data = { firstname: 'John', lastname: 'Doe' };
myfetch('POST v1/users', data)
  .then(({ id )} => console.log(`User #${id} created successfully !`));

// Update
data.firstname = 'Johnna';
myfetch('PUT v1/users/1', data)
  .then(() => console.log('User updated successfully !'));

// Delete
myfetch('DELETE v1/users/1')
  .then(() => console.log('User deleted successfully !'));

// Retrieve http response 
// payload has a not enumerable prop "_response"
myfetch('v1/users')
  .then(payload => {
    const header = payload._response.headers.get('x-powered-by');
    console.log(`Powered by ${header || 'Unknow'}`),
  });
```

### Params

```javascript
fetchjson(url, data, init);
```

| Prop               | Type     |  Note                                                                                                                           |
|--------------------|----------|---------------------------------------------------------------------------------------------------------------------------------|
| `url`              | `string` | URL to fetch <br />Could be prefixed by a http method `fetchjson('POST https://fake-api.io/v1/users')`                          |
| `data`             | `object` | queryString or Body param according http method                                                                                 |
| `init`<sup>1</sup> | `object` | Init arg passed to native fetch - [see fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch) |

(1) `init` argument can be extends with following optional properties

| Prop            | Type       |  Note                                                                                    |
|-----------------|------------|------------------------------------------------------------------------------------------|
| `hostname`      | `string`   | Prepend URL with hostname if url don't start by a domain                                 |
| `authorization` | `string`   | Authorization header <br />Ignored if url don't start by `hostname` property             |
      
    
```javascript
const init = {
 hostname: 'https://fake-api.io',
 authorization: 'Bearer API_KEY',
};

// Endpoint will be prepend with hostname
fetchjson('POST v1/users', { firstname: 'John' }, init);

// Authorization will be ignored 
// -> hostname is different than init.hostname
fetchjson('https://vendor-api.io/v1/my-account', init);
```

### Return value

Promise resolve with json payload
