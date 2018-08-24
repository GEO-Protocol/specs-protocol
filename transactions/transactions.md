`GEO Protocol / 2018`

![Twin Spark Logo](https://github.com/GEO-Project/specs-protocol/blob/master/transactions/resources/twin_spark.png)

Fast, safe, double-spending resistant, multi-attendee crypto-protocol  
for atomic and consistent assets transfers.
<br/>
<br/>
[`Dima Chizhevsky`](https://github.com/haysaycheese/) 
[`Mykola Ilashchuk`](https://github.com/MukolaIlashchuk) 
`Max Demiyan` 
[`Denis Vasilov`](https://github.com/Vasilov345)
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
# Abstract

# Overview
This specification describes transactions processing algorithm for the [GEO](https://github.com/GEO-Project) network.  
Proposed solution provides ability for up to 1024 network participants to achieve 100% consensus via potentially unstable network, in untrusted environment and/or during malicious participants behaviour.

The objectives of this document are:
1. Provide comprehensive info about proposed method of consensus;
1. Describe guaranteed prevention of double spending, using the distributed reservations locks;
1. Describe the cryptographic primitives used and the motivation to include them into the protocol;
1. Describe the method of mathematical and cryptographic confirmation of operations;
1. Mathematically confirm the impossibility (or extreme complexity) of compromising operations;
1. Provide a list of possible edge cases and describe ways to resolve them, as well as possible outcomes of operations.
