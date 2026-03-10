Overview of Coupon, Voucher and Giftcard Service

# CVS Issuer REST API

The **CVS Issuer API** is the underlying interface for all of **YoyoGroup's Coupon, Voucher, and Gift Card integrations**. It is the **simplest and most direct way** to issue Coupons, Vouchers, and Gift Cards from YoyoGroup.

Each CVS Issuer API method is accompanied by a **table of fields**.

Each method tabulates the available **HTTP verb** along with the list of parameters that they accept.

### Field Indicators

| Indicator | Meaning |
|---|---|
| **Bold field in Request column** | Field is mandatory |
| ✓ in Request column | Field is present in the request message |
| ✓ in Response column | Field is present in the response payload |

---

# HTTP Method Rules

| Method | Request Format |
|---|---|
| **GET** | Never has a body. Parameters are sent in the URL query string. Example: `{root}/{resource}?parameter=value` |
| **POST / PUT** | Always has a body containing a JSON object with parameters. |
| **Exception** | When issuing a coupon, the `issueWiCode` parameter must be sent as a **query parameter**, not in the body. |
| **DELETE** | No query parameters or body. Uses only the resource ID in the URL: `{root}/{resource}/{id}` |

---

# Date Format

A strict **ISO 8601 date format** is used.

Example in Java:

```java
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
```

##Monetary Format

All monetary values are:

- Accepted in cents
- Returned in cents

### Request Query Parameters

| Parameter | Type | Description |
|---|---|---|
| pageSize | Integer (Default 20) | Maximum number of items returned per page |
| pageOffset | Integer (Default 0) | Zero-based page offset into the list of results |
| orderBy | String | Field used to order the list (when ordering is supported) |
| orderDirection | String (Default "ASC") | Order direction |

---

### Pagination Response Fields

| Parameter | Type | Description |
|---|---|---|
| orderBy | String | Field used to order the list |
| orderDirection | String | Order direction |
| pageSize | Integer | Maximum number of items returned per page |
| pageOffset | Integer | Zero-based page offset |
| numItemsOnPage | Integer | Number of items returned on the current page |
| numItemsInTotal | Integer | Total number of items found |
| numPages | Integer | Total number of pages |


## Coupon Campaigns
This request returns a list of active campaigns which are linked to the specific channel (issuer interface). By default only campaigns which still have coupons left to issue will be returned.


```http GET /couponcampaigns ```

This request returns a list of active campaigns which are linked to the specific channel (issuer interface). By default only campaigns which still have coupons left to issue will be returned. If no user reference (userRef) is specified in the request it is possible that there are campaigns which the user has already reached the max allowed per user. When the user tries to issue another on the campaign they will be declined. For a better customer experience we recommend including the user reference in the request. This will hide campaigns which the user cannot issue on. A similar problem occurs when the user has reached the max allowed to be issued, but still has a coupon active for redemption. This campaign will not be included in the campaign list. To include these campaigns the includeRedeemableForUser parameter must be set to true.

Available methods: GET

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| id | Integer | The id of the campaign. | ✓ | ✓ |
| allowExpiryDateOverride | Boolean | Whether the campaign allows the default expiry days to be overwritten on issue. |  | ✓ |
| expiryDays | Integer | Default number of days the coupon is valid from issue date. Expiry is midnight x days after issue. |  | ✓ |
| adwords | List | List of adwords linked to the campaign used for filtering/search. | ✓ | ✓ |
| campaignPhase | String | Campaign phase: TODAY (activated today), CURRENT (all active), PENDING (pending activation). | ✓ |  |
| campaignType | String | Campaign type: COUPON, VOUCHER, COUPONVOUCHER. | ✓ | ✓ |
| categoryIds | List | List of category IDs. Returns campaigns matching any supplied category. | ✓ |  |
| merchantId | Integer | Merchant ID used to filter campaigns. | ✓ |  |
| provinceId | Integer | Province ID used to filter campaigns. | ✓ |  |
| countryId | Integer | Country ID used to filter campaigns. | ✓ |  |
| retailerId | Integer | Retailer ID used to filter campaigns. | ✓ |  |
| longitude | Double | Longitude position used for distance calculation (must be used with latitude). | ✓ |  |
| latitude | Double | Latitude position used for distance calculation (must be used with longitude). | ✓ |  |
| userRef | String | Unique user reference. Returns campaigns the user can still receive coupons for. | ✓ |  |
| includeRedeemableForUser | Boolean | Include campaigns where user still has redeemable coupons/vouchers but cannot be issued more. Must be used with userRef. | ✓ |  |
| totalViewed | Integer | Total number of times the coupon campaign was viewed. |  | ✓ |
| issueFromDate | String | Date from which the campaign may be issued. |  | ✓ |
| issueToDate | String | Date until which the campaign may be issued. |  | ✓ |
| redeemFromDate | String | Date from which the campaign may be redeemed. |  | ✓ |
| redeemToDate | String | Date until which the campaign may be redeemed. |  | ✓ |
| minBasketValue | Integer | Minimum basket value required to redeem. |  | ✓ |
| maxBasketValue | Integer | Maximum basket value allowed to redeem. |  | ✓ |
| name | String | Campaign name. |  | ✓ |
| description | String | Campaign description. |  | ✓ |
| termsAndConditions | String | Campaign terms and conditions. |  | ✓ |
| imageUrl | String | Image URL associated with the campaign. |  | ✓ |
| createDate | String | Date the campaign was created. |  | ✓ |
| requireUserRef | Boolean | Indicates if a userRef is required for the campaign. |  | ✓ |
| allowedUsersRestricted | Boolean | Indicates whether only users linked to the campaign can view its content. |  | ✓ |
| maxNumberPerUser | Integer | Maximum number allowed per user. |  | ✓ |
| maxRedemptionsPerUserPerDay | Integer | Maximum redemptions allowed per user per day. |  | ✓ |
| maxLivePerUser | Integer | Maximum number of live (issued but not redeemed/expired) coupons per user. |  | ✓ |
| minRank | Integer | Minimum rank of the campaign. |  | ✓ |
| categories | Object | Category details including name (String), id (Long), and rank (Integer). |  | ✓ |
| distance | Double | Distance to the closest merchant. |  | ✓ |
| maxAllowedToIssue | Integer | Maximum number allowed to be issued in total. |  | ✓ |
| maxAllowedToIssueDaily | Integer | Maximum number allowed to be issued daily. |  | ✓ |
| maxRedemptionRuleAmount | Integer | Maximum redemption rule amount. |  | ✓ |
| discountType | String | Discount type: fixed value or percentage. |  | ✓ |
| percentageDiscount | Integer | Percentage discount value (only available for voucher campaigns). |  | ✓ |
| totalLive | Integer | Total live coupons/vouchers on the campaign. |  | ✓ |
| totalRedeemed | Integer | Total redeemed coupons/vouchers on the campaign. |  | ✓ |
| totalExpired | Integer | Total expired coupons/vouchers on the campaign. |  | ✓ |
| totalIssued | Integer | Total number issued (live + redeemed). |  | ✓ |
| totalIssuedToday | Integer | Total number issued today. |  | ✓ |
| campaignInfo | Object | Campaign info details including campaign name and value. |  | ✓ |
| stateId | String | Coupon state: A (Active), D (Deactivated), E (Expired), R (Redeemed). | ✓ |  |
| campaignRedemptionRestrictions | Long | Specifies which days the reward can be redeemed. If restrictions exist for a specific day the value will be FALSE. |  | ✓ |


