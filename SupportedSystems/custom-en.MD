# Connect a custom newsletter system

# Introduction

In order to transfer leads/newsletter registrations from customers to your system in an automated and timely manner, Sovendus can call an interface provided by you. The data is transmitted via the URL parameters of an HTTP GET request (also called a "webhook") or an HTTP POST request. The customer data provided can then be further processed by your system as desired.

## Example GET request

An URL you provide to transfer the leads/newsletter sign-ups could look like this:

`https://yourdomain.com/subscribe?salutation=%consumer_salutation%&gender=%consumer_gender%&firstname=%consumer_firstname%&lastname=%consumer_lastname%&email=%consumer_email%&sov_id=%newsletter_history_id%&complianceip=%compliance_ip%&compliancetext=%compliance_text%&compliancetimestamp=%compliance_timestamp%`

> [!ERROR]
> IMPORTANT: To ensure a secure connection, the HTTPS protocol (with a valid certificate) must be used to transfer the data!

### Transmission parameters

> [!WARNING]
> Note that parameters can have different names e.g. firstname could be first_name or any other name.

The URL you provide should contain the following placeholders to allow complete transfer of the customer data:

| Placeholder              | Value                                                                           |
| ------------------------ | ------------------------------------------------------------------------------- |
| %consumer_salutation%    | Salutation of the customer                                                      |
| %consumer_gender%        | Gender of the customer                                                          |
| %consumer_firstname%     | First name of the customer                                                      |
| %consumer_lastname%      | Last name of the customer                                                       |
| %consumer_email%         | E-mail of the customer                                                          |
| %newsletter_history_id%  | Sovendus ID of the record                                                       |
| %compliance_ip% Customer | IP at the time of opt-in consent                                                |
| %compliance_text%        | Opt-in consent text at the time of confirmation - is transmitted base64 encoded |
| %compliance_timestamp%   | Time of confirmation of the opt-in consent text - is transmitted base64 encoded |

To ensure your data protection interests, the following transmission parameters %newsletter_history_id%, %compliance_ip%, %compliance_text% and %compliance_timestamp% must be processed in your system.

> [!ERROR]
> Attention: The %compliance_text% and the %compliance_timestamp% are base64 encoded to avoid transmission errors!

### Example request from our system

An example request from our system could look like this with the following registration data:

#### Registration data:

- First name: Mustermann
- Last name: Müller
- E-mail: mustermann.mueller@mail.de
- Sovendus ID: 21544842251
- IP of consent: 127.0.0.1
- Consent text: Ich willige hiermit ein, dass mich WERBEPARTNER über Neuigkeiten und Angebote (zu [z.B. Damenmode]) per E-Mail informieren darf. Meine Einwilligung kann ich jederzeit bei WERBEPARTNER widerrufen
- Consent timestamp: 01.03.2018 11:58

#### Example URL:

`https://yourdomain.com/subscribe?salutation=Herr&firstname=Mustermann&lastname=M%C3%BCller&email=mustermann.mueller%40mail.de&sov_id=21544842251&complianceip=127.0.0.1&compliancetext=SWNoIHdpbGxpZ2UgaGllcm1pdCBlaW4sIGRhc3MgbWljaCBXRVJCRVBBUlRORVIgw7xiZXIgTmV1aWdrZWl0ZW4gdW5kIEFuZ2Vib3RlICh6dSBbei5CLiBEYW1lbm1vZGVdKSBwZXIgRS1NYWlsIGluZm9ybWllcmVuIGRhcmYuIE1laW5lIEVpbndpbGxpZ3VuZyBrYW5uIGljaCBqZWRlcnplaXQgYmVpIFdFUkJFUEFSVE5FUiB3aWRlcnJ1ZmVu&compliancetimestamp=MDEuMDMuMjAxOCAxMTo1OA%3D%3D`

All transmitted parameters are URL encoded and the character set used is UTF-8.

### Authentication

To prevent unauthorized access to your interface, access to the URL should be secured via HTTP authentication (Basic Authentication).

#### Example URL with authentication:

`https://username:password@yourdomain.com/subscribe?...`

> [!ERROR]
> IMPORTANT: Username and password for authentication should be submitted to Sovendus in a secure way.

### Return values

For Sovendus to determine successful transmission, your interface should return as precise an HTTP status code as possible in the range 200-299.

For unsuccessful transmission, your interface should return as precise an HTTP status code as possible from the error ranges 400-499 or 500-599.

In case of errors, Sovendus will attempt to submit requests to the interface up to 10 times, waiting 10 minutes between each request. Nevertheless, you should ensure that the interface is permanently available.

## Example POST request

A POST request you provide to transfer the leads/newsletter sign-ups could look like this:

#### Request URL:

https://signup.api.com/newsletter_signup

#### Headers:

