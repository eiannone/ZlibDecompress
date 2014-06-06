ZlibDecompress
==============

This class was written from original C++ source code by Mark Adler (http://zlib.net/)

It happens that for some pdf files it is not possibile to extract text stored in compressed zlib format, because gzuncompress() php function fails to decompress it.

I found that the issue is due to the fact that in some cases, in the gzipped pdf stream, the zlib stream header (see RFC 1950) is missing or invalid, and this is the reason why gzuncompress() function cannot decompress data.

Here's my workaround to fix the problem: I wrote a new ZlibDecompress? class in PHP from some original C++ code by Mark Adler taken from the official zlib site, which uses the standard algorithm and always decompress the string correctly, ignoring the zlib stream header, as it contains only CRC data. Obviously this PHP class is much more slower than gzuncompress function, because it is not compiled, so I use it only when gzuncompress fails. For pdf files this happens only in a few cases, so this is a good compromise for me.

Ref. http://bugs.php.net/bug.php?id=39616