---

# Example request

<details class="collapsible">
  <summary>GET/couponcampaigns</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/couponcampaigns" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```

# Example response

```json
{
        "issueFromDate": "2017-09-11T00:00:00+0200",
        "issueToDate": "2017-09-12T23:59:59+0200",
        "redeemFromDate": "2017-09-11T00:00:00+0200",
        "redeemToDate": "2017-09-12T23:59:59+0200",
        "minBasketValue": 0,
        "maxBasketValue": 0,
        "maxRedemptionsPerUserPerDay": 0,
        "id": 48184,
        "name": "% discount",
        "description": "% discount",
        "termsAndConditions": "% discount",
        "createDate": "2017-09-11T11:31:43+0200",
        "requireUserRef": false,
        "allowedUsersRestricted": false,
        "maxNumberPerUser": 1,
        "maxLivePerUser": 0,
        "campaignType": "VOUCHER",
        "minRank": 11,
        "categories": [         {
           "name": "All",
           "id": 782,
           "rank": 11
        }],
        "maxAllowedToIssue": 10,
        "maxAllowedToIssueDaily": 10,
        "maxRedemptionRuleAmount": 100000,
        "discountType": "Percentage",
        "percentageDiscount": 20,
        "totalLive": 0,
        "totalRedeemed": 0,
        "totalExpired": 0,
        "totalIssued": 0,
        "totalIssuedToday": 0,
        "campaignInfo": [         {
           "name": "Adword",
           "value": "%,discount,% discount"
        }],
        "stateId": "A",
        "allowExpiryDateOverride": false,
        "expiryDays": 1096,
        "campaignRedemptionRestrictions":          {
           "redeemabableOnSundays": true,
           "redeemabableOnMondays": true,
           "redeemabableOnTuesdays": true,
           "redeemabableOnWednesdays": true,
           "redeemabableOnThursdays": true,
           "redeemabableOnFridays": true,
           "redeemabableOnSaturdays": true,
           "redemptionFromTime": "00:00",
           "redemptionToTime": "00:00"
        }
     }
  ],
  "paging":    {
     "pageSize": 20,
     "pageOffset": 0,
     "numItemsOnPage": 11,
     "numItemsInTotal": 11,
     "numPages": 1
  },
  "responseCode": "-1",
  "responseDesc": "Success"
```
  </div>
</details>

### GET /couponcampaigns/{campaignId}

When specific campaign's details are required rather than pulling the entire campaign list down, this endpoint can be used to retrieve the single campaign's details.

Endpoint: ```curl {root}/couponcampaigns/{campaignId}```

