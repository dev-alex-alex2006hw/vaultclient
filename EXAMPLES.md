# Usage examples

## Tables of contents

1. [Entity creation and deletion](#entity-creation-and-deletion)
    1. [create-account](#create-account)
    2. [delete-account](#delete-account)
    3. [list-account-users](#list-account-users)
    4. [create-user](#create-user)
    5. [delete-user](#delete-user)
    6. [create-access-key](#create-access-key)
        1. [for an account](#for-an-account)
        2. [for a user](#for-a-user)
    7. [delete-access-key](#delete-access-key)

## Entity creation and deletion

This page provides quick example one-liners on how to create and delete entities
by using the different routes supported by vaultclient and Vault.

See [Vault's design document]
(https://github.com/scality/IronMan-Vault/blob/master/Design.md) for a recap
on how entities are related in our supported IAM model.

The Vault server is assumed to be running at 127.0.0.1:8500 in the following
examples. Keep in mind that the '--host' option is always mandatory, indicating
either Vault Server's IP or Fully Qualified Domain Name.

### Accounts

#### create-account

```sh
$ bin/vaultclient create-account --name TestAccount --email account@test.com \
                                 --password accountpassword --host 127.0.0.1

 {
    "message": {
        "code": 201,
        "message": "Created",
        "body": {
            "arn": "arn:aws:iam::117099473272:/TestAccount/",
            "id": "117099473272",
            "canonicalId": "CER9UNUF89LNFWQCS90RHR0WHTYUW9Q3HY9KBUMSR75V9B4VXG \
                            J0RF89X8SQEBSG"
        }
    }
}
```

#### delete-account

```sh
$ bin/vaultclient delete-account --name TestAccount --host 127.0.0.1

{
    "message": {
        "code": 204,
        "message": "No content."
    }
}
```

#### list-account-users

```sh
$ bin/vaultclient list-account-users --help

  Usage: list-account-users [options]

  Options:

    -h, --help                 output usage information
    --name <NAME>              Name of account
    --marker [MARKER]          Marker for pagination
    --maxItems [MAXITEMS]      Max items per page
    --pathPrefix [PATHPREFIX]  Path prefix for arn search

```

Like:

```sh
bin/vaultclient list-account-users --name test --pathPrefix /user10
--maxItems 2 --host 127.0.0.1

{
    "message": {
        "code": 200,
        "message": "Search successful",
        "body": {
            "isTruncated": true,
            "users": [
                {
                    "arn": "arn:aws:iam::999233902475:/user10/",
                    "createDate": "2016-04-06T12:20:43+02:00",
                    "passwordLastUsed": "2016-04-06T12:20:43+02:00",
                    "path": "/user10/",
                    "userId": "LT4J2YPGGZ9AQILAWBBLREX79OIPLZZ0",
                    "userName": "user10"
                },
                {
                    "arn": "arn:aws:iam::999233902475:/user100/",
                    "createDate": "2016-04-06T12:20:52+02:00",
                    "passwordLastUsed": "2016-04-06T12:20:52+02:00",
                    "path": "/user100/",
                    "userId": "VWDHI44UJEDS6FU4YQRN68384IBKY8W3",
                    "userName": "user100"
                }
            ],
            "marker": "1"
        }
    }
}
```

### Users

NB: to create a user, you need to have created an account to which the user
will belong

#### create-user

```sh
$ bin/vaultclient create-user --account-name TestAccount --name TestUser \
                              --email user@test.com --password userpassword \
                              --host 127.0.0.1

{
    "message": {
        "code": 201,
        "message": "Created",
        "body": {
            "arn": "arn:aws:iam::117099473272:/TestUser/",
            "id": "5EF3TQIXSXP3QFLF8783TT8ZLO37XJLK",
            "name": "TestUser",
            "createDate": "2016-02-22T11:24:39+01:00"
        }
    }
}
```

#### delete-user

```sh
$ bin/vaultclient delete-user --account-name TestAccount --name TestUser \
                              --host 127.0.0.1

{
    "message": {
        "code": 204,
        "message": "No content."
    }
}
```

### Access keys

NB: an access-key is created for a user or for account (not recommended as
it poses security threats)

#### create-access-key

##### for a user

```sh
$ bin/vaultclient create-access-key --account-name TestAccount \
                                    --user-name TestUser --host 127.0.0.1

{
    "message": {
        "code": 201,
        "message": "Created",
        "body": {
            "accountName": "TestAccount",
            "userName": "TestUser",
            "status": "Active",
            "createDate": "2016-02-22T11:25:10+01:00",
            "id": "D4IT2AWSB588GO5J9T00",
            "value": "UEEu8tYlsOGGrgf4DAiSZD6apVNPUWqRiPG0nTB6"
        }
    }
}
```

##### for an account

Creation for account TestAccount (requests authenticated with this key will
be considered as performed by the account itself, not advisable):

```sh
$ bin/vaultclient create-access-key --account-name TestAccount --host 127.0.0.1

{
    "message": {
        "code": 201,
        "message": "Created",
        "body": {
            "accountName": "TestAccount",
            "status": "Active",
            "createDate": "2016-02-22T11:25:37+01:00",
            "id": "7C66DCVN609K7ZHDBVZ0",
            "value": "JXxTT04NxiWb6NcES+rpkHnkXszDq3KxexocJIJ9"
        }
    }
}
```

#### delete-access-key

```sh
$ bin/vaultclient delete-access-key --id 7C66DCVN609K7ZHDBVZ0 --host 127.0.0.1

{
    "message": {
        "code": 204,
        "message": "No content."
    }
}
```
