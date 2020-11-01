
## 3.6.  Error Handling

   TEAP uses the error-handling rules summarized below:

   1.  Errors in the outer EAP packet layer are handled as defined in
       Section 3.6.1.

   2.  Errors in the TLS layer are communicated via TLS alert messages
       in all phases of TEAP.

   3.  The Intermediate-Result TLVs carry success or failure indications
       of the individual EAP methods in TEAP Phase 2.  Errors within the
       EAP conversation in Phase 2 are expected to be handled by
       individual EAP methods.

   4.  Violations of the Inner TLV rules are handled using Result TLVs
       together with Error TLVs.

   5.  Tunnel-compromised errors (errors caused by a failed or missing
       Crypto-Binding) are handled using Result TLVs and Error TLVs.

### 3.6.1.  Outer-Layer Errors

   Errors on the TEAP outer-packet layer are handled in the following
   ways:

   1.  If Outer TLVs are invalid or contain unknown values, they will be
       ignored.

   2.  The entire TEAP packet will be ignored if other fields (version,
       length, flags, etc.) are inconsistent with this specification.

### 3.6.2.  TLS Layer Errors

   If the TEAP server detects an error at any point in the TLS handshake
   or the TLS layer, the server SHOULD send a TEAP request encapsulating
   a TLS record containing the appropriate TLS alert message rather than
   immediately terminating the conversation so as to allow the peer to
   inform the user of the cause of the failure and possibly allow for a
   restart of the conversation.  The peer MUST send a TEAP response to
   an alert message.  The EAP-Response packet sent by the peer may
   encapsulate a TLS ClientHello handshake message, in which case the
   TEAP server MAY allow the TEAP conversation to be restarted, or it
   MAY contain a TEAP response with a zero-length message, in which case
   the server MUST terminate the conversation with an EAP Failure
   packet.  It is up to the TEAP server whether or not to allow
   restarts, and, if allowed, how many times the conversation can be
   restarted.  Per TLS [RFC5246], TLS restart is only allowed for non-
   fatal alerts.  A TEAP server implementing restart capability SHOULD
   impose a limit on the number of restarts, so as to protect against
   denial-of-service attacks.  If the TEAP server does not allow
   restarts, it MUST terminate the conversation with an EAP Failure
   packet.

   If the TEAP peer detects an error at any point in the TLS layer, the
   TEAP peer SHOULD send a TEAP response encapsulating a TLS record
   containing the appropriate TLS alert message.  The server may restart
   the conversation by sending a TEAP request packet encapsulating the
   TLS HelloRequest handshake message.  The peer may allow the TEAP
   conversation to be restarted, or it may terminate the conversation by
   sending a TEAP response with a zero-length message.

### 3.6.3.  Phase 2 Errors

   Any time the peer or the server finds a fatal error outside of the
   TLS layer during Phase 2 TLV processing, it MUST send a Result TLV of
   failure and an Error TLV with the appropriate error code.  For errors
   involving the processing of the sequence of exchanges, such as a
   violation of TLV rules (e.g., multiple EAP-Payload TLVs), the error
   code is Unexpected TLVs Exchanged.  For errors involving a tunnel
   compromise, the error code is Tunnel Compromise Error.  Upon sending
   a Result TLV with a fatal Error TLV, the sender terminates the TLS
   tunnel.  Note that a server will still wait for a message from the
   peer after it sends a failure; however, the server does not need to
   process the contents of the response message.

   For the inner method, retransmission is not needed and SHOULD NOT be
   attempted, as the Outer TLS tunnel can be considered a reliable
   transport.  If there is a non-fatal error handling the inner method,
   instead of silently dropping the inner method request or response and
   not responding, the receiving side SHOULD use an Error TLV with error
   code Inner Method Error to indicate an error processing the current
   inner method.  The side receiving the Error TLV MAY decide to start a
   new inner method instead or send back a Result TLV to terminate the
   TEAP authentication session.

   If a server receives a Result TLV of failure with a fatal Error TLV,
   it MUST send a cleartext EAP Failure.  If a peer receives a Result
   TLV of failure, it MUST respond with a Result TLV indicating failure.
   If the server has sent a Result TLV of failure, it ignores the peer
   response, and it MUST send a cleartext EAP Failure.