Available methods: GET

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| campaignId | Integer | The id of the campaign. | ✓ | ✓ |
| allowExpiryDateOverride | Boolean | Whether the campaign allows the default expiry days to be overwritten on issue. |  | ✓ |
| expiryDays | Integer | Default number of days the coupon will be valid from issue date. Expiry is midnight x days after issue. |  | ✓ |
| totalViewed | Integer | Total number of times the coupon campaign was viewed. |  | ✓ |
| issueFromDate | String | Date from which the campaign may be issued. |  | ✓ |
| issueToDate | String | Date until which the campaign may be issued. |  | ✓ |
| redeemFromDate | String | Date from which the campaign may be redeemed. |  | ✓ |
| redeemToDate | String | Date until which the campaign may be redeemed. |  | ✓ |
| minBasketValue | Integer | Minimum basket value required to redeem. |  | ✓ |
| maxBasketValue | Integer | Maximum basket value allowed to redeem. |  | ✓ |
| name | String | Campaign name. |  | ✓ |
| description | String | Campaign description. |  | ✓ |
| termsAndConditions | String | Campaign terms and conditions. |  | ✓ |
| imageUrl | String | Image URL associated with the campaign. |  | ✓ |
| createDate | String | Date the campaign was created. |  | ✓ |
| requireUserRef | Boolean | Indicates if a userRef is required for the campaign. |  | ✓ |
| allowedUsersRestricted | Boolean | Whether only users linked to the campaign can view its contents. |  | ✓ |
| maxNumberPerUser | Integer | Maximum number allowed per user. |  | ✓ |
| maxRedemptionsPerUserPerDay | Integer | Maximum number of redemptions per user per day. |  | ✓ |
| maxLivePerUser | Integer | Maximum number of live (issued but not redeemed/expired) coupons per user. |  | ✓ |
| minRank | Integer | Minimum rank of the campaign. |  | ✓ |
| categories | Object | Category details including name (String), id (Long), and rank (Integer). |  | ✓ |
| distance | Double | Distance to the closest merchant. |  | ✓ |
| maxAllowedToIssue | Integer | Maximum number allowed to be issued in total. |  | ✓ |
| maxAllowedToIssueDaily | Integer | Maximum number allowed to be issued daily. |  | ✓ |
| maxRedemptionRuleAmount | Integer | Maximum redemption rule amount. |  | ✓ |
| discountType | String | Discount type: fixed value or percentage. |  | ✓ |
| percentageDiscount | Integer | Percentage discount value (only available for voucher campaigns). |  | ✓ |
| totalLive | Integer | Total live coupons/vouchers on the campaign. |  | ✓ |
| totalRedeemed | Integer | Total number redeemed from the campaign. |  | ✓ |
| totalExpired | Integer | Total number expired from the campaign. |  | ✓ |
| totalIssued | Integer | Total number issued (live + redeemed). |  | ✓ |
| totalIssuedToday | Integer | Total number issued today. |  | ✓ |
| campaignInfo | Object | Campaign info details including campaign name and value. |  | ✓ |
| stateId | String | Current state of the coupon: A (Active), D (Deactivated), E (Expired), R (Redeemed). | ✓ |  |


# Example request

<details class="collapsible">
  <summary>GET/couponcampaigns/{campaignId}</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/couponcampaigns/{campaignId}" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```
# Example response

```json
{
  "couponCampaigns": [ {
    "allowExpiryDateOverride": false,
    "expiryDays":0,
    "totalViewed": 0,
    "issueFromDate": "2015-05-21T00:00:00+0200",
    "issueToDate": "2015-11-21T23:59:59+0200",
    "redeemFromDate": "2015-05-21T00:00:00+0200",
    "redeemToDate": "2015-11-21T23:59:59+0200",
    "minBasketValue": 0,
    "maxBasketValue": 0,
    "maxRedemptionsPerUserPerDay": 0,
    "id": 4449,
    "name": "Coupon R5",
    "description": "Coupon R5",
    "termsAndConditions": "VSP T&Cs",
    "imageURL": "http://goo.gl/Zodst9",
    "createDate": "2015-05-21T07:14:01+0200",
    "requireUserRef": false,
    "allowedUsersRestricted": false,
    "maxNumberPerUser": 0,
    "maxLivePerUser": 0,
    "campaignType": "COUPON",
    "minRank": 3,
    "categories": [ {
      "name": "All",
      "id": 807,
      "rank": 3
    }],
    "maxAllowedToIssue": 100000,
    "maxAllowedToIssueDaily": 100000,
    "maxRedemptionRuleAmount": 500,
    "discountType": "Percentage",
    "percentageDiscount": 20,
    "totalLive": 1,
    "totalRedeemed": 37,
    "totalExpired": 1,
    "totalIssued": 38,
    "totalIssuedToday": 4,
    "campaignInfo": [ {
      "name": "Adword",
      "value": "Integration,Gift,Card,R5,Integration Gift Card R5"
    }],
    "stateId": "A"
  }],
  "paging":
  {
    "pageSize": 20,
    "pageOffset": 0,
    "numItemsOnPage": 1,
    "numItemsInTotal": 1,
    "numPages": 1
  },
  "responseCode": "-1",
  "responseDesc": "Success"
}
```
  </div>
</details>

## GET /couponcampaigns/{campaignId}/skus

If the campaign type is COUPON, the campaign will have SKUs linked to it. This endpoint can be used to retrieve the list of SKUs linked to the campaign.

```http Endpoint: {root}/couponcampaigns/{campaignId}/skus ```

Available methods: GET

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| campaignId | Integer | The id of the campaign. | ✓ | ✓ |
| skus | Array | List of SKUs. Each SKU object contains: sku (String), sku value (Integer), sku brand (String), sku product (String), and product size (String). |  | ✓ |

# Example request

<details class="collapsible">
  <summary>GET/couponcampaignId/skus</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/couponcampaigns/{campaignId}/skus" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```

# Example response

```json
{
  "responseCode": "-1",
  "responseDesc": "Success!",
  "skus": [
    {
      "sku": "string",
      "value": 0,
      "brand": "string",
      "product": "string",
      "size": "string"
    }
  ]
}
```
  </div>
</details>

---

Coupons
The fundamental difference between a Yoyo Coupon and Voucher is that a Coupon is associated with a particular SKU (or set of SKUs) being in the basket, while a voucher is merely a fixed discount amount, or percentage discount amount off the total basket amount processed during tender.

The issuing and redemption of Yoyo Coupons and Vouchers are identical when utilising the CVS Issuer API.

### POST /coupons

This resource can be used to issue new coupons (coupon/voucher). Issuing a Coupon or Voucher is a straight forward API call. Two parameters must be supplied: campaignID and userRef. Each time this API is called a Coupon or Voucher is issued against the userRef and the campaignID.

Endpoint: {root}/coupons

