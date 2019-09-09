This package contains the datetime module from Python version 3.8, modified
to make datetime objects timezone-aware by default. It can be used by any
Python 3.x version.

The obvious change is that tzinfo is timezone.utc by default rather than None.
Another readily apparent difference is the default representation:

>>> from datetime3tz import *
>>> datetime(2000, 1, 1)
datetime3.datetime(2000, 1, 1, 0, 0, tzinfo='+00:00')
>>> datetime(2000, 1, 1, tzinfo="+03:00")
datetime3.datetime(2000, 1, 1, 0, 0, tzinfo='+03:00')

The equivalents on the standard library datetime would be:
>>> datetime(2000, 1, 1, tzinfo=timezone.utc)
datetime.datetime(2000, 1, 1, 0, 0, tzinfo=datetime.timezone.utc)
>>> datetime(2000, 1, 1, tzinfo=timezone(timedelta(hours=3)))
datetime.datetime(2000, 1, 1, 0, 0, tzinfo=datetime.timezone(datetime.timedelta(0, 10800)))

The constructor accepts an ISO-formatted timezone offset string and converts
it intenally to a timezone object. Only instances of timezone() without a 
name are represented this way. Any other tzinfo object is shown as usual:

>>> datetime(2000,1,1, tzinfo=dateutil.tz.gettz('America/Jerusalem'))
datetime.datetime(2000, 1, 1, 0, 0, tzinfo=tzfile('/usr/share/zoneinfo/America/Jerusalem'))

A more subtle difference is that posix timestamps (number of seconds since 1970) 
are always UTC. This, IMHO, is a significal design flaw in the original datetime 
module where the utcfromtimestamp() method implicitly treats this number as 
in the local timezone, making it error-prone and confusing. Note how the 
fromtimestamp() and utcfromtimestamp() methods in datetime and datetime3tz
refer to the same times, but one round-trips correctly from timestamp to 
datetime and back while the other does not:

>>> from datetime3tz import *
>>> for dt in datetime.fromtimestamp(999999999), datetime.utcfromtimestamp(999999999):
...     print(dt.timestamp(), dt)
... 
999999999.0 2001-09-09 04:46:39
999999999.0 2001-09-09 01:46:39+00:00
>>> 
>>> from datetime import *
>>> for dt in datetime.fromtimestamp(999999999), datetime.utcfromtimestamp(999999999):
...     print(dt.timestamp(), dt)
... 
999999999.0 2001-09-09 04:46:39
999989199.0 2001-09-09 01:46:39
>>> 
