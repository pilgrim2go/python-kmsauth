# python-kmsauth

A python library for KMS authentication and authorization

## Usage

kmsauth can generate authentication tokens and validate authentication tokens.
kmsauth current supports tokens in v1 or v2 format. By default, when generating
tokens, it will generate tokens in v2 format. The difference between the
formats is the encryption context and the username format.

Decrypting tokens requires the username and the token, so when passing this to
a service, you should pass both along.

### Token formats

v1:
* username: 'my-service-name'
* encryption context: {"to":"their-service-name","from":"my-service-name"}

v2:
* username: '2/service/my-service-name'
* encryption context: {"to":"their-service-name","from":"my-service-name","user\_type":"service"}

### Generating tokens

```python
import kmsauth
# user to service authentication
generator = kmsauth.KMSTokenGenerator(
    # KMS key to use for authentication
    'alias/authnz-production',
    # Encryption context to use
    {
        # We're authenticating to this service
        'to':'confidant-production',
        # It's from this user
        'from':'rlane',
        # This token is for a user
        'user_type': 'user'
    },
    # Find the KMS key in this region
    'us-east-1'
)
username = generator.get_username()
token = generator.get_token()

# service to service authentication
generator = kmsauth.KMSTokenGenerator(
    # KMS key to use for authentication
    'alias/authnz-production',
    # Encryption context to use
    {
        # We're authenticating to this service
        'to':'confidant-production',
        # It's from this service
        'from':'example-production',
        # This token is for a service
        'user_type': 'service'
    },
    # Find the KMS key in this region
    'us-east-1'
)
username = generator.get_username()
token = generator.get_token()
```

### Validating tokens

```python
import kmsauth
validator = kmsauth.KMSTokenValidator(
    # KMS key to use for service authentication
    'alias/authnz-production',
    # KMS key to use for user authentication
    'alias/authnz-users-production',
    # The context of this validation (the "to" context to validate against)
    'confidant-production',
    # Find the KMS keys in this region
    'us-east-1'
)
validator.decrypt_token(username, token)
```

## Reporting security vulnerabilities

If you've found a vulnerability or a potential vulnerability in kmsauth
please let us know at security@lyft.com. We'll send a confirmation email to
acknowledge your report, and we'll send an additional email when we've
identified the issue positively or negatively.

## Getting support or asking questions

kmsauth is a component of Confidant, so discussion for it is through the same
channels as Confidant. We have a mailing list for discussion, and a low volume
list for announcements:

* https://groups.google.com/forum/#!forum/confidant-users
* https://groups.google.com/forum/#!forum/confidant-announce

We also have an IRC channel on freenode and a Gitter channel:

* [#confidant](http://webchat.freenode.net/?channels=confidant)
* [lyft/confidant on Gitter](https://gitter.im/lyft/confidant)

Feel free to drop into either Gitter or the IRC channel for any reason, even
if just to chat. It doesn't matter which one you join, the messages are sync'd
between the two.
