# POS Integration Guide

## Overview

The **Yoyo Platform** is an open, interoperable system that enables mobile and card-initiated transactions directly at point-of-sale.

Through a **single integration**, merchants can:

- Accept transactions from any integrated mobile application  
- Process multiple transaction types  
- Handle various payment methods  
- Manage loyalty programs  

---

<img width="1208" height="351" alt="POS Flow Diagram" src="https://github.com/user-attachments/assets/74b37f40-35c9-49a0-941d-c0d3f6dfa897" />

---

### Integration models

There are generally two manners of performing mobile transactions, namely:

- Over the Counter model
- Sit-down model

## Over the Counter Model

The over-the-counter model is built on the principle that the client provides a wiCode (usually in the form of a QR code) to the cashier at the POS. Ideally the POS has the capability of scanning a QR code and processing the wiCode contained in the QR by making use of the YoyoDetect.dll, or manually entering the wiCode on the POS software to process a mobile payment.

For access to the YoyoGroup Detect SDK, please contact YoyoGroup integrations.

## Sit-down model

Sit-down payments requires the POS to print a unique QR code on each till slip. POS software providers can use the Yoyo Bill Service REST API to generate a QR code containing the bill information to be scanned and either settled or partially settled via the customer integrated mobile application.

<img width="262" height="397" alt="image" src="https://github.com/user-attachments/assets/b56ff979-38ee-445e-b47b-ae4206c03561" />

The POS software requires to be integrated to the transaction history call to retrieve payments made on the basket.

---

## Transaction Types
The YoyoPlatform supports the following transaction types:

- Pay in Store
- Earn Loyalty
- Redeem Loyalty / Coupon / Voucher / Giftcard
- Cash Withdrawal
- Cash Deposit
  
## Key Benefits
All transactions, regardless of the value store provider, follow the same process at the point of sale. This eliminates the need for cashiers to learn specific procedures for each provider.

## Simplified Cashier Training:
Cashiers do not require additional training for each application accepted by the merchant, reducing onboarding time and operational overhead.

## Consolidated Recon and Settlement:
Recon and settlement are aggregated across all providers, allowing merchants to process them once, instead of per provider.

Detailed, low-level reporting is available for each transaction provider, ensuring merchants have access to all data necessary for business intelligence and analytics.
This seamless and standardized approach enhances efficiency and reduces complexity for both merchants and cashiers.

# Authentication

The following table specifies the header parameters which are required in each web service call.

| Field             | Data Type | Required/Optional | Description |
|-------------------|-----------|-------------------|-------------|
| id                | String    | Required          | The application id. |
| password          | String    | Required          | The application password hashed using SHA1. |
| apiClientVersion  | String    | Required          | This field can be used by POS integrators to send the version of their implementation to the Yoyo Platform. |
| apiServerVersion  | String    | Required          | This field allows POS integrators to send the version of the Yoyo server in use at the time of integration, ensuring strict backwards compatibility. |


Example:

```http
POST /wigroup-transactionengine/pos-providers/transaction
id: YOUR_ID
password: YOUR_PASSWORD
apiClientVersion: 1
apiServerVersion: 1
Content-Type: application/json
```
HOST URL
The QA wiCode platform exposes REST service endpoints by utilizing the following host and path: https://rad2.wigroup.co:8181/wigroup-transactionengine/pos-provider/

The DNS for the QA environment is rad2.wigroup.co, using an SLL port connection (8181). This DNS resolves to a load balancer of which the IP address might change without notice.

Monetary values
Monetary amounts are both accepted and returned in minor denominations.

Versioning
This API exposes two required versioning fields.

API server versions
The following API server versions are currently being supported:

| apiServerVersion | Description                                                                                     | Environment(s)           |
|------------------|-------------------------------------------------------------------------------------------------|--------------------------|
| 1.5              | Base                                                                                            | Integration, Production  |
| 1.8              | Support added for loyalty on split tender. VSP Name included on Transaction History.           | Integration, Production  |
| 1.9              | List Redemptions in transaction API.                                                            | Integration, Production  |
| 1.10             | tokenInfo flags added.                                                                          | Integration, Production  |
| 1.11             | getVasToken API call added. Enable main VSP for loyalty redemption.                            | Integration, Production  |
| 1.14             | Enables Over the Counter tipping, billAmount and Discount/Payment split.                       | Integration, Production  |

