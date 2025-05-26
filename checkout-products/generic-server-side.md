# Sovendus Checkout Products Postback - Server to Server

## 1. Introduction

Sovendus displays your product following some transaction process (e.g. order checkout) at its numerous partners. To be able to register your orders properly and assign them correctly to our respective advertising partners, Sovendus offers the possibility to transmit your generated orders via API call.

To achieve this, Sovendus transfers a generic token with any request of your landing page URL. This token is the base of order registration at Sovendus. It is generated dynamically and therefore changes with every request. This token has to be picked out of the URL of your landing page, buffered und returned to Sovendus via a simple API call after completion of the transaction.

> [!INFO]
> Prerequisite for a successful cooperation is the return transfer of the token.
> Without a proper back transfer, the Sovendus algorithm is not able to handle your product as intended. As a
> result, your product won’t be displayed on our advertising partners‘ pages any more, just few days after.

## 2. Login credentials



> [!WARNING]
> Please keep your login credentials confidential (Product-ID and API key). Your credentials will be sent to you by your contact at Sovendus.

## 3. Workflow

![Workflow-image](https://raw.githubusercontent.com/Sovendus-GmbH/Generic-Sovendus-Checkout-Products-Postback-Integration-Documentation/main/workflowimg.png)

## 4. External Product-ID

Every campaign at Sovendus receives its own fixed alphanumerical Product-ID. This Product-ID has to be added to the API call transfer. The Product-ID is permanently assigned to your product.

You receive this Product-ID from your contact at Sovendus or you can learn it from paragraph 2 of this documentation.

## 5. Token

With every click on your product teaser image an ampersand (&) and the parameter **sovReqToken** will be added to the URL of your landing page by default. The alphanumerical value of the sovReqToken parameter is dynamic and changes with every new click on your product. The token has to be picked out of the URL by you, buffered and transferred back to Sovendus via API call after completion of the transaction.

### Example

https://www.website.com/landingpage?channel=sovendus&sovReqToken=XXXXX-XXXXX-XXX-XXXXX

URL of your landingpage: https://www.website.com/landingpage?channel=sovendus \
Token: sovReqToken=XXXXX-XXXXX-XXX-XXXXX

> [!INFO]
> The parameter **sovReqToken** can be renamed by request. Please inform Sovendus about this.

### Syntax of token value

- The test token should have the structure of a regular token (aaaaa-aaaa-aaa-aaaaa)
- The test token should contain a hint that this is actual a test token (e.g., aaaaa-test-aaa-aaaaa)
- The test token should contain the "code" "test" as a second part, e.g., this should be accepted test tokens - 11111-test-111-11111 - fffff-test-fff-fffff - 12345-test-123-12345 - 1a2b3-test-1a2-1a2b3  

If the test token and all other parameters are valid, the API will return the regular 200 response.

## 6. API call

> [!INFO]
> Please use the API call **exclusively** for server-to-server connections.  
> On no account can there be any requests from browsers of end customers.

The URL of the API call is composed as follows:

https://press-order-api.sovendus.com/ext/{externalProductID}/{token}/api

**externalProductID**: Enter external Product-ID of corresponding product campaign (paragraph 4) \
**token**: Enter token transferred by Sovendus (paragraph 5)

The HTTP procedure of the interface request has to be POST.

To authenticate the API call, an alphanumerical API key is required additionally to the parameters within the URL. This key is transferred within the request body of the request. You receive this API key directly from your contact at Sovendus or you can learn it from paragraph 2 of this documentation.

> [!EXAMPLE]
> Content-Type: application/json  
> {  
> "apiKey":"XXXX-XXXXX-XXXXXX-XXX"  
> }

## 7. Response

| HTTP – Status             | Description                                                                                                                                                                                                                                                                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200 OK                    | To successful requests the API responds with the external Product-ID and the token from the URL: <br> <p> Content-Type: application/json <br> { <br> "externalProductId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX" <br> "sovReqToken": "XXXXXX-XXXXX-XXXX-XXXXXXX" <br> }                                                             |
| 403 Forbidden             | Incorrect or missing API keys lead to an error at authentication: <br> <p> Content-Type: application/json <br> { <br> "message": "This API call is unauthorized. Please contact your account manager." <br> }                                                                                                                      |
| 404 Not Found             | Incorrect URL, routing endpoint could not be found: <br> <p> Content-Type: application/json <br> { <br> "message": "Invalid API call." <br> }                                                                                                                                                                                      |
| 405 Method Not Allowed    | Incorrect HTTP procedure: <br> <p> Content-Type: application/json <br> { <br> "message": "Invalid API call." <br> }                                                                                                                                                                                                                |
| 422 Unprocessable Entity  | URL parameters could not be found: <br> <p> Content-Type: application/json <br> { <br> "message": "Invalid API parameters." <br> } <br> <p> Content type: application/json <br> { <br> "message": "External product id not found." <br> } <br> <p> Content type: application/json <br> { <br> "message": "Token not found." <br> } |
| 500 Internal Server Error | Unexpected server error. The request should be repeated with some delay.                                                                                                                                                                                                                                                           |
| No Response               | Possible timeouts. The request should be repeated with some delay.                                                                                                                                                                                                                                                                 |

## 8. Contact

For any question regarding the technical implementation of the Sovendus API for products with external order routes please contact Sovendus via e-mail oder phone.
