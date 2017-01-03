# Prerequisites
With access control enabled, ensure you have a user with `userAdmin` or `userAdminAnyDatabase` role in the admin database. This user can administrate user and roles such as: create users, grant or revoke roles from users, and create or modify customs roles.

You can create users either before or after enabling access control. If you enable access control before creating any user, MongoDB provides a localhost exception which allows you to create a user administrator in the admin database. Once created, you must authenticate as the user administrator to create additional users as needed.
* Install MongoDB server on Linux or Window OS with default configuration. 

# Create first admin user
In the admin database, add a user with the `userAdminAnyDatabase` role. For example, the following creates the user myUserAdmin in the admin database:
```
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: "abc123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```

# Enable authentication
Re-start the `mongod` instance with the `--auth` command line option or, if using a configuration file, the [`security.authorization`](https://docs.mongodb.com/manual/reference/configuration-options/#security.authorization) setting as below.
```
security:
  authorization: enabled
```
Default configuration file is at `\etc\mongod.conf`

# Login with created user
Using the mongo shell, switch to the authentication database (in this case, admin), and use `db.auth(<username>, <pwd>)` method to authenticate:
```
use admin
db.auth("myUserAdmin", "abc123" )
```
# Create second user to read/write database
```
use admin
db.createUser(
  {
    user: "test",
    pwd: "abc123",
    roles: [ { role: "readWriteAnyDatabase", db: "admin" } ]
  }
)
```

# References
* [Enable Auth](https://docs.mongodb.com/manual/tutorial/enable-authentication/)
