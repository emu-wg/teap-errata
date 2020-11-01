### 3.3.2.  Optional Password Authentication

   The use of EAP-FAST-GTC as defined in RFC 5421 [RFC5421] is NOT
   RECOMMENDED with TEAPv1 because EAP-FAST-GTC is not compliant with
   EAP-GTC defined in [RFC3748].  Implementations should instead make
   use of the password authentication TLVs defined in this
   specification.  The authentication server initiates password
   authentication by sending a Basic-Password-Auth-Req TLV defined in
   Section 4.2.14.  If the peer wishes to participate in password
   authentication, then it responds with a Basic-Password-Auth-Resp TLV
   as defined in Section 4.2.15 that contains the username and password.
   If it does not wish to perform password authentication, then it
   responds with a NAK TLV indicating the rejection of the Basic-
   Password-Auth-Req TLV.  Upon receiving the response, the server MUST
   indicate the success or failure of the exchange using an
   Intermediate-Result TLV.  Multiple round trips of password
   authentication requests and responses MAY be used to support some
   "housecleaning" functions such as a password or pin change before a
   user is authenticated.
