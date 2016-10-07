ZlibDecompress
==============

This class was written from original C++ source code by Mark Adler (http://zlib.net/)

It happens that for some pdf files it is not possibile to extract text stored in compressed zlib format, because gzuncompress() php function fails to decompress it.

I found that the issue is due to the fact that in some cases, in the gzipped pdf stream, the zlib stream header (see RFC 1950) is missing or invalid, and this is the reason why gzuncompress() function cannot decompress data.

Here's my workaround to fix the problem: I wrote a new ZlibDecompress? class in PHP from some original C++ code by Mark Adler taken from the official zlib site, which uses the standard algorithm and always decompress the string correctly, ignoring the zlib stream header, as it contains only CRC data. Obviously this PHP class is much more slower than gzuncompress function, because it is not compiled, so I use it only when gzuncompress fails. For pdf files this happens only in a few cases, so this is a good compromise for me.

Ref. http://bugs.php.net/bug.php?id=39616

#Usage

The `ZlibDecompress.php` file contains the class `ZlibDecompress`, which requires the `HuffmanTable.php` file/class to work. 
To use it, simply call the `inflate()` method:

    $zlib = new ZlibDecompress;
    $inflated = $zlib->inflate(substr($compressed_stream,2));

For pdf files, it can be used in conjunction with `gzuncompress()` function, calling it only when the latter fails to decompress the stream:

    ini_set("memory_limit","-1");
    $inflated = @gzuncompress($stream);
    if (!$inflated) {
        require_once("ZlibDecompress.php");
        $zlib = new ZlibDecompress;
        $inflated = $zlib->inflate(substr($stream, 2));    
    }

Note that when using the `inflate()` method you must cut the first two bytes (i.e. the zlib stream header) from the compressed pdf stream.
