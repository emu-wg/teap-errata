## Appendix C.  Examples

### C.1.  Successful Authentication

   The following exchanges show a successful TEAP authentication with
   basic password authentication and optional PAC refreshment.  The
   conversation will appear as follows:

       Authenticating Peer     Authenticator
       -------------------     -------------
                               <- EAP-Request/
                               Identity
       EAP-Response/
       Identity (MyID1) ->

                               <- EAP-Request/
                               EAP-Type=TEAP, V=1
                               (TEAP Start, S bit set, Authority-ID)

       EAP-Response/
       EAP-Type=TEAP, V=1
       (TLS client_hello with
        PAC-Opaque in SessionTicket extension)->

                               <- EAP-Request/
                               EAP-Type=TEAP, V=1
                               (TLS server_hello,
                               (TLS change_cipher_spec,
                                TLS finished)

       EAP-Response/
       EAP-Type=TEAP, V=1 ->
       (TLS change_cipher_spec,
        TLS finished)

       TLS channel established
       (messages sent within the TLS channel)

                              <- Basic-Password-Auth-Req TLV, Challenge

       Basic-Password-Auth-Resp TLV, Response with both
       username and password) ->

       optional additional exchanges (new pin mode,
       password change, etc.) ...

                            <- Crypto-Binding TLV (Request),
                                Result TLV (Success),
                                (Optional PAC TLV)

       Crypto-Binding TLV(Response),
       Result TLV (Success),
       (PAC-Acknowledgement TLV) ->

       TLS channel torn down
       (messages sent in cleartext)

                               <- EAP-Success

### C.2.  Failed Authentication

   The following exchanges show a failed TEAP authentication due to
   wrong user credentials.  The conversation will appear as follows:

       Authenticating Peer     Authenticator
       -------------------     -------------
                               <- EAP-Request/Identity

       EAP-Response/
       Identity (MyID1) ->


                               <- EAP-Request/
                               EAP-Type=TEAP, V=1
                               (TEAP Start, S bit set, Authority-ID)

       EAP-Response/
       EAP-Type=TEAP, V=1
       (TLS client_hello with
        PAC-Opaque in SessionTicket extension)->

                               <- EAP-Request/
                               EAP-Type=TEAP, V=1
                               (TLS server_hello,
                               (TLS change_cipher_spec,
                                TLS finished)

       EAP-Response/
       EAP-Type=TEAP, V=1 ->
       (TLS change_cipher_spec,
        TLS finished)

       TLS channel established
       (messages sent within the TLS channel)

                              <- Basic-Password-Auth-Req TLV, Challenge

       Basic-Password-Auth-Resp TLV, Response with both
       username and password) ->

                               <- Result TLV (Failure)

       Result TLV (Failure) ->

       TLS channel torn down
       (messages sent in cleartext)

                               <- EAP-Failure

### C.3.  Full TLS Handshake Using Certificate-Based Ciphersuite

   In the case within TEAP Phase 1 where an abbreviated TLS handshake is
   tried, fails, and falls back to the certificate-based full TLS
   handshake, the conversation will appear as follows:

      Authenticating Peer    Authenticator
      -------------------    -------------
                             <- EAP-Request/Identity
      EAP-Response/
      Identity (MyID1) ->

      // Identity sent in the clear.  May be a hint to help route
         the authentication request to EAP server, instead of the
         full user identity.

                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TEAP Start, S bit set, Authority-ID)
      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_hello with
      PAC-Opaque in SessionTicket extension)->

      // Peer sends PAC-Opaque of Tunnel PAC along with a list of
         ciphersuites supported.  If the server rejects the PAC-
         Opaque, it falls through to the full TLS handshake.

                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS server_hello,
                               TLS certificate,
                              [TLS server_key_exchange,]
                              [TLS certificate_request,]
                               TLS server_hello_done)

      EAP-Response/
      EAP-Type=TEAP, V=1
      ([TLS certificate,]
       TLS client_key_exchange,
      [TLS certificate_verify,]
       TLS change_cipher_spec,
       TLS finished) ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS change_cipher_spec,
                               TLS finished,
                               EAP-Payload-TLV[EAP-Request/
                               Identity])

      // TLS channel established
         (messages sent within the TLS channel)

      // First EAP Payload TLV is piggybacked to the TLS Finished as
         Application Data and protected by the TLS tunnel.

      EAP-Payload-TLV
      [EAP-Response/Identity (MyID2)]->

      // identity protected by TLS.

                               <- EAP-Payload-TLV
                               [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X] ->

      // Method X exchanges followed by Protected Termination

                           <- Intermediate-Result-TLV (Success),
                               Crypto-Binding TLV (Request),
                               Result TLV (Success)

      Intermediate-Result-TLV (Success),
      Crypto-Binding TLV (Response),
      Result-TLV (Success) ->

      // TLS channel torn down
      (messages sent in cleartext)

                              <- EAP-Success

