### 3.3.1.  EAP Sequences

   EAP [RFC3748] prohibits use of multiple authentication methods within
   a single EAP conversation in order to limit vulnerabilities to man-
   in-the-middle attacks.  TEAP addresses man-in-the-middle attacks
   through support for cryptographic protection of the inner EAP
   exchange and cryptographic binding of the inner authentication
   method(s) to the protected tunnel.  EAP methods are executed serially
   in a sequence.  This version of TEAP does not support initiating
   multiple EAP methods simultaneously in parallel.  The methods need
   not be distinct.  For example, EAP-TLS could be run twice as an inner
   method, first using machine credentials followed by a second instance
   using user credentials.

   EAP method messages are carried within EAP-Payload TLVs defined in
   Section 4.2.10.  If more than one method is going to be executed in
   the tunnel, then upon method completion, the server MUST send an
   Intermediate-Result TLV indicating the result.  The peer MUST respond
   to the Intermediate-Result TLV indicating its result.  If the result
   indicates success, the Intermediate-Result TLV MUST be accompanied by
   a Crypto-Binding TLV.  The Crypto-Binding TLV is further discussed in
   Sections 4.2.13 and 5.3.  The Intermediate-Result TLVs can be
   included with other TLVs such as EAP-Payload TLVs starting a new EAP
   conversation or with the Result TLV used in the protected termination
   exchange.

   If both peer and server indicate success, then the method is
   considered complete.  If either indicates failure, then the method is
   considered failed.  The result of failure of an EAP method does not
   always imply a failure of the overall authentication.  If one
   authentication method fails, the server may attempt to authenticate
   the peer with a different method.
