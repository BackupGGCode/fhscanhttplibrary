Cross-Platform and small C++ HTTP api. This library currently supports:

  * **Authentication**: Basic, digest and NTLM.
  * **Cookies**: Manual and automatic cookie handling, using internal btree.
  * **Redirection**: The library is currently able to handle HTTP redirects (301, 302 and 303)
  * **HTTP Proxy**: The API allows to create multiple HTTP proxy instances without adding new code.
  * **HTTPS Proxy**: By design, the HTTPCore API allows the interception of SSL traffic.
  * **Bandwidth**: The used bandwidth among other HTTP limits can be stablished.
  * **Transfer Encoding**: Automatic chunk encoding and decoding.
  * **Content Encoding**: Supports gzip and deflate.
  * **Persistent connections**: HTTP/1.0 and HTTP/1.1 support with persistent connections.
  * **Protocols**: Supports both HTTP and HTTPS protocols.
  * **Multithreading**: Fully thread safe api.
  * **Callbacks**: Integrated with an internal callback system that allows flexible data manipulation by third part plugins.

There are also some new features we are currently working on:

  * **IPV6**: Ipv6 support will be finished soon.
  * **UNICODE Support**: Working under win32.

Check the <a href='http://code.google.com/p/fhscanhttplibrary/wiki/APIReference'>API reference manual</a> for information about how does Fhscan HTTP API works.