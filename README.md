# webrtc2

## Libraries

* Client-side Fingerprinting
  * https://github.com/Valve/fingerprintjs2
* Server-side Fingerprinting
  * https://www.npmjs.com/package/express-fingerprint
* Browser Client
  * https://github.com/feross/simple-peer
* Client Signatures
  * https://keybase.io/kbpgp

## Concepts

### Spacerifts
* HTTP-based peer-linking mechanism with minimum possible roundtrips.

| Method          | TYPE Parameter   | Other Parameters | Description   |
| :-------------: | :-------------:  | :-------------:    | :------------- |
| Request  | CONNECT   | SIGNAL        | Sent to server by Alice and Bob, as Initiators. |
| Response | DEMOTE    | NONCE, SIGNAL | Server sends Bob a Nonce and Alice's Signal for him to answer instead. |
| Request  | CONNECT   | NONCE, SIGNAL | Bob's answer to Alice's signal, sent to server. |
| Response | ANSWER    | SIGNAL | Bob's answer sent to Alice by server. |
| Response | DELIVERED |  | Server's confirmation that bob's answer has been sent to Alice. |
| Request  | BOUNCE   | NONCE, SIGNAL | Bob's new signal, plus Nonce from Alice to be bounced, sent to server. |

* Iterations on pending request should be triggered on each new incoming request, reactive approach.
* `NAMESPACE` parameter may be used so the server can be used by different projects.
* `https://github.com/expressjs/compression` can compress the signal string length.
* Use of client signatures along with namespaces, like `https://keybase.io/kbpgp`:
  * Load private key.
  * Load trusted public keys.
  * Sign outgoing, verify incoming.
  * Payload looks like `SIGNED_NAMESPACE`, `SIGNED_SIGNAL`, `PUBLIC_KEY`
* HTTP status codes on specific scenarios:
  * `299` (No Peer) - When no available peer is found.
  * `298` (No Namespace Peer) - When no available peer in specified namespace is found.
  * `297` (Rate Limit) - Request is immediately dropped because you're requesting too fast.
  * `499` (Missing Parameter) - Request error caused by a missing parameter.
* Possible configs / rate limits:
  * 1 request / second.
  * 1 peer connection / 5 seconds.
  * Request timeout: 15 seconds
* Possible action fallbacks
  * If bob already connected to alice and receives alice's request.. Bob sends `{ TYPE: 'BOUNCE', NONCE, SIGNAL}` to bounce it, for server to reassign alice, and for bob to reassign his signal to another peer.
  * If alice is already connected to bob, and receives an answer from bob.. This shouldn't happen, but instead Alice disregards it and sends a new `{ TYPE: 'CONNECT', SIGNAL }` again.
  * If bob timeout, bob sends new `{ TYPE: 'CONNECT', SIGNAL }`
  * If alice timeout, alice sends new `{ TYPE: 'CONNECT', SIGNAL }`
  * If server receives bob's answer but alice's request is already disconnected / void, server sends bob another `{ TYPE: 'DEMOTE', NONCE,  SIGNAL}`

### Paired Peer Memory

```
let peer1_fingerprint = 'peer1';
let peer2_fingerprint = 'peer2';
let record = { peer1_fingerprint, peer2_fingerprint, timestamp: Date.now() }; // Create record.

let PairMemory = new Map();
PairMemory.set(record); // Save record

setInterval(() => {

  PairMemory.forEach((value, key) => {
    if ((Date.now() - key.timestamp) > 3600 * 1000) { // If record was order than 1 hr..
      PairMemory.delete(key); // Delete.
    }
  });
  
}, 30000); // Check every 30 seconds.
```

## License

Attribution 4.0 International (CC BY 4.0)

* https://creativecommons.org/licenses/by/4.0/
* https://creativecommons.org/licenses/by/4.0/legalcode.txt

![c](https://creativecommons.org/images/deed/cc_blue_x2.png) ![a](https://creativecommons.org/images/deed/attribution_icon_blue_x2.png)