### C.4.  Client Authentication during Phase 1 with Identity Privacy

   In the case where a certificate-based TLS handshake occurs within
   TEAP Phase 1 and client certificate authentication and identity
   privacy is desired (and therefore TLS renegotiation is being used to
   transmit the peer credentials in the protected TLS tunnel), the
   conversation will appear as follows:

      Authenticating Peer     Authenticator
      -------------------     -------------
                             <- EAP-Request/Identity
      EAP-Response/
      Identity (MyID1) ->

      // Identity sent in the clear.  May be a hint to help route
         the authentication request to EAP server, instead of the
         full user identity.

                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TEAP Start, S bit set, Authority-ID)
      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_hello)->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS server_hello,
                               TLS certificate,
                              [TLS server_key_exchange,]
                              [TLS certificate_request,]
                               TLS server_hello_done)
      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_key_exchange,
       TLS change_cipher_spec,
       TLS finished) ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS change_cipher_spec,
                               TLS finished,
                               EAP-Payload-TLV[EAP-Request/
                               Identity])

      // TLS channel established
         (EAP Payload messages sent within the TLS channel)

      // peer sends TLS client_hello to request TLS renegotiation

      TLS client_hello ->

                              <- TLS server_hello,
                               TLS certificate,
                               [TLS server_key_exchange,]
                               [TLS certificate_request,]
                               TLS server_hello_done
      [TLS certificate,]
       TLS client_key_exchange,
      [TLS certificate_verify,]
       TLS change_cipher_spec,
       TLS finished ->

                              <- TLS change_cipher_spec,
                                 TLS finished,
                                 Crypto-Binding TLV (Request),
                                 Result TLV (Success)

      Crypto-Binding TLV (Response),
      Result-TLV (Success)) ->

      //TLS channel torn down
      (messages sent in cleartext)

                              <- EAP-Success

### C.5.  Fragmentation and Reassembly

   In the case where TEAP fragmentation is required, the conversation
   will appear as follows:

      Authenticating Peer     Authenticator
      -------------------     -------------
                              <- EAP-Request/
                              Identity
      EAP-Response/
      Identity (MyID) ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TEAP Start, S bit set, Authority-ID)

      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_hello)->

                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS server_hello,
                               TLS certificate,
                              [TLS server_key_exchange,]
                              [TLS certificate_request,]
                               TLS server_hello_done)
                              (Fragment 1: L, M bits set)

      EAP-Response/
      EAP-Type=TEAP, V=1 ->

                              <- EAP-Request/
                                 EAP-Type=TEAP, V=1
                              (Fragment 2: M bit set)
      EAP-Response/
      EAP-Type=TEAP, V=1 ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (Fragment 3)
      EAP-Response/
      EAP-Type=TEAP, V=1
      ([TLS certificate,]
       TLS client_key_exchange,
      [TLS certificate_verify,]
       TLS change_cipher_spec,
       TLS finished)
       (Fragment 1: L, M bits set)->

                               <- EAP-Request/
                              EAP-Type=TEAP, V=1
      EAP-Response/
      EAP-Type=TEAP, V=1
      (Fragment 2)->
                             <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS change_cipher_spec,
                               TLS finished,
                              [EAP-Payload-TLV[
                              EAP-Request/Identity]])

      // TLS channel established
         (messages sent within the TLS channel)

      // First EAP Payload TLV is piggybacked to the TLS Finished as
         Application Data and protected by the TLS tunnel.

      EAP-Payload-TLV
      [EAP-Response/Identity (MyID2)]->

      // identity protected by TLS.

                               <- EAP-Payload-TLV
                               [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X] ->

      // Method X exchanges followed by Protected Termination

                           <- Intermediate-Result-TLV (Success),
                               Crypto-Binding TLV (Request),
                               Result TLV (Success)

      Intermediate-Result-TLV (Success),
      Crypto-Binding TLV (Response),
      Result-TLV (Success) ->

      // TLS channel torn down
      (messages sent in cleartext)

                              <- EAP-Success

