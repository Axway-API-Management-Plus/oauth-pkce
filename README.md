# OAuth PKCE


API-Gateway OAuth-PKCE-Client


### Create Code-Challenge

```js
var imp = new JavaImporter(com.vordel.trace, java.security, java.util);
with(imp) {
    function invoke(msg) {
        var secureRandom = new SecureRandom();
        var codeVerifier = java.lang.reflect.Array.newInstance(java.lang.Byte.TYPE, 32);
        secureRandom.nextBytes(codeVerifier);
        codeVerifier = Base64.getUrlEncoder().withoutPadding().encodeToString(codeVerifier)
        Trace.info("Code verifier: " + codeVerifier);
        msg.put("code_verifier_key", codeVerifier);

        var messageDigest = MessageDigest.getInstance("SHA-256");
        var bytes = codeVerifier.getBytes("US-ASCII");
        messageDigest.update(bytes, 0, bytes.length);

        var codeChallenge = messageDigest.digest();
        codeChallenge = Base64.getUrlEncoder().withoutPadding().encodeToString(codeChallenge);
        Trace.info("Code Challenge: " + codeChallenge);
        msg.put("code_challenge", codeChallenge);
        msg.put("code_challenge_method", "S256");
        return true;
    }
};
```

### Get OAuth-State

```js
var imp = new JavaImporter(com.vordel.trace, java.net);
with(imp) {
    function invoke(msg) {
        var locationHeader = msg.get("http.headers").getHeader("Location");

        Trace.info("Location header: " + locationHeader);
        var url = new URL(locationHeader);
        var queryParams = url.getQuery();
        var params = queryParams.split("&");

	for(var i = 0; i < params.length; i++) {
		var param = params[i];
                var paramName = param.split("=")[0];
		if(paramName == "state") {
	                var paramValue = param.split("=")[1];
			msg.put("state", paramValue);
			break;
		}
	}
        return true;
    }
};
```