Available methods: POST

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| issueWiCode | Boolean (query parameter) | Whether to issue a wiCode linked to the coupon for single redemption. Default is false. | ✓ |  |
| campaignId | Integer | The id of the campaign to issue against. | ✓ | ✓ |
| userRef | String | A unique user reference on the issuer channel system. Used to enforce campaign user limits. | ✓ | ✓ |
| mobileNumber | String | The mobile number in international format. | ✓ | ✓ |
| smsMessage | String | Overrides the original SMS message. Supports hashtags: **#WICODE**, **#MOBISITE**, **#BALANCE**, **#EXPIRY**. | ✓ |  |
| sendSMS | Boolean | Indicates whether an SMS should be sent on issue. | ✓ | ✓ |
| additionalInfo | String | Additional information stored with the coupon and retrievable later. |  | ✓ |
| serviceProviderId | Integer | Airtime provider ID for Airtime Reward campaigns. If not specified, the system attempts to determine the provider automatically. | ✓ | ✓ |
| id | Integer | The id of the coupon. |  | ✓ |
| campaignName | String | The name of the campaign. |  | ✓ |
| campaignType | String | Campaign type: **COUPON** or **VOUCHER**. |  | ✓ |
| termsAndConditions | String | Terms and conditions of the coupon. |  | ✓ |
| numExpiryDays | Integer | Number of expiry days. Used in request only. | ✓ |  |
| createDate | String | The date the coupon campaign was created. |  | ✓ |
| description | String | Description of the campaign. |  | ✓ |
| imageURL | String | Image URL of the coupon. |  | ✓ |
| redeemFromDate | String | Date from which the coupon can be redeemed. |  | ✓ |
| redeemToDate | String | Date until which the coupon is redeemable. |  | ✓ |
| wiCode | String | The wiCode linked to the user token. |  | ✓ |
| voucherAmount | Integer | Possible coupon redemption amount. |  | ✓ |
| wiQR | String | QR representation of the wiCode linked to the user token. |  | ✓ |

# Example request

<details class="collapsible">
  <summary>POST/IssueCoupon</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/coupons?issueWiCode=true" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
       "campaignId": "9000",
       "userRef": "VSP_User_Id_001",
       "mobileNumber": "string",
       "smsMessage": "string",
       "sendSMS": boolean
     }'
```

# Example response

```json
{  
  “coupon”: {
    “id”: 20,
    “userRef”: "VSP_User_Id_001",
    “campaignId”: 9000,
    “campaignName”: "VSPTestCouponCampaign",
    “campaignType”: "COUPON",
    “termsAndConditions”: "VSP T&Cs",
    “createDate”: "2015-05-15T15:10:07+0200",
    “description”: "R5.00 off SKU 1234",
    “redeemFromDate”: "2015-05-15T15:10:07+0200",
    “redeemToDate”: "2015-08-22T23:59:59+0200",
    “wiCode”: 1234567,
    “voucherAmount”: 500,
    “wiQr”: 1234567
  },
  “responseCode”: "-1",
  “responseDesc”: "Success"
}
```
  </div>
</details>

### Coupon Transactions

## GET /coupontransactions

This endpoint can be used to retrieve all coupon related transactions. If no filters are specified it will return all coupon transactions ordered by date.

Endpoint: {root}/coupontransactions
Available methods: GET

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| transactionId | Integer | The top level transaction id. Groups multiple coupon and gift card transactions under a single transaction. | ✓ | ✓ |
| userRef | String | If specified, transactions linked to the user will be returned. | ✓ | ✓ |
| couponId | Integer | Filter transactions for a specific coupon ID. | ✓ | ✓ |
| wiCode | String | The wiCode linked to the user token. |  | ✓ |
| campaignId | Integer | Filter transactions for a specific campaign. | ✓ | ✓ |
| dateFrom | String | The date from which to return transactions. | ✓ |  |
| dateTo | String | The date to which to return transactions. | ✓ |  |
| id | Integer | The coupon transaction id. |  | ✓ |
| merchantId | Integer | The merchant id. |  | ✓ |
| merchantName | String | The merchant name. |  | ✓ |
| retailerId | Integer | The retailer id. |  | ✓ |
| retailerName | String | The retailer name. |  | ✓ |
| processedAmount | Integer | The processed amount. |  | ✓ |
| transactionDate | String | The transaction date. |  | ✓ |
| interfaceIssuerId | String | The id of the interface issuer. |  | ✓ |
| issuerId | String | The id of the issuer. |  | ✓ |
| stateId | Integer | Current transaction state: N (New), P (Processing), F (Failed), SPR (Successful pending recon), S (Successful), RV (Reversed). | ✓ |  |

# Example request

<details class="collapsible">
  <summary>GET/coupontransctions</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/coupontransactions" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```
# Example response

```json
{
  “couponTransactions“: [ {
    “id“: 123499,
    “transactionId“: 101694,
    “couponId“: 183980,
    “wiCode“: "528626479",
    “userRef“: "VSP_User_Id_001",
    “merchantId“: 1050,
    “merchantName“: "Test Merchant",
    “retailerId“: 10,
    “retailerName“: "YourRetailer",
    “processedAmount“: 2500,
    “transactionDate“: "2015-07-23T14:07:24+0200",
    “interfaceIssuerId“: "AAIssuer",
    “issuerId“: 128,
    “stateId“: "S"
  }],
  “paging“:
  {
    “pageSize“: 20,
    “pageOffset“: 0,
    “numItemsOnPage“: 1,
    “numItemsInTotal“: 1,
    “numPages“: 1
  },
  “responseCode“: "-1",
  “responseDesc“: "Success"
}
```
  </div>
</details>

### GET /coupontransactions/{id}

This resource returns a specific coupon transaction.

Endpoint: {root}/coupontransactions/{id}
Available methods: GET


| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| id | Integer | The coupon transaction id. | ✓ | ✓ |
| transactionId | Integer | The top level transaction id. Groups multiple coupon and gift card transactions under one transaction. |  | ✓ |
| couponId | Integer | Filter on a specific coupon ID. |  | ✓ |
| wiCode | String | The wiCode linked to the user token. |  | ✓ |
| userRef | String | If specified, transactions linked to the user will be returned. |  | ✓ |
| merchantId | Integer | The merchant id. |  | ✓ |
| merchantName | String | The merchant name. |  | ✓ |
| retailerId | Integer | The retailer id. |  | ✓ |
| retailerName | String | The retailer name. |  | ✓ |
| processedAmount | Integer | The processed amount. |  | ✓ |
| transactionDate | String | The transaction date. |  | ✓ |
| interfaceIssuerId | String | The id of the interface issuer. |  | ✓ |
| issuerId | String | The id of the issuer. |  | ✓ |
| stateId | Integer | The current transaction state: N (New), P (Processing), F (Failed), SPR (Successful pending recon), S (Successful), RV (Reversed). | ✓ |  |


# Example request

<details class="collapsible">
  <summary>GET/coupontranactionsid</summary>
  <div class="details-body">


```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/coupontransactions/{id}" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```
# Example response

```json
{
  “couponTransactions“: [ {
    “id“: 123499,
    “transactionId“: 101694,
    “couponId“: 183980,
    “wiCode“: "528626479",
    “userRef“: "VSP_User_Id_001",
    “merchantId“: 1050,
    “merchantName“: "Test Merchant",
    “retailerId“: 10,
    “retailerName“: "YourRetailer",
    “processedAmount“: 2500,
    “transactionDate“: "2015-07-23T14:07:24+0200",
    “interfaceIssuerId“: "AAIssuer",
    “issuerId“: 128,
    “stateId“: "S"
  }],
  “paging“:
  {
    “pageSize“: 20,
    “pageOffset“: 0,
    “numItemsOnPage“: 1,
    “numItemsInTotal“: 1,
    “numPages“: 1
  },
  “responseCode“: "-1",
  “responseDesc“: "Success"
}
```
  </div>
</details>

### Giftcards
This resource is used to issue a giftcard. Depending on how the campaign is setup by the campaign owner, the channel can set the balance and expiry date of the giftcard. A giftcard can be redeemed multiple times.


## Create (issue) a Gift Card

Issuing a Gift card is a simple API call. Three parameters must be supplied: campaignID, balance and userRef. Each time this API is called a Gift card is issued against the userRef and the campaignID. If issueWicode is set to true, a wiCode will be issued for the gift card right away. If not, a wiCode can be issued using the issueGiftCardWicode API call.

A Gift Card can be ISSUED in a De-Activated State, then activated later via the portal or API call.

Endpoint: {root}/giftcards
Available methods: POST

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| issueWiCode | Boolean (query) | True if a wiCode should be issued, otherwise false. | ✓ |  |
| campaignId | Integer | The id of the campaign to issue against. | ✓ | ✓ |
| balance | Integer | Amount to issue on the gift card. Must fall within the campaign min/max limits and available float. | ✓ | ✓ |
| userRef | String | Unique user reference on the issuer channel system used to enforce campaign user limits. | ✓ | ✓ |
| mobileNumber | String | Mobile number in international format. | ✓ | ✓ |
| smsMessage | String | Overrides the original SMS message. Supports hashtags: **#WICODE**, **#MOBISITE**, **#BALANCE**, **#EXPIRY**. | ✓ |  |
| sendSMS | Boolean | Indicates whether an SMS should be sent on issue. | ✓ | ✓ |
| sendFollowUpSMS | Boolean | Indicates whether a follow-up SMS should be sent if the user partially redeems the gift card. | ✓ | ✓ |
| numExpiryDays | Integer | If allowed by the campaign, overrides default expiry days (still capped by campaign limit). | ✓ |  |
| id | Integer | The id of the gift card. |  | ✓ |
| interfaceIssuerId | Integer | The interface issuer id. |  | ✓ |
| issuerId | Integer | The issuer id. |  | ✓ |
| issuedAmount | Integer | The issued amount on the gift card. |  | ✓ |
| redeemedAmount | Integer | The amount redeemed from the gift card. |  | ✓ |
| expiredAmount | Integer | The amount expired on the gift card. |  | ✓ |
| createDate | String | The creation date of the gift card. |  | ✓ |
| expiredDate | String | The date on which the gift card expired. |  | ✓ |
| campaignName | String | The name of the campaign. |  | ✓ |
| campaignType | String | The type of campaign: **GIFTCARD**. |  | ✓ |
| description | String | Description of the campaign. |  | ✓ |
| imageUrl | String | Image URL of the gift card. |  | ✓ |
| termsAndConditions | String | Terms and conditions of the gift card. |  | ✓ |
| stateId | String | Current state of the gift card. Can also be set in request to issue a deactivated card. States: **A (Active), D (Deactivated), E (Expired), R (Redeemed fully)**. | ✓ | ✓ |

# Example request


<details class="collapsible">
  <summary>POST/issueGiftcard</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/giftcards?issueWiCode=true" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
       "campaignId": "9000",
       "balance": "2000"
       "userRef": "VSP_User_Id_001",
       "mobileNumber": "string",
       "numExpiryDays": 3,
       "smsMessage": "Dear Customer. You have #BALANCE value to spend. wiCode: #WICODE. Expires: #EXPIRY.",
       "sendSMS": boolean,
       "sendFollowUpSMS": boolean,
       "stateId": "A"
     }'