### C.6.  Sequence of EAP Methods

   When TEAP is negotiated with a sequence of EAP method X followed by
   method Y, the conversation will occur as follows:

      Authenticating Peer     Authenticator
      -------------------     -------------
                              <- EAP-Request/
                              Identity
      EAP-Response/
      Identity (MyID1) ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TEAP Start, S bit set, Authority-ID)

      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_hello)->

                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS server_hello,
                               TLS certificate,
                              [TLS server_key_exchange,]
                              [TLS certificate_request,]
                               TLS server_hello_done)
      EAP-Response/
      EAP-Type=TEAP, V=1
      ([TLS certificate,]
       TLS client_key_exchange,
      [TLS certificate_verify,]
       TLS change_cipher_spec,
       TLS finished) ->
                             <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS change_cipher_spec,
                               TLS finished,
                               Identity-Type TLV,
                              EAP-Payload-TLV[
                              EAP-Request/Identity])

      // TLS channel established
         (messages sent within the TLS channel)

      // First EAP Payload TLV is piggybacked to the TLS Finished as
         Application Data and protected by the TLS tunnel

      Identity_Type TLV
      EAP-Payload-TLV
      [EAP-Response/Identity] ->

                              <- EAP-Payload-TLV
                            [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X] ->

             // Optional additional X Method exchanges...

                             <- EAP-Payload-TLV
                            [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X]->

                              <- Intermediate Result TLV (Success),
                               Crypto-Binding TLV (Request),
                               Identity-Type TLV,
                               EAP Payload TLV [EAP-Type=Y],

      // Next EAP conversation started after successful completion
         of previous method X.  The Intermediate-Result and Crypto-
         Binding TLVs are sent in next packet to minimize round
         trips.  In this example, an identity request is not sent
         before negotiating EAP-Type=Y.

      // Compound MAC calculated using keys generated from
         EAP method X and the TLS tunnel.

      Intermediate Result TLV (Success),
      Crypto-Binding TLV (Response),
      EAP-Payload-TLV [EAP-Type=Y] ->

             // Optional additional Y Method exchanges...

                             <- EAP Payload TLV [
                             EAP-Type=Y]

      EAP Payload TLV
      [EAP-Type=Y] ->

                             <- Intermediate-Result-TLV (Success),
                               Crypto-Binding TLV (Request),
                               Result TLV (Success)

      Intermediate-Result-TLV (Success),
      Crypto-Binding TLV (Response),
      Result-TLV (Success) ->

      // Compound MAC calculated using keys generated from EAP
         methods X and Y and the TLS tunnel.  Compound keys are
         generated using keys generated from EAP methods X and Y
         and the TLS tunnel.

      // TLS channel torn down (messages sent in cleartext)

                              <- EAP-Success

