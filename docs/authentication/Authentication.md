# Authentication

The Newton Pro API uses the HMAC-SHA256 signing method for REST requests. A signature must be included in every request to a private endpoint, but is not required for public endpoints. 

This documentation explains how to generate the proper HTTP headers, including example code in several programming languages:

### ClientID:Signature

**Client ID**: Is your unique client identifier. You can find this under the API Access settings in the [Newton web app](https://web.newton.co).

**Signature**:
  1) Concatenate the following using the character ":" as a delimiter:
    * HTTP method (GET, POST, etc.)
    * Content Type (application/json) (empty if GET request)
    * Request URI (/api/...) without the query string parameters
    * SHA256 Data hash of the body encoded in HEX (empty if no body)
    * Timestamp (unix epoch)

      -> Will be used to validate the NewtonDate HTTP header.

      -> Any request older than 5 minutes will be ignored.

  2) Calculate the SHA256 hash of the concatenated string using your Client Secret Key found in the API Access settings in the [Newton web app](https://web.newton.co). 
  3) Encode the hash in Base64

#### NewtonDate:

Must be the same date as the one hashed in the signature. Format is unix epoch and it *must to be floored*.

#### Code Samples for Signature Generation

<!--
type: tab
title: Java
-->

<!--
lineNumbers: true
-->
```java
final String ALGORITHM_NAME = "HmacSHA256";
final String ENCODING_NAME = "UTF-8";

final String CLIENT_ID = "Your Client ID";
final String CLIENT_SECRET = "Your Client Secret";

String currentTime = Long.toString(System.currentTimeMillis() / 1000L);

/*
  If the request has a body, you would use this instead of empty string below (replace BODY with actual request body):

  MessageDigest digest = MessageDigest.getInstance("SHA-256");
  byte[] encodedHash = digest.digest("BODY".getBytes(StandardCharsets.UTF_8));
  String hashed_body = Hex.encodeHexString(encodedHash);
*/

String[] signatureParameters = {
    "GET",
    "",
    "/api/PATH",
    "",  //If the request has a BODY, this would be hashed_body
    currentTime
};

String signatureData = String.join(":", signatureParameters);

final Mac sha256_HMAC = Mac.getInstance(ALGORITHM_NAME);
final SecretKeySpec secret_key = new SecretKeySpec(CLIENT_SECRET.getBytes(ENCODING_NAME), ALGORITHM_NAME);
sha256_HMAC.init(secret_key);

byte[] computedSignature = sha256_HMAC.doFinal(signatureData.getBytes(ENCODING_NAME));

String NewtonAPIAuth = CLIENT_ID + ":" + Base64.getEncoder().encodeToString(computedSignature);
String NewtonDate = currentTime;
```

<!--
type: tab
title: Python
-->

<!--
lineNumbers: true
-->
```python
import hmac
from datetime import datetime
from base64 import b64encode
from math import floor
from hashlib import sha256

ENCODING = "utf-8"

CLIENT_ID = "Your Client ID"
CLIENT_SECRET = "Your Client Secret"

current_time = str(floor(datetime.now().timestamp()))

#If the request has a body, you would use this instead of empty string below (replace BODY with actual request body):
#hashed_body = sha256(BODY).hexdigest()

signature_parameters = [
    "GET",
    "",
    "/api/PATH",
    "",  #If the request has a body, this would be hashed_body
    current_time
]

signature_data = ":".join(signature_parameters).encode(ENCODING)

computed_signature = hmac.new(
    CLIENT_SECRET.encode(ENCODING),
    msg=signature_data,
    digestmod=sha256
).digest()

NewtonAPIAuth = CLIENT_ID + ":" + b64encode(computed_signature).decode()
NewtonDate = current_time
```

<!--
type: tab
title: NodeJS
-->

<!--
lineNumbers: true
-->
```javascript
const crypto = require('crypto');

const CLIENT_ID = "Your Client ID";
const CLIENT_SECRET = "Your Client Secret";

const currentTime = Math.round((new Date()).getTime() / 1000);

//If the request has a body, you would use this instead of empty string below (replace BODY with actual request body):
//const hashed_body = crypto.createHash('sha256').update(BODY).digest('hex');

const signatureParameters = [
    "GET",
    "",
    "/api/PATH",
    "", //If the request has a body, it would be hashed_body
    currentTime
];

const signatureData = signatureParameters.join(":");

const computedSignature = crypto.createHmac('sha256', CLIENT_SECRET)
                                 .update(signatureData)
                                 .digest("base64");

const NewtonAPIAuth = `${CLIENT_ID}:${computedSignature}`;
const NewtonDate = currentTime;
```

<!-- type: tab-end -->

#### Possible Errors Related to Authentication

<!--
type: tab
title: Generic Authentication Error
-->

```json
{
    "detail": "Invalid authorization."
}
```

<!--
type: tab
title: Missing Authentication Headers
-->

```json
{
    "detail": "Authentication credentials were not provided."
}
```

<!-- type: tab-end -->
