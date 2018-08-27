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
  
**4. Requirements for anonymity of operations:**
  1. _Network addressation agnostic_  
  Algorithm must be agnostic on addressation mechanics, so the participants might be present by any kind of addresses (IP / any kind of name resolutions services / prosy services, etc). As a consequence - algorithm should support anonimization via tor and other similar networks.
  
  1. _Lack of a common ledger and common operations history_
  The outcome of the operation must be determined and distributed only within the participants of this operation. The remaining nodes of the network, who do not participate in the operation, must not be able to retrieve the data about operation, and/or its content.
  
**5. Requirements for network resources:**
  1. _Resistance to unstable networks._  
  Algorithm is resistant to network interference, packet loss and/or even whole messages loss. In the worst case, if consensus can't be reached, the operation must be canceled on all devices participating in it.
  
  1. _Transport protocol agnostic._  
  Algorithm must not require a permanent connection and must not base its own mechanics on the guarantees provided by different protocols, starting with the transport layer of the OSI model (for example, TCP). The reference implementation of the algorithm is based on UDP (see network protocol specifications for the details) [※ 1].
  
**6. Requirements for fault tolerance:**
  1. _Strict operation completion guarantee_  
  Algorithm in conjunction with the rest of the GEO protocol ecosystem solutions, must be ablr to ensure the finality of any running operation on the network. Scenarios under which the operation was started, but can not be completed (dead locks) must be striclty documnted and excluded.
  
  1. _Automatic synchronization_  
  Algorithm must be able to automatically resolve possible conflicts between participants of the network, leading them back to the synchronization state, via strit logic of cryptographically-based solutions.
  
  
**7. Requirements for the speed of operations:**
  1. _Sub-second processing time for one participant._  
  This point depends very much on the configuration of the separately taken node, but in general, the GEO Protocol team aims to create an algorithm that is as effective in time as possible. This approach is reflected in a number of optimizations adopted in the algorithm, and continues to find a place in the new solutions.
  
**8. Requirements for portability:**
  1. The algorithm must not contain platform-dependent components.
  1. The algorithm must not be hardware specific or dependent.
  
  
※ To increase the probabilty of achieving consensus, and / or shorten the time of the algorithm execution, various traffic optimisation and correction techniques can be used as well. This protocol does not assumes any of them to be present, but obviously works better in the environments with stable network connections.


# Cryptographic primitives
This section describes the used cryptographic primitives and the motivation for their inclusion in the protocol.

## Lamport Signature
[_Lamport signature_](https://en.wikipedia.org/wiki/Lamport_signature) or _Lamport one-time signature scheme_ is a method for constructing a digital signature. Lamport signatures can be built from any cryptographically secure one-way function; usually a cryptographic hash function is used [※ 1]. In Twin Spark, Lamport's signature is based on [_BLAKE2b_](https://blake2.net/).

### Limitations related to Lamport Signature
Because Lamport signature is a one-time scheme (each one signing procedure discloses part of the private key), `n` key pairs are needed for `n` operations processing. Thus, each one pair of network members, who has common trust line, or channel, must generate a pool of keys, to be able to sign operations, before any other operations would be possible to process. By default, this pool contains `1024` key pairs. Detailed descriptions of the key exchange mechanics and channels establishment can be found in the trust lines specifications `[todo #5: provide link to TL specifications]`.

### Difficulties of applicability in existing solutions
A pair of Lamport keys (Private key and Public key) takes `32 kB`:
* `16 kB` — public key.
* `16 kB` — private key.
* `8 kB` — signature.

The size of signatures and related keys genarated via the Lamport Signature scheme, are a way more expensive than corresponding sizes of signatures and keys, which are based on classic async. cryptograpty solutions (RSA/ECDSA/etc). The relatively large size of signatures makes the Lamport crypto system a very unlikely candidate for the inclusion in existing blockchain solutions in the foreseeable future due to a significant increase in the ledgers growth rate. Also, Lamport's signature, has no practical sense in systems whose cryptographic strength directly depends on components built on classical approaches and are known to be vulnerable against quantum-based attacks. For example, the inclusion of the Lamport cryptosystem in solutions built on top of ethereum / bitcoin, or other popular blockchain solution, has no practical meaning due to the underlying potential vulnerability of the central ledgers of these systems.

※ For each hash function that generates an n-bit digest, the ideal resiliency to restore the prototype and to restore the second prototype implies 2n operations and 2n bits of memory for each one execution of the hash function in the classical computational model. Using the Grover algorithm, restoring the pre-image of the ideal hash value is bounded from above by `O (2n / 2)` operations in the quantum computational model. At the moment, Lamport's signature is considered one of the most reliable digital signature algorithms, proved to be resistant to attacks using quantum computing.