| Key                 | Value                |
| ------------------- | -------------------- |
| Content-Type        | application/json     |
| Custom_header       | custom_value         |
| api_key_header_name | YOUR_API_PRIVATE_KEY |

#### Body:

```json
{
  "salutation": "%consumer_salutation%",
  "gender": "%consumer_gender%",
  "firstname": "%consumer_firstname%",
  "lastname": "%consumer_lastname%",
  "email": "%consumer_email%",
  "sov_id": "%newsletter_history_id%",
  "compliance_ip": "%compliance_ip%",
  "compliance_text": "%compliance_text%",
  "compliance_timestamp": "%compliance_timestamp%"
}
```

> [!ERROR]
> IMPORTANT: To ensure a secure connection, the HTTPS protocol (with a valid certificate) must be used to transfer the data!

### Transmission parameters

> [!WARNING]
> Note that parameters can have different names e.g., firstname could be first_name or any other name.

The example request you provide should contain the following placeholders, to allow complete transfer of the customer data:

| Placeholder              | Value                                           |
| ------------------------ | ----------------------------------------------- |
| %consumer_salutation%    | Salutation of the customer                      |
| %consumer_gender%        | Gender of the customer                          |
| %consumer_firstname%     | First name of the customer                      |
| %consumer_lastname%      | Last name of the customer                       |
| %consumer_email%         | E-mail of the customer                          |
| %newsletter_history_id%  | Sovendus ID of the record                       |
| %compliance_ip% Customer | IP at the time of opt-in consent                |
| %compliance_text%        | Opt-in consent text at the time of confirmation |
| %compliance_timestamp%   | Time of confirmation of the opt-in consent text |

To ensure your data protection interests, the following transmission parameters %newsletter_history_id%, %compliance_ip%, %compliance_text% and %compliance_timestamp% must be processed in your system.

An example request from our system could look like this with the following registration data:

#### Registration data:

- First name: Mustermann
- Last name: Müller
- E-mail: mustermann.mueller@mail.de
- Sovendus ID: 21544842251
- IP of consent: 127.0.0.1
- Consent text: Ich willige hiermit ein, dass mich WERBEPARTNER über Neuigkeiten und Angebote (zu [z.B. Damenmode]) per E-Mail informieren darf. Meine Einwilligung kann ich jederzeit bei WERBEPARTNER widerrufen
- Consent timestamp: 01.03.2018 11:58

#### Example POST request:

##### Request URL:

https://signup.api.com/newsletter_signup

##### Headers:

| Key                 | Value                |
| ------------------- | -------------------- |
| Content-Type        | application/json     |
| Custom_header       | custom_value         |
| api_key_header_name | YOUR_API_PRIVATE_KEY |

##### Body:

```json
{
  "salutation": "Herr",
  "gender": "MALE",
  "firstname": "Mustermann",
  "lastname": "Müller",
  "email": "mustermann.mueller@mail.de",
  "sov_id": "21544842251",
  "compliance_ip": "127.0.0.1",
  "compliance_text": "Ich willige hiermit ein, dass mich WERBEPARTNER über Neuigkeiten und Angebote (zu [z.B. Damenmode]) per E-Mail informieren darf. Meine Einwilligung kann ich jederzeit bei WERBEPARTNER widerrufen",
  "compliance_timestamp": "2023-09-01T11:58:27+02:00"
}
```

The character set used is UTF-8.

### Authentication

To prevent unauthorized access to your interface, access to the URL should be secured via HTTP authentication (Basic Authentication).

#### Example URL with authentication:

`https://username:password@yourdomain.com/subscribe`

#### Authentication Token in the Header or Body:

To prevent unauthorized access to your interface, we support authentication token either in the header or in the body.

##### Example header with authentication:

| Key                 | Value                |
| ------------------- | -------------------- |
| Content-Type        | "application/json    |
| api_key_header_name | YOUR_API_PRIVATE_KEY |

> [!ERROR]
> IMPORTANT: Username and password for authentication should be submitted to Sovendus in a secure way.

### Return values

For Sovendus to determine successful transmission, your interface should return as precise an HTTP status code as possible in the range 200-299.

For unsuccessful transmission, your interface should return as precise an HTTP status code as possible from the error ranges 400-499 or 500-599.

In case of errors, Sovendus will attempt to submit requests to the interface up to 10 times, waiting 10 minutes between each request. Nevertheless, you should ensure that the interface is permanently available.

## Data protection

In any communication with Sovendus, the identification of the data record must take place by means of the ID provided by Sovendus and must not take place by means of personal data. Therefore, this characteristic should be stored for each data record.

## Example implementation

For your help, we provide you a sample HTTP GET endpoint script in PHP. This script takes the data, decodes the values, and writes them into a JSON file.
You are free to use, modify or extend this sample code. Of course, you can also use your own implementation.