# Quick Start

To integrate POS transactions your system must:

1. Capture the **customer token** (wiCode or QR code)
2. Submit the **transaction request**
3. Process the **transaction response**
4. Confirm the transaction using the **Advice API**

### Transaction Flow

### Step 1 — Customer Presents the wiCode or QR code for Payment to the cashier or input.

Your POS or checkout should capture the token value and prepare a transaction request.

Example token:

### Step 2 — Token Info (OPTIONAL)

Before processing a transaction you may validate the token. The Info call is optional as validation can also be done using the transaction request.

The tokenInfo request serves multiple purposes:

- Validation: It checks whether the provided wiCode is valid and eligible for redemption.
- Monetary Value Retrieval: The response typically includes the monetary value associated with the coupon, voucher, or gift card. This information is crucial for displaying potential discounts to users during their        shopping experience.
- Special Cases: Note that if a coupon applies to specific items or offers a percentage discount, the response may differ from standard cases.
- Host URL: https://rad2.wigroup.co:8181/wigroup-transactionengine/pos-providers/token-info
- HTTP Method: POST

➡ [Token Validation](../index.html#operation/post_wigroup-transactionengine-pos-providers-token-info)

<details class="collapsible">
  <summary>Token Info Request API</summary>
  <div class="details-body">

```json
{
    "id": "POS_ID_GOES_HERE",
    "password": "POS_PASSWORD_GOES_HERE",
    "apiServerVersion": "1.14",
    "apiSClientVersion": "this will be your software version identifier",
    "Content-Type": "application/json"
}
Here’s an example of how to structure the tokenInfo request:

 Token Info Request Example
{
    "token": {
        "id": "1234567",
        "type": "WICODE"
    },
    "storeTrxDetails": {
        "storeId": 1050,
        "basketId": "basket1",
        "cashierId": "cashier1",
        "posId": "workstation1",
        "trxId": "1"
    }
}
```
</div>
</details>

# Response Examples

This response indicates the details of a coupon associated with the provided wiCode:

Coupon Response Example:

<details class="collapsible">
  <summary>Token Info Response</summary>
  <div class="details-body">

```json
{
    "vsp": {
        "id": 20016,
        "name": "wiCoupon",
        "coupons": {
            "coupon": [
                {
                    "product": [
                        {
                            "id": "coffee1"
                        },
                        {
                            "id": "coffee2"
                        },
                        {
                            "id": "coffee3"
                        }
                    ],
                    "name": "API Coupon Documentation",
                    "discount": 1750
                }
            ]
        },
        "hasVoucher": false,
        "hasCoupon": true,
        "hasWallet": false,
        "hasGiftCard": false
    },
    "responseCode": "-1",
    "responseDesc": "Success"
}

```
</div>
</details>


This response provides details about a voucher associated with the provided wiCode:
Voucher Response Example:

<details class="collapsible">
  <summary>Token Info Voucher Response</summary>
  <div class="details-body">

```json

{
    "vsp": {
        "id": 20016,
        "name": "wiCoupon",
        "coupons": {
            "coupon": [
                {
                    "name": "API Voucher Documentation",
                    "discount": 10000
                }
            ]
        },
        "hasVoucher": true,
        "hasCoupon": false,
        "hasWallet": false,
        "hasGiftCard": false
    },
    "responseCode": "-1",
    "responseDesc": "Success"
}
```
</div>
</details>

 This response outlines the details of a gift card associated with the provided wiCode:
 Gift Card Response Example:

 <details class="collapsible">
  <summary>Token Info Voucher Response</summary>
  <div class="details-body">

 ```json
{
    "vsp": {
        "id": 20016,
        "name": "wiCoupon",
        "balances": {
            "balance": [
                {
                    "name": "API Gift Card Documentation",
                    "type": "CENT",
                    "value": 50000
                }
            ]
        },
        "hasVoucher": false,
        "hasCoupon": false,
        "hasWallet": true,
        "hasGiftCard": false
    },
    "responseCode": "-1",
    "responseDesc": "Success"
}
```

</div>
</details>

### Step 3 — Submit Transaction

Once the token is validated, submit the payment transaction. Should the Info API not be implemented, the transaction request can be immediately initiated.

➡ [Create POS Transaction](../index.html#operation/post_wigroup-transactionengine-pos-providers-transaction)

## Example Request

<details class="collapsible">
  <summary>Transaction Request</summary>
  <div class="details-body">

```json
{
    "type": "String",
    "switchTrxId": "String",
    "totalAmount": 0,
    "basketAmount": 0,
    "cashbackAmount": 0,
    "tipAmount": 0,
    "billAmount": 0,
    "product": [
        {
            "id": "String",
            "units": 0,
            "pricePerUnit": 0
        },
        {
            "id": "String",
            "units": 0,
            "pricePerUnit": 0
        }
    ],
    "token": {
        "id": "String",
        "type": "String",
        "payload": "String"
    },
    "storeTrxDetails": {
       "storeId": "123778",
        "basketId": "String",
        "trxId": "String",
        "posId": "String",
        "cashierId": "String",
        "billId": 0,
        "posBillId": "String"
    }
}
```
</div>
</details>


---
The merchant identifiers (storeId and retailerId) will be provided by Yoyo. The authentication details found in the header (id and password) will also be provided by Yoyo. The product array list is required for coupons. Please refer to the transaction details for more information.

- Host URL: ```http https://rad2.wigroup.co:8181/wigroup-transactionengine/pos-providers/transaction ```
- HTTP Method: POST
- All responses with a "responseCode": "-1" is considered successful. Anything other than "-1" is considered failed.
- Please refer to "responseDesc" field for more information

## Example Response

<details class="collapsible">
  <summary>Transaction Response</summary>
  <div class="details-body">

```json
#Transaction Response Example:

{
    "token": {
        "id": "1234567",
        "type": "WICODE"
    },
    "type": "PAYMENT",
    "storeTrxDetails": {
        "storeId": 1050,
        "remoteStoreId": "10501",
        "retailerId": 999,
        "basketId": "basket1",
        "trxId": "12345",
        "posId": "workstation1",
        "cashierId": "cashier1"
    },
    "wiTrxId": 431711,
    "totalAmountProcessed": 10000,
    "basketAmountProcessed": 10000,
    "cashbackAmountProcessed": 0,
    "tipAmountProcessed": 0,
    "amountToSettle": 0,
    "billAmount": 10000,
    "vsp": {
        "id": 20016,
        "name": "wiCoupon",
        "trxId": "569023",
        "responseCode": "-1",
        "responseDesc": "Success",
        "vspRef": "CVS_20230606150456819_Grant Test"
    },
    "discount": [
        {
            "name": "API Voucher Documentation",
            "amount": 10000,
            "product": []
        }
    ],
    "loyalty": [],
    "balance": [],
    "redemptions": [
        {
            "description": "API Voucher Documentation",
            "processedAmount": 10000,
            "settleAmount": 0,
            "type": "VOUCHER",
            "vspId": 20016,
            "wiVspTrxId": 658779
        }
    ],
    "responseCode": "-1",
    "responseDesc": "Success"
}
```
</div>
</details>

### Step 4 - Transaction conclusion

Once all outstanding basket values have been settled, an Advice request must be sent to conclude the transaction using the "action": "FINALISE" tag.
In certain scenarios, a transaction must instead be REVERSED, including when:

- The basket is modified after a wiCode has already been processed
- An alternative payment method fails
- For e-commerce integrations, when the checkout process times out after a wiCode is processed and is not completed within the specified time frame

## Example Request:

<details class="collapsible">
  <summary>Advice Request</summary>
  <div class="details-body">

```json
{
    "action": "FINALISE",
    "originalTrx": {
        "wiTrxId": 431342,
        "type": "PAYMENT",
        "storeTrxDetails": {
            "retailerId": 999,
            "storeId": 10501,
            "basketId": "basket1",
            "cashierId": "cashier1",
            "posId": "workstation1",
            "trxId": "12345"
        }
    }
}
```
</div>
</details>


## Example Response:

<details class="collapsible">
  <summary>advice Response</summary>
  <div class="details-body">

```json

{
    "originalTrx": {
        "storeTrxDetails": {
            "storeId": 10501,
            "retailerId": 999,
            "basketId": "basket1",
            "trxId": "12345",
            "posId": "workstation1",
            "cashierId": "cashier1"
        },
        "wiTrxId": 431342,
        "type": "PAYMENT"
    },
    "action": "FINALISE",
    "wiTrxId": 431342,
    "responseCode": "-1",
    "responseDesc": "Success"
}
```
</div>
</details>


### Transaction History request

The transaction history request returns a list of transactions that have been processed. Transaction history may be linked to a specific retail store and is used to summarize a list of partial transactions associated with a particular basket,bill or store (or even a combination of the two).

The typical interactions for processing a transaction and performing the transaction history call is displayed in the figure below.

<img width="1113" height="676" alt="image" src="https://github.com/user-attachments/assets/f989b6c4-458e-4364-9955-dd917b5ae1e8" />

---

The transaction history request provides a summary of the transactions associated with the storeTrxDetails provided. It is recommended that the storeID (or both the remoteStoreID and retailerID) as well as the basketID is provided in the transaction history request to find all the (partial) transactions associated with a particular basket. It is, however, required that the storeID (or both the remoteStoreID and retailerID) be provided at the very minimum. The basic layout of the transaction history request looks a follows:

---

| Field                | Required/Optional | Description |
|----------------------|------------------|-------------|
| API Credentials      | Required         | API credentials issued to you by YoyoGroup. |
| dateFrom             | Optional*        | Start date for the transaction history search. Format: `yyyy-MM-dd HH:mm:ss`. |
| dateTo               | Optional*        | End date for the transaction history search. Format: `yyyy-MM-dd HH:mm:ss`. |
| pageSize             | Optional         | Number of transactions returned per page from the transaction history result set. Default is 10. |
| pageOffset           | Optional         | Page number to return from the transaction history result set. The first page starts at 0. |
| StoreTrxDetails      | Required         | Container for retailer transaction and store details. |
| BasketId             | Required         | Internal retailer basket ID. |
| RemoteStoreID        | Optional         | Retailer’s internal store identifier. Used together with RetailerID if StoreID is not provided. |
| StoreID              | Optional         | Store WID assigned by YoyoGroup. If populated, this value will be used instead of RemoteStoreID. |
| TrxID                | Required         | Retailer’s internal unique transaction ID. |

## Example Request

<details class="collapsible">
  <summary>Transaction History Request</summary>
  <div class="details-body">
    
```json
curl --location --globoff 'https://rad2.wigroup.co:8181/wigroup-transactionengine/pos-providers/transaction-history' \
--header 'id: {{api_id}}' \
--header 'password: {{api_password}}' \
--header 'apiClientVersion: {{api_client_version}}' \
--header 'apiServerVersion: {{api_server_version}}' \
--header 'Content-Type: application/json' \
--data '{
    "storeTrxDetails": {
        "storeId": "<long>",
        "retailerId": "<long>",
        "basketId": "<long>",
        "trxId": "<long>",
        "posId": "<string>",
        "cashierId": "<string>"
    },
    "dateFrom": "<string>",
    "dateTo": "<string>",
    "pageSize": 10,
    "pageOffset": 0 
}'
```
</div>
</details>


### Example 

<details class="collapsible">
  <summary>Transaction History Response</summary>
  <div class="details-body">

```json
{
    "storeTrxDetails": {
        "storeId": "<long>",
        "retailerId": "<long>",
        "basketId": "<string>",
        "trxId": "<string>",
        "posId": "<string>",
        "cashierId": "<string>"
    },
    "dateFrom": "2022-02-24 00:00:00",
    "dateTo": "2022-02-26 00:00:00",
    "pageSize": 10,
    "pageOffset": 0,
    "transactions": [
        {
            "id": "<long>",
            "type": "<string>",
            "state": "S",
            "vspId": "<long>",
            "vspName": "<string>",
            "token": {
                "id": "<string>",
                "type": "WICODE"
            },
            "billAmount": "<integer>",
            "totalAmountProcessed": "<integer>",
            "basketAmountProcessed": "<integer>",
            "cashbackAmountProcessed": "<integer>",
            "tipAmountProcessed": "<integer>",
            "amountToSettle": "<ingintegereter>",
            "createDate": "2022-02-25 12:57:13.0",
            "lastModifiedDate": "2022-10-19 09:55:43.0",
            "responseCode": "-1",
            "adviceResponseCode": "-1"
        }
    ],
    "responseCode": "-1",
    "responseDesc": "Success"
}
```
</div>
</details>

## Transaction History V2 request

The transaction history V2 request returns details on a single transaction that have been processed. Transaction history may be linked to a specific retail store and is used to request details of a specific transaction.

<img width="832" height="421" alt="image" src="https://github.com/user-attachments/assets/c89ca7d0-fbd3-4c3b-aac0-ea7d3b3acda7" />

The transaction history request provides details of the transaction stecified by the transasctionId and associated with the storeTrxDetails provided. It is mandatory to provide the transasctionId as well as the storeID (or both the remoteStoreID and retailerID). The basic layout of the transaction history request looks a follows:

| Field            | Required/Optional | Description |
|------------------|------------------|-------------|
| API Credentials  | Required         | API credentials issued to you by YoyoGroup. |
| transactionId    | Required         | Primary key for the request. |
| StoreTrxDetails  | Required         | Container for retailer transaction and store details. |
| BasketId         | Required         | Internal retailer basket ID. |
| RemoteStoreID    | Optional         | Retailer’s internal store identifier. Used together with RetailerID if StoreID is not provided. |
| StoreID          | Optional         | Store WID assigned by YoyoGroup. If populated, this value will be used instead of RemoteStoreID. |
| TrxID            | Required         | Retailer’s internal unique transaction ID. |

---

| Field | Description |
|------|-------------|
| ResponseCode & ResponseDescription | Code and description of the response. Use the VSP response for additional details to display to the user. A code of `-1` will always be associated with a **Success** description. |
| BasketAmountProcessed | The basket amount that was used in processing the transaction. |
| CashBackAmount | The amount of cash (in cents) withdrawn as part of the transaction. It can be used together with a basket payment or on its own. |
| StoreTrxDetails | Returns the same store and transaction details that were sent in the request. |
| Token (ID, Type) | Returns the same token details that were sent in the request. |
| TotalAmountProcessed | The total transaction amount in cents. This is the sum of the BasketAmount, CashBackAmount, and TipAmount. |
| Type | The transaction type processed. Possible values are `PAYMENT`, `DEPOSIT`, or `WITHDRAWAL`. |
| VSP (ID, Message, ResponseCode, ResponseDescription, TrxID) | Indicates which VSP processed the transaction and includes the VSP response code and message. The response description can be displayed to the user/customer. `TrxID` is the unique VSP transaction ID for the request. |
| wiTrxID | The unique Yoyo Platform transaction ID assigned to the transaction. This value is required for processing a subsequent **ADVISE** call. |
| Discount (Amount, Product, Id, Units) | Lists all items that were discounted in the transaction. Returned by a discount VSP, typically the YoyoGroup Coupon Voucher Server. When product information is present, a **coupon** has been redeemed; when no product information is present, a **voucher** has been redeemed. |

---

## Transaction Callback

The **Transaction Callback** allows the Yoyo Platform to notify an external system when a transaction has completed.

Once a transaction is completed (successfully or unsuccessfully), the platform sends a **POST request** to a configured callback endpoint.

This mechanism is commonly used in **SITDOWN payment integrations**, where the POS must be notified that a bill has been settled through a mobile payment.

---

## Use Case

Retailers supporting **SITDOWN transactions** may require notification when payment has been made against an open bill.

The callback can be used to:

- Close the bill in the POS system
- Update the payment status
- Prompt for an alternative payment method if the transaction fails
- Trigger reconciliation or reporting workflows

Additional transaction details can be retrieved using the **Transaction History API**.

---

## Transaction Callback Failure

To improve synchronization reliability, the Yoyo Platform retries callback requests if no response is received.

| Behaviour | Description |
|---|---|
| Retry attempts | The platform retries the callback up to **10 times** if no response is received. |
| Timeout handling | If all retry attempts fail, a fallback redemption policy should be followed. |

---

## Webhook Endpoint

The transaction callback is implemented as a **RESTful JSON webhook**.

The Yoyo Platform sends a **POST request** containing the transaction details to the configured endpoint.

### Example Endpoint

```http
POST https://{your-webhook-url}
```

| Parameter             | Type    | Description |
|-----------------------|---------|-------------|
| transactionId         | Integer | Unique identifier of the transaction on the Yoyo platform. |
| basketId              | String  | Unique identifier of the basket. |
| storeId               | Integer | Unique identifier of a store. Once-off provided by Yoyo. |
| totalAmountProcessed  | Integer | Sum of the `processedAmount` across each redemption. |
| state                 | String  | Final transaction status. ENUM: `['S','R','F']`. |


| Status | Description |
|-------|-------------|
| S     | Success     |
| F     | Failed      |
| R     | Reverse     |


# Bills API

The **Bills API** enables retailers to create and retrieve bills on the Yoyo Platform.  
Bills are typically used in **sit-down payment scenarios**, where a bill is created for a table and later settled through a mobile payment.

---

# Create Bill

Creates a new bill on the Yoyo Platform.

This endpoint is typically called by the POS when a **bill is opened or updated**, allowing the platform to display the bill to a customer for payment.

## Endpoint

```http https://rad2.wigroup.co:8181/wigroup-bill/bills ```


---

## Request Parameters

| Field | Type | Required | Description |
|---|---|---|---|
| API Credentials | Object | Yes | API credentials issued by Yoyo. |
| basketId | String | Yes | Unique identifier for the basket or bill. |
| storeId | Integer | Yes | Store identifier assigned by Yoyo. |
| billAmount | Integer | Yes | Total bill amount in cents. |
| currency | String | Yes | Currency code (e.g. ZAR). |
| description | String | Optional | Optional description of the bill. |

---

## Example Request

<details class="collapsible">
  <summary>Create Bill</summary>
  
  <div class="details-body">

```json
{
  "basketId": "BILL12345",
  "storeId": 1050,
  "billAmount": 10000,
  "currency": "ZAR",
  "description": "Table 12"
}
```
</div>
</details>


<details class="collapsible">
  <summary>Bill Response</summary>
  <div class="details-body">

### Example response

```xml
<?xml version='1.0' encoding='UTF-8'?>
<bill>
    <id>4233</id>
    <storeId>10501</storeId>
    <posId>0672ceb2-5b00-440c-8060-512822a558be</posId>
    <basketId>7820098e-54bf-484f-b23d-a3fe04016068</basketId>
    <amount>2300</amount>
    <header> |   Discovery Vitality Active    |    Rewards accepted here       | |</header>
    <qrImage>Qk2mEAAAAAAAAD4AAAAoAAAArwAAAK8AAAABAAEAAAAAAGgQAAAAAAAAAAAAAAAAAAACAAAAAAAA///////////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD//gAAAAA+D4P/+AHwf/8AAAB////+AAD//gAAAAA+D4P/+AHwf/8AAAB////+AAD//gAAAAA+D4P/+AHwf/8AAAB////+AAD//gAAAAA+D4P/+AHwf/8AAAB////+AAD//gAAAAA+D4P/+AHwf/8AAAB////+AAD//g////g+AAAf/8AAAAAAP/B8H//+AAD//g////g+AAAf/8AAAAAAP/B8H//+AAD//g////g+AAAf/8AAAAAAP/B8H//+AAD//g////g+AAAf/8AAAAAAP/B8H//+AAD//g////g+AAAf/8AAAAAAP/B8H//+AAD//g+AAPg//4AAB/4Af///wfAAAP/+AAD//g+AAPg//4AAB/4Af///wfAAAP/+AAD//g+AAPg//4AAB/4Af///wfAAAP/+AAD//g+AAPg//4AAB/4Af///wfAAAP/+AAD//g+AAPg//4AAB/4Af///wfAAAP/+AAD//g+AAPg+AH//AAAPgB8HwfAAH//+AAD//g+AAPg+AH//AAAPgB8HwfAAH//+AAD//g+AAPg+AH//AAAPgB8HwfAAH//+AAD//g+AAPg+AH//AAAPgB8HwfAAH//+AAD//g+AAPg+AH//AAAPgB8HwfAAH//+AAD//g+AAPg+AHwAB8HwfAAAAAB8H//+AAD//g+AAPg+AHwAB8HwfAAAAAB8H//+AAD//g+AAPg+AHwAB8HwfAAAAAB8H//+AAD//g+AAPg+AHwAB8HwfAAAAAB8H//+AAD//g+AAPg+AHwAB8HwfAAAAAB8H//+AAD//g////g///wAB8APgAD//g+D///+AAD//g////g///wAB8APgAD//g+D///+AAD//g////g///wAB8APgAD//g+D///+AAD//g////g///wAB8APgAD//g+D///+AAD//g////g///wAB8APgAD//g+D///+AAD//gAAAAA///wfB8AP/AD4PgB////+AAD//gAAAAA///wfB8AP/AD4PgB////+AAD//gAAAAA///wfB8AP/AD4PgB////+AAD//gAAAAA///wfB8AP/AD4PgB////+AAD//gAAAAA///wfB8AP/AD4PgB////+AAD////////+AAPgB////AD//g+D4P/+AAD////////+AAPgB////AD//g+D4P/+AAD////////+AAPgB////AD//g+D4P/+AAD////////+AAPgB////AD//g+D4P/+AAD////////+AAPgB////AD//g+D4P/+AAD//gAAHwA//4AfB8APgAAAAA+AAP/+AAD//gAAHwA//4AfB8APgAAAAA+AAP/+AAD//gAAHwA//4AfB8APgAAAAA+AAP/+AAD//gAAHwA//4AfB8APgAAAAA+AAP/+AAD//gAAHwA//4AfB8APgAAAAA+AAP/+AAD//gAAHwfAAAAfB8HwA/8AAAAD4P/+AAD//gAAHwfAAAAfB8HwA/8AAAAD4P/+AAD//gAAHwfAAAAfB8HwA/8AAAAD4P/+AAD//gAAHwfAAAAfB8HwA/8AAAAD4P/+AAD//gAAHwfAAAAfB8HwA/8AAAAD4P/+AAD//gAAAPg////gAAH//B/4P//8AP/+AAD//gAAAPg////gAAH//B/4P//8AP/+AAD//gAAAPg////gAAH//B/4P//8AP/+AAD//gAAAPg////gAAH//B/4P//8AP/+AAD//gAAAPg////gAAH//B/4P//8AP/+AAD//gB/4AfAAHwf/8AAAB//wAB8AP/+AAD//gB/4AfAAHwf/8AAAB//wAB8AP/+AAD//gB/4AfAAHwf/8AAAB//wAB8AP/+AAD//gB/4AfAAHwf/8AAAB//wAB8AP/+AAD//gB/4AfAAHwf/8AAAB//wAB8AP/+AAD///AAAPgB//wA/8H/g+D4Af+D///+AAD///AAAPgB//wA/8H/g+D4Af+D///+AAD///AAAPgB//wA/8H/g+D4Af+D///+AAD///AAAPgB//wA/8H/g+D4Af+D///+AAD///AAAPgB//wA/8H/g+D4Af+D///+AAD///B////B8H/gAAAPgB//wA+D///+AAD///B////B8H/gAAAPgB//wA+D///+AAD///B////B8H/gAAAPgB//wA+D///+AAD///B////B8H/gAAAPgB//wA+D///+AAD///B////B8H/gAAAPgB//wA+D///+AAD//gAAH/gB/4P/+AHwf/8AP/+AH//+AAD//gAAH/gB/4P/+AHwf/8AP/+AH//+AAD//gAAH/gB/4P/+AHwf/8AP/+AH//+AAD//gAAH/gB/4P/+AHwf/8AP/+AH//+AAD//gAAH/gB/4P/+AHwf/8AP/+AH//+AAD///AAHwf+D4Pg/8H////4Pg+D///+AAD///AAHwf+D4Pg/8H////4Pg+D///+AAD///AAHwf+D4Pg/8H////4Pg+D///+AAD///AAHwf+D4Pg/8H////4Pg+D///+AAD///AAHwf+D4Pg/8H////4Pg+D///+AAD//g+D4AAB8AP/AAH//B8APgAD4P/+AAD//g+D4AAB8AP/AAH//B8APgAD4P/+AAD//g+D4AAB8AP/AAH//B8APgAD4P/+AAD//g+D4AAB8AP/AAH//B8APgAD4P/+AAD//g+D4AAB8AP/AAH//B8APgAD4P/+AAD///AAHwf/////+AAPgAAH/gB8H//+AAD///AAHwf/////+AAPgAAH/gB8H//+AAD///AAHwf/////+AAPgAAH/gB8H//+AAD///AAHwf/////+AAPgAAH/gB8H//+AAD///AAHwf/////+AAPgAAH/gB8H//+AAD///AD//g/8H/g+AAPg/8HwfB////+AAD///AD//g/8H/g+AAPg/8HwfB////+AAD///AD//g/8H/g+AAPg/8HwfB////+AAD///AD//g/8H/g+AAPg/8HwfB////+AAD///AD//g/8H/g+AAPg/8HwfB////+AAD///+D4Af//4P/+AAP///4AAAD4P/+AAD///+D4Af//4P/+AAP///4AAAD4P/+AAD///+D4Af//4P/+AAP///4AAAD4P/+AAD///+D4Af//4P/+AAP///4AAAD4P/+AAD///+D4Af//4P/+AAP///4AAAD4P/+AAD//gB8APg/8H/gAAHwfAD4P///4P/+AAD//gB8APg/8H/gAAHwfAD4P///4P/+AAD//gB8APg/8H/gAAHwfAD4P///4P/+AAD//gB8APg/8H/gAAHwfAD4P///4P/+AAD//gB8APg/8H/gAAHwfAD4P///4P/+AAD////////+AH/g+D/wA//////////+AAD////////+AH/g+D/wA//////////+AAD////////+AH/g+D/wA//////////+AAD////////+AH/g+D/wA//////////+AAD////////+AH/g+D/wA//////////+AAD//gAAAAA+D4Pg+D4Pg+D4AAAAAP/+AAD//gAAAAA+D4Pg+D4Pg+D4AAAAAP/+AAD//gAAAAA+D4Pg+D4Pg+D4AAAAAP/+AAD//gAAAAA+D4Pg+D4Pg+D4AAAAAP/+AAD//gAAAAA+D4Pg+D4Pg+D4AAAAAP/+AAD//g////g/8HwfAD4P/+D4P///4P/+AAD//g////g/8HwfAD4P/+D4P///4P/+AAD//g////g/8HwfAD4P/+D4P///4P/+AAD//g////g/8HwfAD4P/+D4P///4P/+AAD//g////g/8HwfAD4P/+D4P///4P/+AAD//g+AAPg//4AAB8Hwf+D4PgAD4P/+AAD//g+AAPg//4AAB8Hwf+D4PgAD4P/+AAD//g+AAPg//4AAB8Hwf+D4PgAD4P/+AAD//g+AAPg//4AAB8Hwf+D4PgAD4P/+AAD//g+AAPg//4AAB8Hwf+D4PgAD4P/+AAD//g+AAPg//4Af+AAAA+D4PgAD4P/+AAD//g+AAPg//4Af+AAAA+D4PgAD4P/+AAD//g+AAPg//4Af+AAAA+D4PgAD4P/+AAD//g+AAPg//4Af+AAAA+D4PgAD4P/+AAD//g+AAPg//4Af+AAAA+D4PgAD4P/+AAD//g+AAPg/8HwfAD//g//4PgAD4P/+AAD//g+AAPg/8HwfAD//g//4PgAD4P/+AAD//g+AAPg/8HwfAD//g//4PgAD4P/+AAD//g+AAPg/8HwfAD//g//4PgAD4P/+AAD//g+AAPg/8HwfAD//g//4PgAD4P/+AAD//g////g///wAAAHwf+D4P///4P/+AAD//g////g///wAAAHwf+D4P///4P/+AAD//g////g///wAAAHwf+D4P///4P/+AAD//g////g///wAAAHwf+D4P///4P/+AAD//g////g///wAAAHwf+D4P///4P/+AAD//gAAAAA+AAAfAAHwA//4AAAAAP/+AAD//gAAAAA+AAAfAAHwA//4AAAAAP/+AAD//gAAAAA+AAAfAAHwA//4AAAAAP/+AAD//gAAAAA+AAAfAAHwA//4AAAAAP/+AAD//gAAAAA+AAAfAAHwA//4AAAAAP/+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAD////////////////////////////+AAA=</qrImage>
    <mobilePaymentUrl>https://rad2.wigroup.co/bill/4233</mobilePaymentUrl>
    <qrData>https://rad2.wigroup.co/bill/4233</qrData>
    <footer>
        <![CDATA[]]>
    </footer>
    <createDate>2026-02-10T14:06:42+0000</createDate>
    <expiryDate>2026-02-10T15:06:42+0000</expiryDate>
    <state>A</state>
    <cashierId>19e264b7-7501-490a-b080-e4e079014fa2</cashierId>
</bill>
```
</div>
</details>