```

# Example response

```json
{
  “giftcard”: {
    “id”: 20,
    “campaignId”: 9000,
    “interfaceIssuerId”: "TestChannel",
    “issuerId”: 94,
    “userRef”: "VSP_User_Id_001",
    “mobileNumber”: "string",
    “sendSMS”: boolean,
    “sendFollowUpSMS”: boolean,
    “issuedAmount”: 2000,
    “redeemedAmount”: 0,
    “expiredAmount”: 0,
    “balance”: 2000,
    “createDate”: "2015-03-13T15:10:07+0200",
    “expiryDate”: "2018-03-12T23:59:59+0200",
    “campaignName”: "VSPTestGiftCardCampaign",
    “campaignType”: "GIFTCARD",
    “description”: "Gift Card worth R20.00",
    “imageURL”: "http://goo.gl/Zodst9",
    “termsAndCondition: "VSP T&Cs",
    “stateId”: "A",
    “wicode”: "123456789999"
  },
  “responseCode”: "-1",
  “responseDesc”: "Success"
}
```
  </div>
</details>


### Issue a wiCode / Token for a Gift Card
This resource is used to issue a wicode against giftcard for length of giftcard expiry. A wicode will only be issued if the wiCode field of a particular Giftcard is NULL.

Endpoint: {root}/giftcards/{id}/wicode
Available methods: POST

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| userRef | String | Unique user reference on the issuer channel system used to enforce campaign user limits. |  | ✓ |
| wiCode | String | The wiCode. |  | ✓ |
| createDate | String | The creation date. |  | ✓ |
| validTillDate | String | The date the wiCode is valid until. |  | ✓ |
| lastModifiedDate | String | The last modified date. |  | ✓ |
| stateId | String | Current state of the gift card: **A (Active), D (Deactivated), E (Expired), R (Redeemed fully)**. | ✓ |  |

# Example request

<details class="collapsible">
  <summary>POST/issueGiftcardWicode</summary>
  <div class="details-body">


```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/giftcards/{id}/wicode" \
  -H "id: {id}" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X POST
```

# Example response

```json{
   "token":    {
      "userRef": "string",
      "wiCode": "123456789999",
      "createDate": "2016-07-06T12:05:10+0200",
      "validTillDate": "2019-07-06T23:59:59+0200",
      "lastModifiedDate": "2016-07-06T12:05:10+0200",
      "stateId": "A"
   },
   "responseCode": "-1",
   "responseDesc": "Success"
}
```

  </div>
</details>

### Giftcard Campaigns
This request returns a list of active campaigns which are linked to the specific channel (issuer interface).

GET /giftcardcampaigns
This endpoint returns all the active Gift Card campaigns which can be issued against. A campaign can be issued against if it has the required min float and the user has not yet reached the max allowed per user limit.

Available methods: GET

All requested parameters, except the API credentials, are query parameters.

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| id | Integer | The id of the campaign. | ✓ | ✓ |
| adwords | List | List of adwords linked to the campaign. Used as searchable keywords to filter campaigns. | ✓ |  |
| categoryIds | List | List of category IDs. Returns campaigns matching any supplied category. | ✓ |  |
| merchantId | Integer | Merchant ID used to filter campaigns. | ✓ |  |
| provinceId | Integer | Province ID used to filter campaigns. | ✓ |  |
| countryId | Integer | Country ID used to filter campaigns. | ✓ |  |
| retailerId | Integer | Retailer ID used to filter campaigns. | ✓ |  |
| longitude | Double | Longitude position used for distance calculation. Must be used with latitude. | ✓ |  |
| latitude | Double | Latitude position used for distance calculation. Must be used with longitude. | ✓ |  |
| userRef | String | Unique user reference. Returns campaigns where the user can still be issued gift cards. | ✓ |  |
| includeRedeemableForUser | Boolean | Include campaigns where the user still has redeemable vouchers but cannot be issued more. Must be used with userRef. | ✓ |  |
| minValueAllowedToIssue | Integer | Minimum value of a gift card that can be issued. |  | ✓ |
| maxValueAllowedToIssue | Integer | Maximum value of a gift card that can be issued. |  | ✓ |
| totalAmountIssued | Integer | Total amount (in cents) issued to gift cards. |  | ✓ |
| totalAmountRedeemed | Integer | Total amount (in cents) redeemed on the campaign. |  | ✓ |
| allowedExpiryDateOverride | Boolean | Whether the expiry date may be overridden. |  | ✓ |
| name | String | Name of the gift card campaign. |  | ✓ |
| description | String | Description of the gift card campaign. |  | ✓ |
| termsAndConditions | String | Terms and conditions of the gift card campaign. |  | ✓ |
| imageUrl | String | Image URL of the gift card campaign. |  | ✓ |
| createDate | String | Date the gift card campaign was created. |  | ✓ |
| requireUserRef | Boolean | Indicates if a userRef is required for the campaign. |  | ✓ |
| allowedUsersRestricted | Boolean | Indicates whether only users linked to the campaign can view its content. |  | ✓ |
| maxLivePerUser | Integer | Maximum number of live (issued but not redeemed/expired) gift cards per user. |  | ✓ |
| maxNumberPerUser | Integer | Maximum number allowed per user. |  | ✓ |
| campaignType | String | Campaign type: **GIFTCARD**. |  | ✓ |
| minRank | Integer | Minimum rank of the gift card campaign. |  | ✓ |
| categories | Object | Category details including name (String), id (Integer), and description (Long). |  | ✓ |
| distance | Double | Distance to the closest merchant. |  | ✓ |
| totalExpired | Integer | Total number of gift cards expired. |  | ✓ |
| totalIssued | Integer | Total number of gift cards issued (live + redeemed). |  | ✓ |
| totalLive | Integer | Total number of live (active) gift cards. |  | ✓ |
| totalRedeemed | Integer | Total number of gift cards redeemed. |  | ✓ |
| campaignInfo | Object | Campaign info details including campaign name and value. |  | ✓ |
| stateId | String | Current state of the gift card: **A (Active), D (Deactivated), E (Expired), R (Redeemed fully)**. | ✓ |  |
| allowExpiryDateOverride | Boolean | Whether the campaign allows default expiry days to be overwritten on issue. |  | ✓ |
| expiryDays | Integer | Default number of days the gift card is valid from issue date. Expiry occurs at midnight after the specified days. |  | ✓ |

# Example request

<details class="collapsible">
  <summary>GET/giftcardCampaigns</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/giftcardcampaigns" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```

