### 4.2.9.  Request-Action TLV

   The Request-Action TLV MAY be sent by both the peer and the server in
   response to a successful or failed Result TLV.  It allows the peer or
   server to request the other side to negotiate additional EAP methods
   or process TLVs specified in the response packet.  The receiving side
   MUST process this TLV.  The processing for the TLV is as follows:

      The receiving entity MAY choose to process any of the TLVs that
      are included in the message.

      If the receiving entity chooses NOT to process any TLV in the
      list, then it sends back a Result TLV with the same code in the
      Status field of the Request-Action TLV.

      If multiple Request-Action TLVs are in the request, the session
      can continue if any of the TLVs in any Request-Action TLV are
      processed.

      If multiple Request-Action TLVs are in the request and none of
      them is processed, then the most fatal status should be used in
      the Result TLV returned.  If a status code in the Request-Action
      TLV is not understood by the receiving entity, then it should be
      treated as a fatal error.

      After processing the TLVs or EAP method in the request, another
      round of Result TLV exchange would occur to synchronize the final
      status on both sides.

   The peer or the server MAY send multiple Request-Action TLVs to the
   other side.  Two Request-Action TLVs MUST NOT occur in the same TEAP
   packet if they have the same Status value.  The order of processing
   multiple Request-Action TLVs is implementation dependent.  If the
   receiving side processes the optional (non-fatal) items first, it is
   possible that the fatal items will disappear at a later time.  If the
   receiving side processes the fatal items first, the communication
   time will be shorter.

   The peer or the server MAY return a new set of Request-Action TLVs
   after one or more of the requested items has been processed and the
   other side has signaled it wants to end the EAP conversation.

   The Request-Action TLV is defined as follows:

     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |M|R|         TLV Type          |            Length             |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |     Status   |      Action    |                TLVs....
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+--+-+-+-+-+-+-+-+-+-+-+-+-

   M

      Mandatory, set to one (1)

   R

      Reserved, set to zero (0)

   TLV Type

      8 - Request-Action TLV

   Length

      2 + cumulative length of all included TLVs

   Status

      The Status field is one octet.  This indicates the result if the
      party who receives this TLV does not process the action.  Values
      include:

      1  Success

      2  Failure

   Action

      The Action field is one octet.  Values include:

      1  Process-TLV

      2  Negotiate-EAP

   TLVs

      This field is of indefinite length.  It contains TLVs that the
      peer wants the server to process.
