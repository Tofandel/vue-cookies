# vue-cookies

A simple Vue.js plugin for handling browser cookies reactively, heavily inspired by [vue-cookies](https://www.npmjs.com/package/vue-cookies)
This lib overrides the default fetch and XMLHttpRequest implementations to reload cookies after a request is executed, 
allowing you to use cookies as computed properties or to watch cookies for changes made by a request

## Installation
```bash
npm install vue-cookies-reactive --save
```
```js
// require
var Vue = require('vue')
Vue.use(require('vue-cookies-reactive'))

// es2015 module
import Vue from 'vue'
import VueCookies from 'vue-cookies-reactive'
Vue.use(VueCookies)

// set default config
Vue.$cookies.config('7d')

// set global cookie
Vue.$cookies.set('theme','default');
Vue.$cookies.set('hover-time','1s');
```

## Api

syntax format: **[this | Vue].$cookies.[method]**

### config()
Set global config
```js
$cookies.config(expireTimes[,path[, domain[, secure[, sameSite]]])  // default: expireTimes = 1d, path = '/', domain = '', secure = '', sameSite = 'Lax'
```
### set()
Set a cookie
```js
$cookies.set(keyName, value[, expireTimes[, path[, domain[, secure[, sameSite]]]]])   //return this
```
### get()
Get a cookie or all cookies as an object if no key is passed
```js
$cookies.get(keyName)  // return value                             
```
### remove()
Remove a cookie
```js
$cookies.remove(keyName [, path [, domain]])  // return this
```
### isKey()
Check if a cookie is set
```js
$cookies.isKey(keyName)  // return false or true
```
### keys()
Get all keys of cookies
```js
$cookies.keys()  // return an array
```
### refresh()
Refresh all cookies, note that this is already done automatically after each `XMLHttpRequest` or `fetch` are executed so it's unlikely you will need it
```js
$cookies.resfresh()  // return this
```

## Example Usage

#### set global config
```js
// 30 day after, expire
Vue.$cookies.config('30d')

// set secure, only https works
Vue.$cookies.config('7d','','',true)

// 2019-03-13 expire
this.$cookies.config(new Date(2019,03,13).toUTCString())

// 30 day after, expire, '' current path , browser default
this.$cookies.config(60 * 60 * 24 * 30,'');

```

#### support json object
```js
var user = { id:1, name:'Journal',session:'25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX' };
this.$cookies.set('user',user);
// print user name
console.log(this.$cookies.get('user').name)
```

#### set expire times
**Suppose the current time is : Sat, 11 Mar 2017 12:25:57 GMT**

**Following equivalence: 1 day after, expire**

**Support chaining sets together**
```js
 // default expire time: 1 day
this.$cookies.set("user_session","25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX")
        // number + d , ignore case
        .set("user_session","25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX","1d")
        .set("user_session","25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX","1D")
        // Base of second
        .set("user_session","25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX",60 * 60 * 24)
        // input a Date, + 1day
        .set("user_session","25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX", new Date(2017, 03, 12))
        // input a date string, + 1day
        .set("user_session","25j_7Sl6xDq2Kc3ym0fmrSSk2xV2XkUkX", "Sat, 13 Mar 2017 12:25:57 GMT")
```
#### set expire times, input number type

```js
this.$cookies.set("default_unit_second","input_value",1);            // 1 second after, expire
this.$cookies.set("default_unit_second","input_value",60 + 30);      // 1 minute 30 second after, expire
this.$cookies.set("default_unit_second","input_value",60 * 60 * 12); // 12 hour after, expire
this.$cookies.set("default_unit_second","input_value",60 * 60 * 24 * 30); // 1 month after, expire
```

#### set expire times - end of browser session

```js
this.$cookies.set("default_unit_second","input_value",0);          // end of session - use 0 or "0"!
```


#### set expire times , input string type

| Unit   | full name |
| ----------- | ----------- |
| y           |  year       |
| m           |  month      |
| d           |  day        |
| h           |  hour       |
| min         |  minute     |
| s           |  second     |

**Unit Names Ignore Case**

**not support the combination**

**not support the double value**

```js
this.$cookies.set("token","GH1.1.1689020474.1484362313","60s");  // 60 second after, expire
this.$cookies.set("token","GH1.1.1689020474.1484362313","30MIN");  // 30 minute after, expire, ignore case
this.$cookies.set("token","GH1.1.1689020474.1484362313","24d");  // 24 day after, expire
this.$cookies.set("token","GH1.1.1689020474.1484362313","4m");  // 4 month after, expire
this.$cookies.set("token","GH1.1.1689020474.1484362313","16h");  // 16 hour after, expire
this.$cookies.set("token","GH1.1.1689020474.1484362313","3y");  // 3 year after, expire

// input date string 
this.$cookies.set('token',"GH1.1.1689020474.1484362313", new Date(2017,3,13).toUTCString());
this.$cookies.set("token","GH1.1.1689020474.1484362313", "Sat, 13 Mar 2017 12:25:57 GMT ");
```

#### set expire support date
```js
var date = new Date;
date.setDate(date.getDate() + 1);
this.$cookies.set("token","GH1.1.1689020474.1484362313", date);
```

#### set never expire
```js
this.$cookies.set("token","GH1.1.1689020474.1484362313", Infinity);  // never expire
// never expire , only -1,Other negative Numbers are invalid
this.$cookies.set("token","GH1.1.1689020474.1484362313", -1); 
```

#### remove cookie
```js
this.$cookies.set("token",value); // domain.com and *.doamin.com are readable
this.$cookies.remove("token"); // remove token of domain.com and *.doamin.com 

this.$cookies.set("token", value, null, null, "domain.com"); // only domain.com are readable
this.$cookies.remove("token", null, "domain.com"); // remove token of domain.com 
```

#### set other arguments
```js
// set path
this.$cookies.set("use_path_argument","value","1d","/app");  

// set domain
this.$cookies.set("use_path_argument","value",null, null, "domain.com");   // default 1 day after,expire

// set secure
this.$cookies.set("use_path_argument","value",null, null, null,true);

// set sameSite - should be one of `None`, `Strict` or `Lax`. Read more https://web.dev/samesite-cookies-explained/
this.$cookies.set("use_path_argument","value",null, null, null, null, "Lax");
```

#### other operation
```js
// check a cookie exist
this.$cookies.isKey("token")

// get a cookie
this.$cookies.get("token");

// remove a cookie
this.$cookies.remove("token");

// get all cookie key names, line shows
this.$cookies.keys().join("\n"); 

// remove all cookie
this.$cookies.keys().forEach(cookie => this.$cookies.remove(cookie))

// vue-cookies global
[this | Vue].$cookies.[method] 

```


## Warning

**$cookies key names Cannot be set to ['expires','max-age','path','domain','secure','SameSite']**

This is a limitation of cookies and not this lib


## License

[MIT](http://opensource.org/licenses/MIT)
Copyright (c) 2020, Tofandel