# Example response

```json
{
  “giftcardCampaigns“: [ {
    “minValueAllowedToIssue“: 500,
    “maxValueAllowedToIssue“: 500,
    “totalAmountIssued“: 500,
    “totalAmountRedeemed“: 6000,
    “id“: 4420,
    “name“: "Gift Card R5",
    “description“: "Gift Card R5",
    “termsAndConditions“: "VSP T&Cs",
    “imageURL”: "http://goo.gl/Zodst9",
    “createDate“: "2015-05-21T07:14:01+0200",
    “requireUserRef“: false,
    “allowedUsersRestricted“: false,
    “maxNumberPerUser“: 0,
    “maxLivePerUser“: 0,
    “campaignType“: "GIFTCARD",
    “minRank“: 2,
    “categories“: [   {
      “name“: "All",
      “id“: 807,
      “rank“: 3
    }],
    “totalLive“: 1,
    “totalRedeemed“: 6000,
    “totalExpired“: 1,
    “totalIssued“: 14,
    “campaignInfo“: [{
      “name“: "Adword",
      “value“: "Integration,Gift,Card,R5,Integration Gift Card R5"
    }],
    “stateId“: "A",
    "allowExpiryDateOverride": false,
    "expiryDays": 1095
  }],
  “paging“:   {
    “pageSize“: 20,
    “pageOffset“: 0,
    “numItemsOnPage“: 1,
    “numItemsInTotal“: 1,
    “numPages“: 1
  },
  “responseCode“: "-1",
  “responseDesc“: "Success"
}
```

  </div>
</details>


### GET /giftcardcampaigns/{id}

This endpoint returns all the active Gift Card campaigns which can be issued against. A campaign can be issued against if it has the required min float and the user has not yet reached the max allowed per user limit.

Available methods: GET

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| id | Integer | The id of the campaign. | ✓ | ✓ |
| minValueAllowedToIssue | Integer | The minimum value of a gift card that can be issued. |  | ✓ |
| maxValueAllowedToIssue | Integer | The maximum value of a gift card that can be issued. |  | ✓ |
| totalAmountIssued | Integer | Total amount (in cents) issued to gift cards. |  | ✓ |
| totalAmountRedeemed | Integer | Total amount (in cents) redeemed on the campaign. |  | ✓ |
| allowedExpiryDateOverride | Boolean | Whether the expiry date may be overridden. |  | ✓ |
| name | String | The name of the gift card campaign. |  | ✓ |
| description | String | The description of the gift card campaign. |  | ✓ |
| termsAndConditions | String | The terms and conditions of the gift card campaign. |  | ✓ |
| imageUrl | String | The image URL of the gift card campaign. |  | ✓ |
| createDate | String | The date the gift card campaign was created. |  | ✓ |
| requireUserRef | Boolean | True if a userRef is required for the gift card campaign. |  | ✓ |
| allowedUsersRestricted | Boolean | Whether only users linked to the campaigns are able to view the contents of the campaign. |  | ✓ |
| maxLivePerUser | Integer | Maximum number of live (issued but not redeemed/expired) gift cards allowed per user. |  | ✓ |
| maxNumberPerUser | Integer | Maximum number allowed per user. |  | ✓ |
| campaignType | String | Campaign type: **GIFTCARD**. |  | ✓ |
| minRank | Integer | The minimum rank of the gift card campaign. |  | ✓ |
| categories | Object | Category details including name (String), id (Integer), and description (Long). |  | ✓ |
| distance | Double | Distance to the closest merchant. |  | ✓ |
| totalExpired | Integer | Total number of gift cards expired. |  | ✓ |
| totalIssued | Integer | Total number of gift cards issued (live + redeemed). |  | ✓ |
| totalLive | Integer | Total number of live (active) gift cards. |  | ✓ |
| totalRedeemed | Integer | Total number of gift cards redeemed. |  | ✓ |
| campaignInfo | Object | Campaign info details including campaign name (String) and campaign info value (String). |  | ✓ |
| stateId | String | Current state of the gift card: **A (Active), D (Deactivated), E (Expired), R (Redeemed fully)**. | ✓ |  |
| allowExpiryDateOverride | Boolean | Whether the campaign allows the default expiry days to be overwritten on issue. |  | ✓ |
| expiryDays | Integer | Default number of days the coupon will be valid from issue date. Expiry is midnight after the specified days. |  | ✓ |



### Retrieve Campaign Metadata

This API call retrieves **all metadata entries linked to a given campaign**.

**Metadata** refers to additional information stored against a campaign by the **Campaign Owner**.  
It is configured during campaign setup and can be used for purposes such as:

- Storing an **external system campaign identifier**
- Adding **tags** to help applications group campaigns

---
**Available Methods**

`GET`

---

### Parameters

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| campaignId | Integer | Filter metadata for a specific campaign. | ✓ |  |
| name | String | Name of the key–value pair. |  | ✓ |
| value | String | Value of the key–value pair. |  | ✓ |



### Retrieve Retailers Linked to Active Campaigns

Returns a list of **retailers linked to active campaigns on the channel**.

