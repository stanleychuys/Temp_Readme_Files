# Node Management Web - URL List
## url/path/to/`<n-th>`/entry
The URL to ask for login. server will give a challenge, or failed the request.
- Method : POST
- Request 
  * Parameters (sample)
  
    Type | Param | description | example |
    :----|:-----|:----------- |:------- |
    query | param_a |  desc for param a |param_a=xxx |
    . | param_b |  desc for param b |param_b=yyy |
    path | \<n-th> | n-th obj's id | 1234 |
    
  * Cookies
    name | description | example |
    :-----|:----------- |:------- |
    sid | the sessionid got from rsp of url xxx |sid=xxx |
  * Headers
    name | description | example |
    :-----|:----------- |:------- |
    X-Auth-Token | auth token got from rsp of url xxx |X-Auth-Token xxx |
  * Content
    * Content-Type : `application/json`
    * body content
      ```
      "field0": {
    	// field1 : description for field 1
        // type : string, ex : xxx
    	field1: "xxx"
    	// field2 : description for field 2
        field2: {
    	  // field3 : description for field 2
          // type : integer, ex : 123
          field3: 123
          ...
        }
      }
      ```
- Response 
  * Cookies
    name | description | example |
    :-----|:----------- |:------- |
    cookie2 | desc for cookie2 |cookie2=xxx |
  * Headers
    name | description | example |
    :-----|:----------- |:------- |
    Location | next URL to get challenge |Location xxx |
  * possible response
    * success (200 OK)
      * response Content-Type : `application/json`
      * response body content
        ```
        "field0": {
    	  // field1 : description for field 1
          // type : string, ex : xxx
    	  "field1": "xxx",
    	  // field2 : description for field 2
          // type : array of class_a, { 
          //          string member1
          //          int member2
          //        }
          // ex : [{"member1":"xxx","member2":123}, ...]
    	  "field2": [
            {"member1":"xxx","member2":123},
            {"member1":"yyy","member2":456},
          ],
          ...
        }
        ```
    * fail (400 Bad Request)
      * response Content-Type : `text/html`
      * response body content
        ```
        failed. check parameters.
        ```
  