### C.7.  Failed Crypto-Binding

   The following exchanges show a failed crypto-binding validation.  The
   conversation will appear as follows:

   Authenticating Peer     Authenticator
   -------------------     -------------
                           <- EAP-Request/
                           Identity
    EAP-Response/
    Identity (MyID1) ->
                            <- EAP-Request/
                            EAP-Type=TEAP, V=1
                            (TEAP Start, S bit set, Authority-ID)

    EAP-Response/
    EAP-Type=TEAP, V=1
    (TLS client_hello without
    PAC-Opaque in SessionTicket extension)->
                            <- EAP-Request/
                            EAP-Type=TEAP, V=1
                            (TLS Server Key Exchange
                             TLS Server Hello Done)
    EAP-Response/
    EAP-Type=TEAP, V=1 ->
    (TLS Client Key Exchange
     TLS change_cipher_spec,
     TLS finished)

                           <- EAP-Request/
                           EAP-Type=TEAP, V=1
                           (TLS change_cipher_spec
                            TLS finished)
                            EAP-Payload-TLV[
                            EAP-Request/Identity])

      // TLS channel established
         (messages sent within the TLS channel)

      // First EAP Payload TLV is piggybacked to the TLS Finished as
         Application Data and protected by the TLS tunnel.

      EAP-Payload TLV/
      EAP Identity Response ->

                          <-  EAP Payload TLV, EAP-Request,
                              (EAP-MSCHAPV2, Challenge)

      EAP Payload TLV, EAP-Response,
      (EAP-MSCHAPV2, Response) ->

                          <-  EAP Payload TLV, EAP-Request,
                              (EAP-MSCHAPV2, Success Request)

      EAP Payload TLV, EAP-Response,
      (EAP-MSCHAPV2, Success Response) ->

                        <- Intermediate-Result-TLV (Success),
                            Crypto-Binding TLV (Request),
                               Result TLV (Success)

      Intermediate-Result-TLV (Success),
      Result TLV (Failure)
      Error TLV with
      (Error Code = 2001) ->

      // TLS channel torn down
      (messages sent in cleartext)

                           <- EAP-Failure

### C.8.  Sequence of EAP Method with Vendor-Specific TLV Exchange

   When TEAP is negotiated with a sequence of EAP methods followed by a
   Vendor-Specific TLV exchange, the conversation will occur as follows:

      Authenticating Peer     Authenticator
      -------------------     -------------
                              <- EAP-Request/
                              Identity
      EAP-Response/
      Identity (MyID1) ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TEAP Start, S bit set, Authority-ID)

      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_hello)->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS server_hello,
                               TLS certificate,
                       [TLS server_key_exchange,]
                       [TLS certificate_request,]
                           TLS server_hello_done)

      EAP-Response/
      EAP-Type=TEAP, V=1
      ([TLS certificate,]
       TLS client_key_exchange,
      [TLS certificate_verify,]
       TLS change_cipher_spec,
       TLS finished) ->
                             <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS change_cipher_spec,
                               TLS finished,
                              EAP-Payload-TLV[
                              EAP-Request/Identity])

      // TLS channel established
         (messages sent within the TLS channel)

      // First EAP Payload TLV is piggybacked to the TLS Finished as
         Application Data and protected by the TLS tunnel.

      EAP-Payload-TLV
      [EAP-Response/Identity] ->

                            <- EAP-Payload-TLV
                            [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X] ->

                             <- EAP-Payload-TLV
                            [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X]->

                              <- Intermediate Result TLV (Success),
                               Crypto-Binding TLV (Request),
                               Vendor-Specific TLV,

      // Vendor-Specific TLV exchange started after successful
         completion of previous method X.  The Intermediate-Result
         and Crypto-Binding TLVs are sent with Vendor-Specific TLV
         in next packet to minimize round trips.

      // Compound MAC calculated using keys generated from
         EAP method X and the TLS tunnel.

      Intermediate Result TLV (Success),
      Crypto-Binding TLV (Response),
      Vendor-Specific TLV ->

          // Optional additional Vendor-Specific TLV exchanges...

                             <- Vendor-Specific TLV

      Vendor-Specific TLV ->
                             <- Result TLV (Success)

      Result-TLV (Success) ->

      // TLS channel torn down (messages sent in cleartext)

                              <- EAP-Success

