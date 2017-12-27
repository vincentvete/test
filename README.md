# ODEO eMoney API
 This blueprint explain how to implement basic and primary transaction to ODEO eMoney API.
 
 
 Communication to this set of API should use HTTPS protocol to ensure the request
 is encrypted. Primary transaction will require transaction pin validation to 
 meet security requirement, in case of security requirement does not match,
 transaction will be rejected.
 
 
### Platform ID
Platform ID is required to determine the client platform as following:

Code  | Description
------|------------
1     | Android
2     | Website
3     | Web App
4     | IOS 
 
### Header
Header            | Type    | Required | Value            | Description
------------------|---------|----------|------------------|---------
Authorization     | string  | No       | Bearer xxx       | the xxx should be filled with token 
Content-Type      | String  | Yes      | Application/json | used to indicate the resource type




### Login [POST /user/login]

The response of this api will have a token which will be used to authorize particular api call.

- Payload

    Parameter         | Type    | Required | Description
    ------------------|---------|----------|------------
    phone_number      | String  | Yes      |
    password          | String  | Yes      |
    platform_id       | Integer | Yes      | see Platform ID Section
    
+ Request (application/json)

        {
            "scenario": (forgot_password | sign_up),
            "phone_number": "08xxxxxxx"
        }       
    
+ Response 200 (application/json)
       
        {
            "status": "SUCCESS",
            "data": {
                "token": xxx,
                "token_refresh": xxx,
                "user_id": xxx,
                "email": xxx;
                "phone_number": xxx;
            },
            "message": ""
        }
        
### Refresh Token [POST /user/refresh-token]

This api should be called if response of api returns 401. 
Refresh token will be revalidated and new token will be returned.

- Parameter Requirement

    Parameter         | Type    | Required | Description
    ------------------|---------|----------|------------
    user_id           | Integer | Yes      | 
    refresh_token     | String  | Yes      |
 
 + Request (application/json)

        {
            "user_id": 1,
            "refresh_token": "xxxxx"
        }       
    
+ Response 200 (application/json)
       
        {
            "status": "SUCCESS",
            "data": {
                "token": xxx,
            },
            "message": ""
        }
            
### OTP Verification [POST /user/verify]

Send an otp token to user based on phone number.

Parameter         | Type    | Required | Description
------------------|---------|----------|------------
scenario          | string  | Yes      | 
phone_number      | String  | Yes      |

Scenario is required to determine the usage of verification was called. 
The value should be either:
1. forgot_password
2. sign_up     
    
+ Request (application/json)

        {
            "scenario": (forgot_password | sign_up),
            "phone_number": "08xxxxxxx"
        }       
    
+ Response 200 (application/json)
       
        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }
    
    
### Register [POST /user/register]

use this API to register new user.  

- Payload

    Parameter        | Type    | Required | Description
    -----------------|---------|----------|------------
    phone_number     | String  | Yes      |
    email            | String  | Yes      |
    name             | Integer | Yes      | 
    password         | String  | Yes      |
    confirm_password | Integer | Yes      | 
    platform_id      | Integer | Yes      | see Platform ID Section
  
+ Request (application/json)

        {
            "phone_number":"628xxxxxxx",
            "name": "xxxx",
            "email": "xxxx,
            "password": "123456",
            "confirm_password": "123456",
            "platform_id": 1
        }       
          
+ Response 200 (application/json)
       
        {
            "status": "SUCCESS",
            "data": {
                "token": xxx,
                "token_refresh": xxx,
                "user_id": xxx,
                "email": xxx;
                "phone_number": xxx;
            },
            "message": ""
        }    
  
### Check Password [POST /user/check-password]

use to revalidate user in case of inactivity.

* required autorization header

- Payload

    Parameter| Type    | Required | Description
    ---------|---------|----------|------------
    pin      | String  | Yes      |     
    
            
+ Request (application/json)

        {
            "pin": xxxxxx,
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }
    
### Check Cash Balance [GET /user/cash]

Use this api to retrieve user cash balance.

* required autorization header

+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                "cash": {
                    "amount": 100000,
                    "currency": "IDR",
                    "formatted_amount": "Rp100,000"
                }
            },
            "message": ""
        }

        
### get recent transactions [GET /user/recent-transactions?{limit}&{offset}]

Use this api to retrieve user mutations.

* required autorization header

+ Parameters
    + offset (number) - start of row number.
    + limit (number) -  limit retreived size.
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
              "recent_transactions": [
               {
                    "description": "Top Up",
                    "type": "oCash",
                    "created_at": "2016-10-11 11:53:47",
                    "transaction_amount": {
                      "amount": 100000,
                      "currency": "IDR",
                      "formatted_amount": "Rp100,000"
                    }
                },
                {
                    "description": "Withdraw",
                    "type": "oCash",
                    "created_at": "2016-10-11 11:53:47",
                    "transaction_amount": {
                      "amount": -100000,
                      "currency": "IDR",
                      "formatted_amount": "(Rp100,000)"
                    }
                }
              ]
            },
            "message": ""
        }
        
