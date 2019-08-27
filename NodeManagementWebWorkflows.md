# Node Management Web - Workflows
## Client to get sensor readings of specific node
IT people visits this web to get overall sensor readings of a specific node with node id as specified.

1. request to login user account
   ```
   $ curl -c cjar -b cjar -k -H \
     "Content-Type: application/json" -X  POST  \
     -d '{"data": "xxx" }' \
     https://urlpath/to/login 
     
   ...<console output expected>...
   ```
   Describe how to handle response
   > put some notice here if any
2. get challenge and return authentication info
3. visit url path/to/get/`<node-id>`/readings/
   