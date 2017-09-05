## ZEN Messaging Protocol - version 1

ZEN user messages are to be transported as a part of T→Z and Z→Z ZEN transactions in the memo field. The memo filed has a maximum length of 512 bytes and when specified on a command line needs to be in HEX format. There are currently some limitations in JoinSplits/→Z transactions that complicate the design of the protocol somewhat:
  * When Z address receives an incoming transaction the recipient sees the date/amount/memo but does not see the sending T/Z address. This seems to be the case for both T→Z and Z→Z ZEN transactions.
  * When a transaction is sent from (also sometimes to – bug #2) a Z address, it is not recorded in the sender’s wallet.dat
  
The limitations require that a sender’s identity be established at the level of the messaging protocol (by signing the message with a T address). Every sender of a message (taking part in ZEN messaging) needs to have two addresses:
  * Z address to send and receive the messaging transaction (used for “transport” only)
  * T address to sign the actual message sent and prove the sender’s identity (for the use case when the user is not anonymous).

A sender (human) may (potentially) have multiple messaging identities/contact details but for every identity/contact details, a pair of Z+T addresses are to be created/designated for use. Every ZEN message is to be sent as one Z→Z transaction from sender to recipient (in the first protocol version).

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

