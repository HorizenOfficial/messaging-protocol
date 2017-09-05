# ZEN Messaging Protocol
![Logo](https://avatars0.githubusercontent.com/u/29291571?v=4&s=200 "Logo")

One of the directions of development of ZENCash is secure communications. This specification is intended to describe in detail the initial messaging features to be implemented and to also chart a course for their extension in the future. To ensure inter-application compatibility ZEN messaging is to be based on a well-defined protocol so that future apps (wallets/chat clients etc) may be made compatible with each other. 

Messaging use cases may be categorized as:
  * 1-2-1 and group messaging – in the former case two users chat with each other (classic chat case). In the latter case many users send messages to a common “channel”. New users may be added to the channel.
  * Messages with identifiable senders and anonymous messages – in some cases users wish to prove their identity to the recipients. In other cases users wish to remain anonymous.

Based on the above we get 4 major uses case combinations (1-2-1 / group) combined with (anonymous / identifiable senders).

## Contents
### [ZEN Messaging Identities](Identities.md)
### [ZEN Messaging Protocol - version 1](Protocol_v1.md)




---

Copyright (c) 2017 Ivan Vaklinov

Permission is granted to copy, distribute and/or modify this document
under the terms of the GNU Free Documentation License, Version 1.3
or any later version published by the Free Software Foundation;
with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
A copy of the license is included in the section entitled 
["GNU Free Documentation License"](LICENSE).
