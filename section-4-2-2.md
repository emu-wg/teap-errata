4.2.2.  Authority-ID TLV

     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |M|R|         TLV Type          |            Length             |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                              ID...
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

   M

      0 (Optional)

   R

      Reserved, set to zero (0)

   TLV Type

      1 - Authority-ID

   Length

      The Length field is two octets and contains the length of the ID
      field in octets.

   ID

      Hint of the identity of the server to help the peer to match the
      credentials available for the server.  It should be unique across
      the deployment.
