## ZEN Messaging Protocol - version 1

### Basics

ZEN user messages are to be transported as a part of T→Z and Z→Z ZEN transactions in the memo field. The memo filed has a maximum length of 512 bytes and when specified on a command line needs to be in HEX format. There are currently some limitations in JoinSplits/→Z transactions that complicate the design of the protocol somewhat:
  * When Z address receives an incoming transaction the recipient sees the date/amount/memo but does not see the sending T/Z address. This seems to be the case for both T→Z and Z→Z ZEN transactions.
  * When a transaction is sent from (also sometimes to – [bug #2](https://github.com/ZencashOfficial/zencash-swing-wallet-ui/issues/2)) a Z address, it is not recorded in the sender’s wallet.dat
  
The limitations require that a sender’s identity be established at the level of the messaging protocol (by signing the message with a T address). Every sender of a message (taking part in ZEN messaging) needs to have two addresses:
  * Z address to send and receive the messaging transaction (used for “transport” only)
  * T address to sign the actual message sent and prove the sender’s identity (for the use case when the user is not anonymous).

A sender (human) may (potentially) have multiple messaging identities/contact details but for every identity/contact details, a pair of Z+T addresses are to be created/designated for use. Every ZEN message is to be sent as one Z→Z transaction from sender to recipient (in the first protocol version).

### ZEN Messages with identifiable sender

When sent as a part of the memo field, a ZEN message is to be in JSON format and be encoded in UTF-8 in the memo field (all clients need to read/write the JSON to bytes using UTF-8). Example ZEN message (with an identifiable sender):
```
{
   "zenmsg":
   { "ver": 1,
     "from": "znn9wRVAkgNnKCYJvsHu9nm4Q7c6AxLE2qR",
     "message": "Actual chat message is here etc. whatever", 
     "sign": "H5L3vhCQ+3EAz7BRrpNqy2x42VF+oY1WowGxCwEJkMlcbfX+GLU3PWOzPwJK+BUBY5YoDk/hAkF4GwtqyWWOngI="
   }
}
```

The example has spacing/TABs but in production no spaces will be present between the fields. The fields in a ZEN message are:
  * `zenmsg`:  Designates this JSON object as a ZEN message (not to be confused)
  * `ver`: protocol version. The initial implementation will have protocol version 1. It is to support only plain text messages. Later protocol versions will have more features.
  * `from`: Address of the sender (T address). It is needed to make sure the sender can identify himself to the recipient.
  * `message`: This is the actual ZEN text (in the future html/RTF etc.) message that one user sends and another receives.
  * `sign`: Signature made of the text message content (encoded in HEX – see below!) using the T address. This proves that the sender actually has control over the T address (`from`).

With this messaging format the effective maximum length of a user message is approx. 340 characters (single byte chars). Future protocol versions may allow larger messages by sending one message split into multiple transactions etc. When a user sends a message (`zenmsg`), the text message is signed with the  T address (from) and the signature is included as sign.

There may be issues with message signing and non-ASCII text: the signing/verification is done by zend (a native application) and it receives commands from the wallet via command line (other GUI clients may use a direct RPC cpnnection). If the message chars are ASCII (or otherwise one byte per char) the signing/verification is expected to be the same across all platforms. However if the user enters other unusual chars (e.g. Chinese chars – that are encoded as 2-3 bytes each) it is not clear if the signing/verification will work alike on multiple platforms. To avoid this problem we can introduce the following rules to sign/verify the message:
  * The message as entered by the use in the GUI (in UNICODE) is converted to bytes using UTF-8 encoding.
  * The bytes are encoded in HEX and converted to upper case (a-f → A-F)
  * This resulting string (e.g. HEX encoded like 3132A4B52C3B65...) is then signed and verified (not the original message).

The above rules should give us the same behavior on different platforms and will not depend on possible issues with UNICODE support on a command line or in different UI frameworks etc. The conversion of UNICODE chars to bytes using UTF-8 is expected to work the same way no matter what GUI client is doing the conversion.

### ZEN a Anonymous messages

When the message is sent anonymously, the structure is to be slightly different: When user A sends the first ever message to user B - the GUI client generates a unique thread ID (UUID) for the messaging direction UserA->UserB, and stores it permanently. All subsequent unsigned messages UserA->UserB will come with the same persistent ID. The GUI client will present all anonymous messages that are received with the same ID, as coming from the same anonymous sender. An anonymous message example would be:

```
{
   "zenmsg":
   { 
     "ver": 1,
     "message": "Actual chat message is here etc. whatever", 
     "threadid": "123e4567-e89b-12d3-a456-426655440000"
   }
}
```

The difference as compared to a message with an identified user is that sign/from has been replaced with threadid. Since the sender of an anonymous message cannot be identified the recipient will not be able to respond to it in 1-2-1 conversations (he does not know the Z address to send a response to)! As an additional use case the sender's Z address may be included in the first (only the first) message sent in the direction UserA->UserB. UserB may then respond and all responses will be using the same thread ID (UUID). An anonymous message with the sender’s Z address to allow responding is to be like:

```
{
   "zenmsg":
   { 
     "ver": 1,
     "message": "Actual chat message is here etc. whatever", 
     "threadid": "123e4567-e89b-12d3-a456-426655440000",
     "returnaddress": "zcZLZ6hjYYCqZEg64aHpvCpaX7yQyvAedjhHkP2e1LWVSNmxhj6CUYJqAsPGXzxB5prMppyv2jsJxbGbw4JDvdxpPUbNNUa"
   }
}
```

The new field is returnaddress. Adding a return address may reveal a user's identity indirectly or otherwise weaken the anonymity… In addition thread ID is not 100% reliable as an indication that messages come from the same user, especially in group messaging scenarios (other users may see and fake it).

A message will either be signed with a T address or be anonymous having a thread ID (UUID) - but never both.