---

**GET /retailers*

**Available Methods**

`GET`

---

### Parameters

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| campaignId | Integer (query) | If specified, returns retailers linked to the campaign. | ✓ |  |
| createDate | String | The date the retailer was created. |  | ✓ |
| id | Integer | The id of the retailer. |  | ✓ |
| name | String | The name of the retailer. |  | ✓ |
| description | String | A description of the retailer. |  | ✓ |
| logoURL | String | The retailer logo URL. |  | ✓ |

<details class="collapsible">
  <summary>GET/retailers</summary>
  <div class="details-body">

# Example request

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/retailers" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```


# Example response

```json
{
  retailers: [ {
    “createDate“: "2013-03-19T07:34:02+0200",
    “id“: 1050,
    “name“: "Test Retailer",
    “description“: "The best tester",
    “logoURL“: "www.testurl.com/image.png"
  }],
  “responseCode“: "-1",

  “responseDesc“: "Success"
}
```
  </div>
</details>


### Retrieve Merchants Linked to Active Campaigns

This resource returns the **collection of all merchants linked to active campaigns on the channel**.

---

**Endpoint**


**Available Methods**

`GET`

---

### Parameters

| Parameter | Type | Description | Request | Response |
|---|---|---|---|---|
| campaignId | Integer | Filter merchants for a specific campaign. | ✓ |  |
| retailerId | Integer | The retailer id. | ✓ | ✓ |
| id | Integer | The id of the merchant. |  | ✓ |
| name | String | The name of the merchant. |  | ✓ |
| provinceDesc | String | Description of the merchant province. |  | ✓ |
| provinceId | Integer | The id of the merchant province. |  | ✓ |
| stateId | String | Current merchant state: **A (Active), D (Deactivated)**. | ✓ |  |
| retailerId | Integer | The retailer id. |  | ✓ |
| retailerName | String | The retailer name. |  | ✓ |
| longitude | Double | Longitude position. Must be used with latitude. |  | ✓ |
| latitude | Double | Latitude position. Must be used with longitude. |  | ✓ |
| distance | Double | Distance to the closest merchant. |  | ✓ |


# Example request

<details class="collapsible">
  <summary>GET/merchants</summary>
  <div class="details-body">

```curl curl "https://za-vsp-int.wigroup.co/cvs-issuer/rest/merchants" \
  -H "apiId: {apiId}" \
  -H "apiPassword: {apiPassword}" \
  -H "Content-Type: application/json" \
  -X GET
```

# Example response

```json {
  merchants: [ {
    “createDate“: "2013-03-19T07:34:02+0200",
    “id“: 1050,
    “name“: "Test Merchant",
    “provinceDesc“: "Eastern Cape",
    “provinceId“: 1,
    “stateId“: "A",
    “retailerId“: 10,
    “retailerName“: "Yoyo Retailer",
    “latitude“: -33.932308,
    “longitude“: 18.467464,
    “distance“: 0
      }
  }],
  “hasNextPage“: false,
  “numOfPages“: 1,
  “numItemsOnPage“: 2,
  “numItemsInTotal“: 2,
  “order“: {},
  “pageSize“: 20,
  “pageOffset“: 0,
  “responseCode“: "-1",
  “responseDesc“: "Success"
}
```
  </div>
</details>

### Redemption callback
The redemption callback URL provides the YoyoPlatform with a means of notifying third parties that a redemption has occurred. Whenever a transaction containing a redemption request is successfully processed by the wiCode platform, a redemption callback will be POSTed to the configured callback URL.

## Redemption Callback failure
To facilitate synchronization, the YoyoPlatform has a built-in finite redemption callback retry policy. This is enables better state synchronization by minimizing the number of redemption callback failures. If, for some reason, no response is received across all redemption callback requests, a redemption fallback policy must be followed.

**Yoyo's CVS will retry the callback 10 times.**

## Webhook URL

Each redemption notification will include three key parameters: itemId, processedAmount, and totalProcessed. The itemId corresponds to the ID of the coupon, or giftcard that was redeemed. The processedAmount corresponds to the monetary value of the redemption, i.e. the value of the coupon or the value of the giftcard that was spent. totalProcessedAmount is the sum of the processed amount across all redemptions in the transaction.

The redemption callback should be built using RESTful JSON programming language.

**This is a POST message from CVS to the webhook URL, not an API that is consumed.*


### Redemption Webhook Callback

The **webhook URL must be secured using a 3rd-party signed SSL certificate**.

This webhook is triggered to notify the channel when a **redemption transaction occurs**.

---

### Parameters

| Parameter | Type | Description |
|---|---|---|
| id | Integer | Unique identifier of the redemption callback. |
| userRef | String | Unique identifier of the user (against whom the Gift Card is issued). |
| basketId | String | Unique identifier of the basket. |
| storeId | Integer | Unique identifier of a store. Provided once-off by Yoyo. |
| totalProcessedAmount | Integer | Sum of the `processedAmount` across each redemption. |
| createDate | String | The date on which the transaction was created. |
| wiCode | String | Number of digits for the desired wiCode. |
| wiTrxId | String | Unique transaction identifier for the wiCode platform. |
| interfaceIssuerId | String | The channel that issued the wiCode for redemption. |


### Redemption Object

| Parameter | Type | Description |
|---|---|---|
| id | Integer | Unique identifier of the redemption. |
| processedAmount | Integer | Value of the redemption. |
| createDate | String | The date of redemption of the item. |
| type | String | Type of item redeemed: **COUPON**, **VOUCHER**, or **GIFTCARD**. |
| itemId | Integer | Unique identifier of the item (e.g., Gift Card ID). |
| campaignId | Integer | Campaign against which the item was issued. |
