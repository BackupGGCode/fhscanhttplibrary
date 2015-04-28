# Introduction #

List of features that should be added in the future. If you want to enhance fhscan by adding more features or by providing me access to different develop platforms, please contact me at atarasco-gmail.com

# Details #

HTTP Core features:
  * Support both local and remote dns resolution when requesting webpages against proxy.
  * Support local Proxy authentication (By adding a local userlist, or local or domain users)
  * Support ipcheck for local proxy.
  * Enhance de bandwidth limit option.
  * Review current C++ classes. There are a lot of things to improve.
  * Allow a real "CONNECT" method for the HTTP Proxy module (not just for man in the middle interception ).
  * Improve the HTTP Proxy module speed.
  * Migrate the base64/md4/md5 functions to a new "encoders" module, and call only openssl functions. Add other hashing algorithms

HTTP Scanner features:
  * Add an scripting language (like SWIG + LUA) for executing more powerful scripts.
  * Add more device signatures. Thats our goal :)
  * Finish the file/directory bruteforce module.
  * Win32 gui (under development)
  * Linux gui (i´ll need help on this). A TCL/TK frontend parsing csv messages from fhscan stdout should be enough

Both:
  * Compatibility checks on different OS. Currently tested under
    * WinXP SP3 x86, Windows 2003 x64( Visual studio 2005, Codegear Rad studio 2008 )
    * Debian lenny, backtrack 4 and ubuntu 9.10 ( g++ 4.3.2 - 4.4.1 )
  * Add MacOSX/BSD compatibility.
  * Add windows mobile support (and create an small frontend).