### contact check [GET /user/contact-check?{telephone_list}]

Use this api to inquiry data of user by phone number.

* required autorization header

+ Parameters
    + telephone_list (string) - comma separated telephones. 
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }
        
### Transfer [POST /user/cash/transfer]

use this api to transfer your cash to another user.

* required autorization header

- Payload

    Parameter| Type    | Required | Description
    ---------|---------|----------|------------
    to_phone | string  | Yes      | beneficiary phone number  
    amount   | integer | Yes      | transfer amount  
    currency | string  | Yes      | currency symbol
    notes    | string  | No       | transfer note  

+ Request (application/json)

        {
           "to_phone": "08xxxxx",
           "amount": 10000,
           "currency": "IDR",
           "notes": "xxxxxxxxx"
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                order_id: xxxx
            },
            "message": ""
        }

### Create Top Up [POST /user/topup/create]

Use this api to create topup order. The response will have an order id. 
Use the order id to continue the payment flow.

* required autorization header

- Payload
    Parameter     | Type    | Required | Description
    --------------|---------|----------|------------
    cash.amount   | string  | Yes      | topup amount  
    cash.currency | string  | Yes      | topup currency 

+ Request (application/json)

        {
           "cash": {
                "amount": xxxx,
                "currency": "IDR"
           }
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                order_id: xxxx
            },
            "message": ""
        }
        
### List Payment [GET /payment/list?{order_id}]

Use this api to retrieve list of available payments.

* required autorization header

+ Parameters
    + order_id (number)
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": [
                {
                    "opc": 18,
                    "name": "Bank Transfer Mandiri",
                    "detail": {
                            "acc_bank": "Mandiri",
                            "acc_name": "PT. xxx Indonesia",
                            "acc_number": "xxx-xx-xx-xx-xx-xx",
                            "acc_branch": "xxxx - Jakarta"
                    }
                },
                {
                    "opc": 19,
                    "name": "Bank Transfer BCA",
                    "detail": {
                        "acc_bank": "BCA",
                        "acc_name": "PT. xxx Indonesia",
                        "acc_number": "0xx-xx-xxx-xx",
                        "acc_branch": "xxx - Jakarta"
                    }
                },
                {
                  "opc": 20,
                  "name": "Bank Transfer BNI",
                  "detail": {
                        "acc_bank": "BNI",
                        "acc_name": "PT. xxx Indonesia",
                        "acc_number": "xxx-xxx-xxx",
                        "acc_branch": "xxxx - Jakarta"
                    }
                },
            ],
            "message": ""
        } 
        
### Create Payment [POST /payment/request]

Use this api to create payment. this will update the order with given payment method. 
Response will have usable data for payment confirmation. 

* required autorization header

- Payload
    Parameter| Type    | Required | Description
    ---------|---------|----------|------------
    order_id | integer | Yes      |   
    opc      | integer | Yes      | payment method ID

+ Request (application/json)

        {
          "order_id": 1,
          "opc": 18,
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                "tnc": [
                    {
                        "1_text": "xxx"
                    },
                    {
                        "1_texr": "xxx"
                    }
                ],
                "content": {
                    "acc_bank": "Mandiri",
                    "acc_name": "PT. xxx Indonesia",
                    "acc_number": "xxx-xx-xx-xx-xx-xx",
                    "acc_branch": "xxxx - Jakarta"
                }
            },
            "message": ""
        }        

### Open Payment [POST /payment/open]

Use this api to commit selected payment.

* required autorization header

- Payload
    Parameter| Type    | Required | Description
    ---------|---------|----------|------------
    order_id | integer | Yes      |   

+ Request (application/json)

        {
           "order_id": 1
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }
        
### Confirm Payment [POST /payment/confirm]

Use this api to fill confirmation data if the user has transferred to the bank. 

* required autorization header

- Payload
    Parameter           | Type    | Required | Description
    --------------------|---------|----------|------------
    order_id            | integer | Yes      |   
    confirmation_number | string  | Yes      |   
    confirmation_name   | string  | Yes      |   
    confirmation_amount | integer | Yes      |   

+ Request (application/json)

        {
           "order_id": 1,
           "confirmation_number": "121313231131",
           "confirmation_name": "xxxxx",
           "confirmation_amount": 100000
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }
        
### Confirm Cancel [POST /payment/confirm/cancel]

Use this api to erase filled confirmation data. 

* required autorization header

- Payload
    Parameter           | Type    | Required | Description
    --------------------|---------|----------|------------
    order_id            | integer | Yes      |   

+ Request (application/json)

        {
           "order_id": 1,
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }        
        
### get order by ID [GET /order/{order_id}]

Use this api to retreive the detail of order by order ID.

* required autorization header

+ Parameters
    + order_id (number)
        
