IP Address Anonimization Module (mmanon)
========================================

**Module Name:    mmanon**

**Author:**\ Rainer Gerhards <rgerhards@adiscon.com>

**Available since**: 7.3.7

**Description**:

The mmanon module permits to anonymize IP addresses. It is a message
modification module that actually changes the IP address inside the
message, so after calling mmanon, the original message can no longer be
obtained. Note that anonymization will break digital signatures on the
message, if they exist.

*How are IP-Addresses defined?*

We assume that an IP address consists of four octets in dotted notation,
where each of the octets has a value between 0 and 255, inclusively.

 

**Module Configuration Parameters**:

Currently none.

 

**Action Confguration Parameters**:

-  **mode** - default "rewrite"
   There exists the "simple" and "rewrite" mode. In simple mode, only
   octets as whole can be anonymized and the length of the message is
   never changed. This means that when the last three octets of the
   address 10.1.12.123 are anonymized, the result will be 10.0.00.000.
   This means that the length of the original octets is still visible
   and may be used to draw some privacy-evasive conclusions. This mode
   is slightly faster than "overwrite" mode, and this may matter in high
   throughput environments.
   The default "rewrite" mode will do full anonymization of any number
   of bits and it will also normlize the address, so that no information
   about the original IP address is available. So in the above example,
   10.1.12.123 would be anonymized to 10.0.0.0.
-  **ipv4.bits** - default 16
   This set the number of bits that should be anonymized (bits are from
   the right, so lower bits are anonymized first). This setting permits
   to save network information while still anonymizing user-specific
   data. The more bits you discard, the better the anonymization
   obviously is. The default of 16 bits reflects what German data
   privacy rules consider as being sufficinetly anonymized. We assume,
   this can also be used as a rough but conservative guideline for other
   countries.
   Note: when in simple mode, only bits on a byte boundary can be
   specified. As such, any value other than 8, 16, 24 or 32 is invalid.
   If an invalid value is given, it is rounded to the next byte boundary
   (so we favor stronger anonymization in that case). For example, a bit
   value of 12 will become 16 in simple mode (an error message is also
   emitted).
-  **replacementChar** - default "x"
   In simple mode, this sets the character that the to-be-anonymized
   part of the IP address is to be overwritten with. In rewrite mode,
   this parameter is **not permitted**, as in this case we need not
   necessarily rewrite full octets. As such, the anonymized part is
   always zero-filled and replacementChar is of no use. If it is
   specified, an error message is emitted and the parameter ignored.

**See Also**

-  `Howto anonymize messages that go to specific
   files <http://www.rsyslog.com/howto-anonymize-messages-that-go-to-specific-files/>`_

**Caveats/Known Bugs:**

-  **only IPv4** is supported

**Samples:**

In this snippet, we write one file without anonymization and another one
with the message anonymized. Note that once mmanon has run, access to
the original message is no longer possible (execept if stored in user
variables before anonymization).

module(load="mmanon") action(type="omfile" file="/path/to/non-anon.log")
action(type="mmanon") action(type="omfile" file="/path/to/anon.log")

This next snippet is almost identical to the first one, but here we
anonymize the full IPv4 address. Note that by modifying the number of
bits, you can anonymize different parts of the address. Keep in mind
that in simple mode (used here), the bit values must match IP address
bytes, so for IPv4 only the values 8, 16, 24 and 32 are valid. Also, in
this example the replacement is done via asterisks instead of lower-case
"x"-letters. Also keep in mind that "replacementChar" can only be set in
simple mode.

module(load="mmanon") action(type="omfile" file="/path/to/non-anon.log")
action(type="mmanon" ipv4.bits="32" mode="simple" replacementChar="\*")
action(type="omfile" file="/path/to/anon.log")

The next snippet is also based on the first one, but anonimzes an "odd"
number of bits, 12. The value of 12 is used by some folks as a
compromise between keeping privacy and still permiting to gain some more
in-depth insight from log files. Note that anonymizing 12 bits may be
insufficient to fulfill legal requirements (if such exist).

module(load="mmanon") action(type="omfile" file="/path/to/non-anon.log")
action(type="mmanon" ipv4.bits="12") action(type="omfile"
file="/path/to/anon.log")

This documentation is part of the `rsyslog <http://www.rsyslog.com/>`_
project.
Copyright © 2008-2013 by `Rainer
Gerhards <http://www.gerhards.net/rainer>`_ and
`Adiscon <http://www.adiscon.com/>`_. Released under the GNU GPL version
3 or higher.
