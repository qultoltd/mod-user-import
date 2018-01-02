# mod-user-import

Copyright (C) 2017 The Open Library Foundation

This software is distributed under the terms of the Apache License,
Version 2.0. See the file "[LICENSE](LICENSE)" for more information.

This module is responsible for importing new or already existing users into FOLIO.

Currently the module contains one endpoint:
POST /user-import

## How to use

1. Login with a user who has permission for importing users (permission name: <code>User import</code>, permission code: <code>user-import.all</code>). This can be done by sending the following request to FOLIO:
<pre>URL: <code>{okapiUrl}/authn/login</code>
Headers:
<code>
  x-okapi-tenant: {tenantName}
  Content-Type: application/json
</code>
Body:
<code>
  {
    "username": "username",
    "password": "password"
  }
</code></pre>

2. The login request will return a header in the response which have to be used for authentication in the following request. The authentication token is returned in the <code>x-okapi-token</code> header (use as <code>okapiToken</code>). The user import request can be sent in the following format:
<pre>URL: <code>{okapiUrl}/user-import</code>
Headers:
<code>
  x-okapi-tenant: {tenantName}
  Content-Type: application/json
  x-okapi-token: {okapiToken}
</code>
Body:
<code>{exampleImport}</code>
</pre>

The default <code>okapiUrl</code> is <code>http://localhost:9130</code>. The default <code>tenantName</code> is <code>diku</code>. An <code>exampleImport</code> can be found in the next section.

## Example import request

<pre><code>
{
  "users": [{
    "username": "somebody012",
    "externalSystemId": "somebody012",
    "barcode": "1657463",
    "active": true,
    "patronGroup": "faculty",
    "personal": {
      "lastName": "Somebody",
      "firstName": "Test",
      "middleName": "",
      "email": "test@email.com",
      "phone": "+36 55 230 348",
      "mobilePhone": "+36 55 379 130",
      "dateOfBirth": "1995-10-10",
      "addresses": [{
        "countryId": "HU",
        "addressLine1": "Andrássy Street 1.",
        "addressLine2": "",
        "city": "Budapest",
        "region": "Pest",
        "postalCode": "1061",
        "addressTypeId": "Home",
        "primaryAddress": true
      }],
      "preferredContactTypeId": "mail"
    },
    "enrollmentDate": "2017-01-01",
    "expirationDate": "2019-01-01"
  }],
  "totalRecords": 1,
  "deactivateMissingUsers": false,
  "updateOnlyPresentFields": false,
  "sourceType": "test"
}
</code></pre>

### patronGroup
The value can be the name of an existing patron group in the system. E.g. faculty, staff, undergrad, graduate. The import module will match the patron group names for the patron group ids.

### addressTypeId
The value can be the name of an existing address type in the system. E.g. Home, Claim, Order. The import module will match the address type names for the address type ids. It is important to note that two addresses for a user cannot have the same address type.

### preferredContactTypeId
The value can be one of the following: mail, email, text, phone, mobile.

### deactivateMissingUsers
This should be true if the users missing from the current import batch should be deactivated in FOLIO.

### updateOnlyPresentFields
This should be true if only the fields present in the import should be updated. E.g. if a user address was added in FOLIO but that type of address is not present in the imported data then the address will be preserved.

### sourceType
A prefix for the externalSystemId to be stored in the system. This field is useful for those organizations that has multiple sources of users. With this field the multiple sources can be separated. The source type is appended to the beginning of the externalSystemId with an underscore. E.g. if the user's externalSystemId is somebody012 and the sourceType is test, the user's externalSystemId will be test_somebody012.
