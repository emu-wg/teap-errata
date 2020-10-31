## 4.2.13.  Crypto-Binding TLV

   The Crypto-Binding TLV is used to prove that both the peer and server
   participated in the tunnel establishment and sequence of
   authentications.  It also provides verification of the TEAP type,
   version negotiated, and Outer TLVs exchanged before the TLS tunnel
   establishment.

   The Crypto-Binding TLV MUST be exchanged and verified before the
   final Result TLV exchange, regardless of whether there is an inner
   EAP method authentication or not.  It MUST be included with the
   Intermediate-Result TLV to perform cryptographic binding after each
   successful EAP method in a sequence of EAP methods, before proceeding
   with another inner EAP method.  The Crypto-Binding TLV is valid only
   if the following checks pass:

   o  The Crypto-Binding TLV version is supported.

   o  The MAC verifies correctly.

   o  The received version in the Crypto-Binding TLV matches the version
      sent by the receiver during the EAP version negotiation.

   o  The subtype is set to the correct value.

   If any of the above checks fails, then the TLV is invalid.  An
   invalid Crypto-Binding TLV is a fatal error and is handled as
   described in Section 3.6.3

   The Crypto-Binding TLV is defined as follows:

     0                   1                   2                   3 
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |M|R|         TLV Type          |            Length             |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |    Reserved   |    Version    |  Received Ver.| Flags|Sub-Type|
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                                                               |
    ~                             Nonce                             ~
    |                                                               |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                                                               |
    ~                   EMSK Compound MAC                           ~
    |                                                               |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                                                               |
    ~                    MSK Compound MAC                           ~
    |                                                               |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

   M

      Mandatory, set to one (1)

   R

      Reserved, set to zero (0)

   TLV Type

      12 - Crypto-Binding TLV

   Length       
   
      36 plus the length of included MAC fields

   Reserved

      Reserved, set to zero (0)

   Version

      The Version field is a single octet, which is set to the version
      of Crypto-Binding TLV the TEAP method is using.  For an
      implementation compliant with this version of TEAP, the version
      number MUST be set to one (1).

   Received Ver

      The Received Ver field is a single octet and MUST be set to the
      TEAP version number received during version negotiation.  Note
      that this field only provides protection against downgrade
      attacks, where a version of EAP requiring support for this TLV is
      required on both sides.

   Flags

      The Flags field is four bits.  Defined values include

      1  EMSK Compound MAC is present

      2  MSK Compound MAC is present

      3  Both EMSK and MSK Compound MAC are present

   Sub-Type

      The Sub-Type field is four bits.  Defined values include

      0  Binding Request

      1  Binding Response

   Nonce

      The Nonce field is 32 octets.  It contains a 256-bit nonce that is
      temporally unique, used for Compound MAC key derivation at each
      end.  The nonce in a request MUST have its least significant bit
      set to zero (0), and the nonce in a response MUST have the same
      value as the request nonce except the least significant bit MUST
      be set to one (1).

EMSK Compound MAC

      The computation of the MAC is described in Section 5.3.  This can be
      the Server MAC (B1_MAC) or the Client MAC (B2_MAC).  

MSK Compound MAC

      The computation of the MAC is described in Section 5.3.  This can be
      the Server MAC (B1_MAC) or the Client MAC (B2_MAC).  
