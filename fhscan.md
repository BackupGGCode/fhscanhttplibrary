# Introduction #


FHScan is a Cross-Platform and small C++ HTTP api developed by <a href='http://www.tarasco.org/security'>Andres Tarasco</a> with the main idea of helping HTTP applications, initialy, an HTTP vulnerability scanner.


# Details #
The initial project grow faster so it was splited into Fhscan scanner and Fhscan HTTP library.

The main core was coded in "C" but due to the complexity of maintaining all the code, most of it was ported to C++, allowing faster development and the execution of multiple instances (like multiple HTTP proxies).

Only external openssl libraries are required to achieve full functionality


# Current features: #
Currently the following features are supported by fhscan library v1.3:
  * **Authentication**: Basic, digest and NTLM.
  * **Cookies**: Manual and automatic cookie handling, using internal btree.
  * **Redirection**: The library is currently able to handle HTTP redirects (301, 302 and 303)
  * **HTTP Proxy**: The API allows to create multiple HTTP proxy instances without adding new code.
  * **HTTPS Proxy**: By design, FHScan API allows the interception of SSL traffic.
  * **Bandwidth**: The used bandwidth among other HTTP limits can be stablished.
  * **Transfer Encoding**: Automatic chunk encoding and decoding.
  * **Content Encoding**: Supports gzip and deflate.
  * **Persistent connections**: HTTP/1.0 and HTTP/1.1 support with persistent connections.
  * **Protocols**: Supports both HTTP and HTTPS protocols.
  * **Multithreading**: Fully thread safe api.
  * **Callbacks**: Integrated with an internal callback system that allows flexible data manipulation by third part plugins.