### C.9.  Peer Requests Inner Method after Server Sends Result TLV

   In the case where the peer is authenticated during Phase 1 and the
   server sends back a Result TLV but the peer wants to request another
   inner method, the conversation will appear as follows:

      Authenticating Peer    Authenticator
      -------------------    -------------
                             <- EAP-Request/Identity
      EAP-Response/
      Identity (MyID1) ->

      // Identity sent in the clear.  May be a hint to help route
         the authentication request to EAP server, instead of the
         full user identity.

                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TEAP Start, S bit set, Authority-ID)
      EAP-Response/
      EAP-Type=TEAP, V=1
      (TLS client_hello)->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS server_hello,
                               TLS certificate,
                              [TLS server_key_exchange,]
                              [TLS certificate_request,]
                               TLS server_hello_done)

      EAP-Response/
      EAP-Type=TEAP, V=1
      [TLS certificate,]
       TLS client_key_exchange,
      [TLS certificate_verify,]
       TLS change_cipher_spec,
       TLS finished ->
                              <- EAP-Request/
                              EAP-Type=TEAP, V=1
                              (TLS change_cipher_spec,
                               TLS finished,
                               Crypto-Binding TLV (Request),
                                Result TLV (Success))

      // TLS channel established
         (TLV Payload messages sent within the TLS channel)

       Crypto-Binding TLV(Response),
       Request-Action TLV
       (Status=Failure, Action=Negotiate-EAP)->

                            <- EAP-Payload-TLV
                                [EAP-Request/Identity]

      EAP-Payload-TLV
      [EAP-Response/Identity] ->

                            <- EAP-Payload-TLV
                            [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X] ->

                             <- EAP-Payload-TLV
                            [EAP-Request/EAP-Type=X]

      EAP-Payload-TLV
      [EAP-Response/EAP-Type=X]->

                              <- Intermediate Result TLV (Success),
                                 Crypto-Binding TLV (Request),
                                 Result TLV (Success)

      Intermediate Result TLV (Success),
      Crypto-Binding TLV (Response),
      Result-TLV (Success)) ->

      // TLS channel torn down
      (messages sent in cleartext)

                              <- EAP-Success

### C.10.  Channel Binding

   The following exchanges show a successful TEAP authentication with
   basic password authentication and channel binding using a Request-
   Action TLV.  The conversation will appear as follows:

       Authenticating Peer     Authenticator
       -------------------     -------------
                               <- EAP-Request/
                               Identity
       EAP-Response/
       Identity (MyID1) ->

                               <- EAP-Request/
                               EAP-Type=TEAP, V=1
                               (TEAP Start, S bit set, Authority-ID)

       EAP-Response/
       EAP-Type=TEAP, V=1
       (TLS client_hello with
        PAC-Opaque in SessionTicket extension)->

                               <- EAP-Request/
                               EAP-Type=TEAP, V=1
                               (TLS server_hello,
                               (TLS change_cipher_spec,
                                TLS finished)

       EAP-Response/
       EAP-Type=TEAP, V=1 ->
       (TLS change_cipher_spec,
        TLS finished)

       TLS channel established
       (messages sent within the TLS channel)

                              <- Basic-Password-Auth-Req TLV, Challenge

       Basic-Password-Auth-Resp TLV, Response with both
       username and password) ->

       optional additional exchanges (new pin mode,
       password change, etc.) ...

                            <- Crypto-Binding TLV (Request),
                                Result TLV (Success),

       Crypto-Binding TLV(Response),
       Request-Action TLV
       (Status=Failure, Action=Process-TLV,
       TLV=Channel-Binding TLV)->

                                <- Channel-Binding TLV (Response),
                                Result TLV (Success),

       Result-TLV (Success) ->

       TLS channel torn down
       (messages sent in cleartext)

                               <- EAP-Success
