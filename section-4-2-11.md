### 4.2.11.  Intermediate-Result TLV


   The Intermediate-Result TLV provides support for acknowledged 
   intermediate Success and Failure messages between multiple inner 
   EAP methods or basic password authentication.
   An Intermediate-Result TLV indicating success
   MUST be accompanied by a Crypto-Binding TLV.  The optional TLVs
   associated with this TLV are provided for future extensibility to
   provide hints about the current result.  The Intermediate-Result TLV
   is defined as follows:

     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |M|R|         TLV Type          |            Length             |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |             Status            |        TLVs...
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

   M

      Mandatory, set to one (1)

   R

      Reserved, set to zero (0)

   TLV Type

      10 - Intermediate-Result TLV

   Length

      2 + cumulative length of the embedded associated TLVs

   Status

      The Status field is two octets.  Values include:

      1  Success

      2  Failure

   TLVs

      This field is of indeterminate length and contains zero or more of
      the TLVs associated with the Intermediate Result TLV.  The TLVs in
      this field MUST NOT have the mandatory bit set.
