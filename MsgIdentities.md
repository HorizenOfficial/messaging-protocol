## 3. ZEN Messaging User Identities

### Basics

A user may have multiple ZEN messaging identities that he presents to other users (as public information). The messaging client/wallet/GUI is responsible for maintaining these identities. A messaging identity needs to have the following fields:
  * `nickname`: (mandatory) – arbitrary, user specified.
  * `sendreceiverddress`: (mandatory) – a Z address specifically created or designated for the messaging identity. It is used to send and receive messaging transactions.
  * `senderidaddress`: (mandatory) – a T address used to sign messages before sending (specific to the messaging identity)
  * `firstname`: (optional)
  * `middlename`: (optional)
  * `surname`: (optional)
  * `email`: (optional)
  * `streetaddress`: (optional)
  * `facebook`: (optional)
  * `twitter`: (optional)

### Details

All text fields are to have a maximum length of 128 chars. When a ZEN messaging identity is shared between users/applications it is to be standardized in a JSON format:
```
{
   "zenmessagingidentity":
   { 
	"nickname": "bob",
	"sendreceiveaddress": "zcZLZ6hjYYCqZEg64aHpvCpaX7yQyvAedjhHkP2e1LWVSNmxhj6CUYJqAsPGXzxB5prMppyv2jsJxbGbw4JDvdxpPUbNNUa",
	"senderidaddress": "znn9wRVAkgNnKCYJvsHu9nm4Q7c6AxLE2qR",
	"firstname": "Robert",
	… etc. all fields
   }
}
```
When encoded as bytes/blob (e.g for transmission over some byte-oriented protocol) the identity object is to be in UTF-8 encoding.

### Sharing identities

The way users share their identities to establish contact is not strictly defined. The implementation of this could also be done in different ways:
  * As an initial implementation GUI wallets/messengers could allow users to import/export/copy/paste their ZEN identity or a contact’s identity as a JSON object. The ZEN identities will be shared by users either privately (via other channels) or as a convenience on a dedicated ZEN slack channel.
  * Once user A imports user B’s identity, the respective GUI client could ask user A if he wishes to send his identity to user B as a [ZEN special message](Protocol_v1.md#zen-special-messages). This is possible only if the message can fit in one Z→Z transaction (for ZEN messaging protocol version 1). Then user B’s GUI client will prompt him to import user A’s identity from the message. 
  * Later a new (unfortunately centralized) “ZEN messaging server” could be implemented where users may publish their ZEN identities. The messaging GUI clients will access the server via some standardized interface (preferably a REST-ful Web service that accepts/serves JSON). Users can publish or search identities on the server.
  * The desired long-term solution is a distributed registry of ZEN messaging identities on the ZEN blockchain!
  
