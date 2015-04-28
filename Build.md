# Linux #

Required optional packages:

  * zlib 1.2.3 and zlib headers (packages zlib1g, zlib1g-dev)
  * Openssl (tested with OpenSSL 0.9.8g)


Building library:
```
 * g++  -DLINUX  -D_OPENSSL_SUPPORT_ -D_ZLIB_SUPPORT_  -c -fPIC HTTPCoreClass/*.cpp HTTPCoreClass/Authentication/*.cpp HTTPCoreClass/Modules/*.cpp
 * g++ -shared -o HTTPCore13.so -fPIC *.o
 * cp HTTPCore13.so /usr/lib/libHTTPCore13.so
```
Building static package:
```
 * g++  -DLINUX  -D_OPENSSL_SUPPORT_ -D_ZLIB_SUPPORT_  -lpthread -lssl -lz HTTPCoreClass/*.cpp HTTPCoreClass/Authentication/*.cpp HTTPCoreClass/Modules/*.cpp Scanner/*.cpp Scanner/Input/*.cpp Scanner/Reporting/*.cpp -o release/Fhscan
```
Building Dynamic package:
```
 * g++  -lpthread -lssl -lz -DLINUX  -D_OPENSSL_SUPPORT_ -D_ZLIB_SUPPORT_ Scanner/*.cpp Scanner/Input/*.cpp Scanner/Reporting/*.cpp HTTPCore13.so -o release/Fhscan
```


There are three different g++ flags that will add extra support to the application:
  * OPENSSL\_SUPPORT_: If defined, the application will be linked with openssl allowing https requests and HTTPS proxy interception.
  * ZLIB\_SUPPORT_ : If defined, the application will be linked with zlib libraries and therefore deflate and gzip compresion methods will be automatically handled by fhscan.
  * LINUX: Mandatory define to be used when compiling under a platform other than windows.


# Windows #

Currently tested with compilers Visual studio 2005 and Codegear RAD studio 2008.