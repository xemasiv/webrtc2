# webrtc2

## Libraries

| Side | Library | Link | Purpose |
| :----: | :-------: | :---- | :------- |
| Client | simple-peer | https://www.npmjs.com/package/simple-peer | WebRTC |
| Server | wrtc | https://www.npmjs.com/package/wrtc | WebRTC |
| Both | simple-websocket | https://github.com/feross/simple-websocket | WebSockets |
| Client | fingerprintjs2 | https://www.npmjs.com/package/fingerprintjs2 | Fingerprint |
| Server | express-fingerprint | https://www.npmjs.com/package/express-fingerprint | Fingerprint |
| Client | localforage | https://www.npmjs.com/package/localforage | Storage |
| Client | kbpgp | https://www.npmjs.com/package/kbpgp | Signatures |
| Both | js-sha3 | https://github.com/emn178/js-sha3 | Hashes |
| Both | js-sha256 | https://github.com/emn178/js-sha256 | Hashes |
| Both | js-sha512 | https://github.com/emn178/js-sha512 | Hashes |
| Both | moment | https://www.npmjs.com/package/moment | Time |
| Both | pako | https://www.npmjs.com/package/pako | Compression |

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
  * If server receives bob's answer but alice's request is already disconnected / void, server sends bob another `{ TYPE: 'DEMOTE', NONCE,  SIGNAL}`. Use `https://stackoverflow.com/a/32469346` / `https://www.npmjs.com/package/on-finished` to detect disconnection with express.

### Paired Peer Memory

* Server mechanism for remembering which peers have been paired in the last x minutes.
* Can use `https://www.npmjs.com/package/express-fingerprint` for server-side fingerprinting.

```
let peer1_fingerprint = 'peer1';
let peer2_fingerprint = 'peer2';
let record = { peer1_fingerprint, peer2_fingerprint, timestamp: Date.now() }; // Create record.

let PairMemory = new Map();
PairMemory.set(record); // Save record

setInterval(() => {

  PairMemory.forEach((value, key) => {
    if ((Date.now() - key.timestamp) > 300 * 1000) { // If record was older than 5 mins..
      PairMemory.delete(key); // Delete it.
    }
  });
  
}, 30000); // Do cleanup every 30 seconds.
```

### Best-latency peer rotation

* Summary:
  * Server-assisted peer rotation, clients' high-latency peers are exchanged to lower-latency peers.
  * Goal is to cut-off ties with high-latency peers, in exchange for lower-latency peers.
* How:
  * Clients have their own server-identified id's.
  * Clients sync their `time` adjustment using servers.
  * Clients measure their latency with their peers.
  * Clients send their geolocation to server (more accurate that relying on IP to Location).
  * Server identifies non-linked peers nearby clients.
  * Server identifies the closer ones using Google's spatial geometry function.
  * Client identifies best-replaceable peers (those with high-latency over X samples)
  * Server sends suggestion to client about potential connection :heart:
* Factors considered:
  * Latency of clients with their existing peers
  * Geolocation of clients
* Client Geolocation:
  * https://caniuse.com/#feat=geolocation
  * https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/Using_geolocation
* Distance between two (2) geolocations
  * https://www.npmjs.com/package/spherical-geometry-js
  

## License

Attribution 4.0 International (CC BY 4.0)

* https://creativecommons.org/licenses/by/4.0/
* https://creativecommons.org/licenses/by/4.0/legalcode.txt

![c](https://creativecommons.org/images/deed/cc_blue_x2.png) ![a](https://creativecommons.org/images/deed/attribution_icon_blue_x2.png)