+ Response 200 (application/json)

        {
            "order_id": 5,
            "status": "10001",
            "created_at": "2016-10-11 11:20:10",
            "paid_at": null,
            "expired_at": "2016-10-12 11:20:10",
            "cart_data": {
              "total": {
                "amount": 100001,
                "currency": "IDR",
                "formatted_amount": "Rp100,001"
              },
              "subtotal": {
                "amount": 100000,
                "currency": "IDR",
                "formatted_amount": "Rp100,000"
              },
              "charges": [
                {
                  "type": "unique_code",
                  "displayed_name": "Unique Code",
                  "price": {
                    "amount": 1,
                    "currency": "IDR",
                    "formatted_amount": "Rp1"
                  }
                }
              ],
              "items": [
                {
                  "service_id": 2,
                  "item_detail": {
                    "topup_id": 8
                  },
                  "service_detail_id": 2,
                  "name": "OCash",
                  "price": {
                    "amount": 100000,
                    "currency": "IDR",
                    "formatted_amount": "Rp100,000"
                  },
                }
              ]
        }
        
### order cancel [POST /order/{order_id}/cancel]

Use this api to cancel created order by order ID.

* required autorization header

- Payload
    Parameter           | Type    | Required | Description
    --------------------|---------|----------|------------
    order_id            | integer | Yes      |   

+ Parameters
    + order_id (number)

+ Request (application/json)

        {
           "order_id": 1,
        } 
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }  
        
        
### get last withdrawal [GET /user/withdraw]

Use this api to retreive last pending withdrawal detail.

* required autorization header

+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                "withdraw_id": 2,
                "cash": {
                  "amount": 100000,
                  "currency": "IDR",
                  "formatted_amount": "Rp100,000"
                },
                "account_bank": "Mandiri",
                "account_name": "xxxx",
                "account_number": "xxxxxxx",
                "status": "PENDING",
                "created_at": "2016-10-11 11:53:47",
                "updated_at": "2016-10-11 11:53:47"
            },
            "message": ""
        } 
        
        
### request withdraw [POST /user/withdraw/create]

Use this API to request a new withdraw.

* required autorization header

- Payload
    Parameter           | Type    | Required | Description
    --------------------|---------|----------|------------
    bank_account_id     | integer | Yes      |  
    cash.amount         | integer | Yes      |  
    cash.currency       | string  | Yes      |  
    pin                 | string  | Yes      |  

+ Request (application/json)

        {
            "bank_account_id": 1,
            "cash": {
                "amount": 100000,
                "currency": "IDR"
            },
            "pin": "123456"
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                "withdraw_id": 2,
                "cash": {
                  "amount": 100000,
                  "currency": "IDR",
                  "formatted_amount": "Rp100,000"
                },
                "account_bank": "Mandiri",
                "account_name": "xxxx",
                "account_number": "xxxxxxx",
                "status": "PENDING",
                "created_at": "2016-10-11 11:53:47",
                "updated_at": "2016-10-11 11:53:47"
            },
            "message": ""
        } 
        
### update account [POST /user/account]

Use this api to update user account data.

* required autorization header

- Payload
    Parameter| Type   | Required | Description
    ------------------|----------|----------
    name     | string | Yes      |  
    email    | string | Yes      |  

+ Request (application/json)

        {
            "name": "xxxx",
            "email": "xxxx@gxxxx.com"
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
            },
            "message": ""
        }       

### get bank list [GET /banks]

Use this api to retreive list of available banks.

+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                "banks": [
                  {
                    "id": 1,
                    "name": "BCA"
                  },
                  {
                    "id": 2,
                    "name": "BNI"
                  },
                  {
                    "id": 3,
                    "name": "BRI"
                  },
                  {
                    "id": 4,
                    "name": "Mandiri"
                  },
            },
            "message": ""
        }
        
### get bank list [GET /user/bank-account]

Use this api to retreive user created bank account data.

* required autorization header

+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {
                "bank_accounts": [
                  {
                    "id": 2,
                    "bank": {
                      "id": 1,
                      "name": "xxx"
                    },
                    "account_name": "xxxxxx",
                    "account_number": "xxxxxxxxx"
                  }
                ],
                "metadata": {
                  "resultset": {
                    "limit": 20,
                    "offset": 0,
                    "count": 1
                  }
                }
            },
            "message": ""
        }     
        
### create user bank account [POST /user/bank-account/create]

use this api to create user bank account data. This data will be used to user withdrawal.

* required autorization header

- Payload
    Parameter       | Type      | Required | Description
    ----------------|-----------|----------|------------
    bank_id         | string    | Yes      |  
    account_name    | string    | Yes      |  

+ Request (application/json)

        {
            "bank_id": 1,
            "account_name": "xxxxxx",
            "account_number": "xxxxxxxxx"
        }  
        
+ Response 201 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }   
        
### delete user bank account [POST /user/bank-account/delete]

use this api to delete created user bank account.

* required autorization header

- Payload
    Parameter       | Type      | Required | Description
    ----------------|-----------|----------|------------
    bank_account_id | string    | Yes      |  

+ Request (application/json)

        {
            "bank_account_id": 1,
        }  
        
+ Response 200 (application/json)

        {
            "status": "SUCCESS",
            "data": {},
            "message": ""
        }           
 