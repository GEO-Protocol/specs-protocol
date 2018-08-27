`GEO Protocol / 2018`

![Twin Spark Logo](https://github.com/GEO-Project/specs-protocol/blob/master/transactions/resources/twin_spark.png)

Fast, safe, double-spending resistant, multi-attendee crypto-protocol  
for atomic and consistent assets transfers.
<br/>
<br/>
[`Dima Chizhevsky`](https://github.com/haysaycheese/) 
[`Mykola Ilashchuk`](https://github.com/MukolaIlashchuk) 
[`Max Demiyan`](https://github.com/MaxDemyan)
[`Denis Vasilov`](https://github.com/Vasilov345)
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
# Abstract
This specification describes transactions processing algorithm (Algorithm further in the doc) for the [GEO](https://github.com/GEO-Project) network.  
Proposed solution provides ability for up to 1024 network participants [※ 1] to achieve 100% consensus via potentially unstable network, in untrusted environment and under active fraud attempts of malicious participants.

<br/>
※ potentially much more.

# Overview
The objectives of this document are:
1. to provide comprehensive info about proposed method of consensus;
1. to describe guaranteed double spending impossibility, and distributed locks mechanics;
1. to describe the cryptographic primitives used and the provide motivation for including ech of them into the protocol;
1. to describe the method of mathematical and cryptographic confirmation of operations (consensus checking);
1. to prodive mathematical confirmation of the impossibility (or extreme complexity) of the operations compromising;
1. to provide a list of possible edge cases and to describe the ways to avoid/resovle them, as well as possible outcomes of operations.

# Source conditions
This section lists the functional and design requirements for the proposed algorithm — the metrics, that were used to analyze all found and potentially applicable solutions for their applicability in the final protocol.

**1. Requirements for cryptographic primitives:**
  1. _Quantum-resistant cryptography._  
  Algorithm must be avare of usage of cryptographic solutions, which are potentially easily compromised in quantum-based environment (RSA / ECDSA, and other solutions that are based on similar mathematical problems).  
  
  1. _Strict minimum of crypto-primitives._  
  Algorithm must use strict minimum of the crypto systems. The role of each of them must be strictly defined and clearly motivated. 
  
**2. Requirements for operations:**
   1. _Strict 100% consensus._  
   Algorithm must enforce achievement of 100% consensus (with possibility of subsequent verification of it) between all  participants involved: the operation itself must be considered as conducted only if all participants (nodes) has approved it. Otherwise - operation must be cancelled also on all participants devices. Achieved consensus must be mathematically approvable and verifieble by any participant of the operation.
   
  1. _Deferred atomicity._  
  For each one operations launched in the network, all nodes involved in it, must achieve final state, or discard the operation at all. Intermediate states are acceptable only in short period of time, but are unacceptable to be permanent. Achieving atomicity should be:
    1. time-predictable (ideally about a few seconds).
    1. safe towards other operations (launched in parallel).
    1. agnostic towards nodes internal code modifications (hacking) and destructive motivation of the involved participants (fraud attempts).

**3. Requirements for end-point devices (nodes):**
  1. _Applicability for modern smartphones._  
  Algorithm should be usable in environments with limited computing resources and memory. Operations that consume a large amount of resources (for example, operations with significant number of participants), should be easy to process partially (pipelining).
  
  1. _Computational efficiency._  
  Algorithm should have low computational complexity. The mechanism for achieving consensus must avoid frequent calling of complex cryptographic operations (as, for example, Proof Of Work mechanics assumes in some blockchain-based solutions).
