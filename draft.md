# Skygard Specification

**Working Draft**

## Introduction

Skygard has been designed from the ground up to provide users with a suite of cloud based tools what are centered around privacy and security, without sacrificing quality. All of our products are built around end-to-end encryption, meaning you, and only you has the key to access your data.

We implement industry-standard encryption technology in a way that is seamless to the user. We build security, privacy and encryption into the foundation of our products. 

## Registration

When you register for an account, we ask for minimal information, a name, an email address and a password.

When submitting the registration form, your browser will generate a hash of your password using 5000 rounds of PBKDF2-SHA256 and your email address as the salt. This hash becomes your authentication token which will be used to authenticate you when logging in, without requiring your password to be sent to the server.

Once your authentication token has been generated, the following information is then sent to the server for registration.

* Your name
* Your email address
* Your authentication token.

If registration is successful we hash your authentication token again server-side using bcrypt, the server will return a user id. Your browser will then generate an X.509 certificate signing request (CSR) and private key using your user id an the common name (CN). Your password is then hashed in your browser again using 5000 rounds of PBKDF2-SHA256 and your user id as the salt. The CSR and encrypted private key is then sent back to the server, which will then return a the following data if your user id matches.

* Your signed certificate which should be stored on-device
* The servers public key which should be stored on-device for signature verification.

The data that is stored on the server is as follows:

* Your name
* Your email address
* Your signed certificate
* Your encrypted private key
* Your hashed authentication token.

Your password is only ever sent to the server in hashed form, and as hashing is a one-way algorithm, it is impossible for us to reverse the hash to view your password.

## Logging In

To login to your account, we require your email address and password to authenticate you. Before any information is sent to the server, your password is hashed with 5000 rounds of PBKDF2-SHA256 using your email address as the salt to generate your authentication token. Your email address and authentication token is then sent to the server and verified against the server-side bcrypt hash of your authentication token.
Once your authentication has been verified with our servers, your encrypted private key is then sent back to your browser which can then be decrypted with your encryption key which is generated in your browser by hashing your password with 5000 rounds of PBKDF2-SHA256 using your user id as the salt.

## Changing your password or email address

Should you need to change your password, you can do this by authenticating in the usual way. You will then be given the option to change your password in your account settings.
Your new password is hashed using the same algorithm used during registration and login, this will re-generate your authentication token and encryption key. Your private key is then re-encrypted in your browser and sent to the server along with your new authentication token as well as your old authentication token for verification. Before the server will accept your new password, your old password must be re-veritifed using the same process as logging in, once your old password is verified, your authentication token	 is then re-hashed server-side using bcrypt and stored along with your encrypted private key.

## Forgot your password

Your password is the key to all of your data that only you know. You must make sure that you keep this password safe because without it you will no longer be able to access your data. If you forget your password, then there will be no way to recover your encrypted data.
