# User Sync API Brief
![Iron Gate Safety](https://storage.googleapis.com/bw2-static/assets/igss-logo-450px.png)

Updated 2020-02-15

The User Sync API allows the Iron Gate Safety System to sync its internal database of users with a third-party system (e.g. an HR system, etc.).  Once implemented, the API allows for companies to sync users across systems at regular, automated intervals.

For support using this API, please contact your Iron Gate representative.

## Planning Your Implementation

Note that in order to implement automatic syncing, typically some assistance from the group responsible for maintaining the third-party system is required.  There is also a manual option for syncing, if an automatic sync setup is not possible.

There are two options available for consideration when planning your sync implementation.

1. __New user accounts will be created on the Iron Gate Safety System__.  On a recurring interval, the third-party system will request an updated user list from Iron Gate's User Sync API and perform its own sync.  See "Bulk Export" method below.
2. __New user accounts will be created on the third-party system__.  On a recurring interval, the third-party system will push an updated user list to Iron Gate's User Sync API.  Iron Gate will use this list to perform user account updates in real-time, returning a confirmation of the actions taken on a per-user basis.  See "Bulk Import" method below.

For most customers, the simplest approach will be to set up an existing server within their infrastructure to push user information to Iron Gate's APIs (option #2) on a nightly basis.  (This sync can occur more often if so required.)

## API Access

To get started, request an API Management User Account from your Iron Gate representative (please allow 5 business days for approval and account creation).  Using this account, you may log in to Iron Gate to request an API Token.  This generated token is to be sent along with all requests to the API in order to authenticate your requests.

## API Method — Bulk Export

You may call the Bulk Export method at any time to receive a full user list from Iron Gate in either JSON or CSV format.

Request (cURL example)—
```bash
## Bulk Export
curl -X "POST" "https://demo.igsafe.com/api/v1/user/bulk-export" \
     -H 'Authorization: Bearer ds4ssY7NFvkqUxHaue7CpiJekgz767vV'
     -H 'Content-Type: application/json; charset=utf-8' \
     -d $'{}'
```

Request (JSON example)—
```json
{
    "users": [
        {
            "name": "Mary Jones",
            "employee_id": "Y-58439",
            "username": "mary.jones",
            "email": "mjones@goodbuilders.biz",
            "phone": "360-555-3434",
            "job": "Corporate Office",
            "created_at": "2017-07-23 13:39:42",
            "updated_at": "2017-10-06 12:56:35"
        },
        {
            "name": "Bob Jones",
            "employee_id": "X-13892",
            "username": "bob.jones",
            "email": "bjones@goodbuilders.biz",
            "phone": "360-555-1212",
            "job": "South Bridge Project",
            "created_at": "2017-07-31 21:40:19",
            "updated_at": "2018-11-05 18:30:44"
        },
        {
            "name": "Seth Jones",
            "employee_id": "Z-8274",
            "username": "seth.jones",
            "email": null,
            "phone": null,
            "job": "Fortune Tower 2 Project",
            "created_at": "2017-07-31 21:54:27",
            "updated_at": "2018-09-26 14:44:07"
        }
    ]
}
```

## API Method — Bulk Import

You may submit a full user list from your third-party system (in JSON or CSV format) to the Bulk Import method.  The method will match submitted records against users in the system by their employee ID and update their information. If an employee ID is not found, it is created in Iron Gate as a new user.  Note that the only required keys to be provided for each user are `name` and `employee_id`.

Request (cURL example)—
```bash
## Bulk Export
curl -X "POST" "https://demo.igsafe.com/api/v1/user/bulk-import" \
     -H 'Authorization: Bearer ds4ssY7NFvkqUxHaue7CpiJekgz767vV' \
     -H 'Content-Type: application/json; charset=utf-8' \
     -d $'{
  "users": [
    {
      "email": "mjones@goodbuilders.biz",
      "employee_id": "Y-58439",
      "phone": "360-555-3434",
      "name": "Mary Jones",
      "job": "Corporate Office"
    },
    {
      "email": "bjones@goodbuilders.biz",
      "employee_id": "X-13892",
      "phone": "360-555-1212",
      "name": "Bob Jones",
      "job": "South Bridge Project"
    },
    {
      "name": "Seth Jones",
      "employee_id": "Z-8274",
      "job": "Fortune Tower 2 Project"
    }
  ]
}'
```

In the example response below, Iron Gate reports it has taken the action to update Bob Jones' existing information while creating a new account for Seth Jones.

Request (JSON example)—
```json
{
    "errors": [],
    "updated_users": [
        {
            "name": "Bob Jones",
            "employee_id": "X-13892",
            "action": "update"
        },
        {
            "name": "Seth Jones",
            "employee_id": "Z-8274",
            "action": "create",
            "username": "seth.jones"
        }
    ]
}
```
