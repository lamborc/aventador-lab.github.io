# MongoDB Docs Guide


## Centos7 Install

```bash
mongo
use <database> # switch or create database
```


### 配置用户

```bash
db.createUser(
  {
    user: "root",
    pwd: "Root@123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
)

use test
db.createUser({user:"nft",pwd:"Nft@123",roles:["readWrite"]})
```
