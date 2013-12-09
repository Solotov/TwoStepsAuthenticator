TwoStepsAuthenticator
=====================

.net implementation of the TOTP: Time-Based One-Time Password Algorithm and HOTP: HMAC-Based One-Time Password Algorithm<br/>
RFC 6238 http://tools.ietf.org/html/rfc6238<br>
RFC 4226 http://tools.ietf.org/html/rfc4226

Compatible with Microsoft Authenticator for Windows Phone, and Google Authenticator for Android and iPhone.

You can use this library as well for a client application (if you want to create your own authenticator) or for a server application (add two-step authentication on your asp.net website)

# TOTP

## Client usage

For a client application, you need to save the secret key for your user. <br/>
Then, you only have to call the method GetCode(string) :

<pre><code>
var secret = user.secretAuthToken;
var authenticator = new TwoStepsAuthenticator.TimeAuthenticator();
var code = authenticator.GetCode(secret);
</code></pre>

## Server usage

On a server application, you will have to generate a secret key, and share it with the user, who will have to enter it in his own authenticator app.

<pre><code>
var key = TwoStepsAuthenticator.Authenticator.GenerateKey();
</code></pre>

When the user will login, he will have to give you the code generated by his authenticator.<br/>
You can check if the code is correct with the method CheckCode(string secret, string code).<br/>
If the code is incorrect, don't log him.

<pre><code>
var secret = user.secretAuthToken;
var code = Request.Form["code"];
var authenticator = new TwoStepsAuthenticator.TimeAuthenticator();
bool isok = authenticator.CheckCode(secret, code);
</code></pre>

### Used codes manager

Every code should only be used once. To prevent repeated use of a code a IUsedCodesManager interface is provided.<br>

A default implementation is provided : used codes are kept in memory for 5 minutes (long enough for codes to become invalid)

You can define how the used codes are stored, for example if you want to handle persistence (database storage), or if you have multiple webservers.<br/>
You have to implement the 2 methods of the IUsedCodesManager :
<pre><code>
void AddCode(ulong challenge, string code);
bool IsCodeUsed(ulong challenge, string code);
</code></pre>

When you create a new Authenticator, add the instance of your IUsedCodesManager as the first param
<pre><code>
var usedCodeManager = new CustomUsedCodeManager();
var authenticator = new TwoStepsAuthenticator.TimeAuthenticator(usedCodeManager);
</code></pre>
