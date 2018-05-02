# webrtc2

## Libraries

* Client Fingerprinting
  * https://github.com/Valve/fingerprintjs2
* Browser Client
  * https://github.com/feross/simple-peer

## Concepts

### Spacerifts
* Servers are simple HTTP Servers

| Method          | TYPE Parameter   | Other Parameters | Description   |
| :-------------: | :-------------:  | :-------------:    | :------------- |
| Request  | CONNECT   | SIGNAL        | Sent to server by Alice and Bob, as Initiators. |
| Response | DEMOTE    | NONCE, SIGNAL | Server sends Bob a Nonce and Alice's Signal for him to answer instead. |
| Request  | CONNECT   | NONCE, SIGNAL | Bob's answer to Alice's signal, sent to server. |
| Response | ANSWER    | SIGNAL | Bob's answer sent to Alice by server. |
| Response | DELIVERED |  | Server's confirmation that bob's answer has been sent to Alice. |

## License

Attribution 4.0 International (CC BY 4.0)

* https://creativecommons.org/licenses/by/4.0/
* https://creativecommons.org/licenses/by/4.0/legalcode.txt

![c](https://creativecommons.org/images/deed/cc_blue_x2.png) ![a](https://creativecommons.org/images/deed/attribution_icon_blue_x2.png)
