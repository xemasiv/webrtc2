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

* Iterations on pending request should be triggered on each new incoming request, reactive approach.
* `NAMESPACE` parameter may be used so the server can be used by different projects.
* `https://github.com/expressjs/compression` can compress the signal string length.
* Use of client signatures along with namespaces, like `https://keybase.io/kbpgp`
  * Load private key.
  * Load trusted public keys.
  * Sign outgoing, verify incoming.
  * Payload looks like `SIGNED_NAMESPACE`, `SIGNED_SIGNAL`, `PUBLIC_KEY`
* HTTP error codes on specific scenarios
  * `299` (No Peer) - When no available peer is found.
  * `298` (No Namespace Peer) - When no available peer in specified namespace is found.
  * `297` (Rate Limit) - Request is immediately dropped because you're requesting too fast.
* Request limits
  * 1 request / second.
  * 1 peer connection / 5 seconds.

## License

Attribution 4.0 International (CC BY 4.0)

* https://creativecommons.org/licenses/by/4.0/
* https://creativecommons.org/licenses/by/4.0/legalcode.txt

![c](https://creativecommons.org/images/deed/cc_blue_x2.png) ![a](https://creativecommons.org/images/deed/attribution_icon_blue_x2.png)
