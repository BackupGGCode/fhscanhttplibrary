# Introduction #

Are you looking for a tiny and small HTTP Proxy for windows and linux? This is probably what you are looking for.


# Details #
Fhscan HTTP Api is really easy to use. You just need two lines of code to create a new HTTP Proxy instance.

```
 HTTPAPI *api = new (HTTPAPI);	
 api->InitHTTPProxy("127.0.0.1","8080");
```

By using **Callbacks** some of your application funtions will be notified each time an event is created. Some basic events are created for example when an http request is sent or when http responses arrive. The callback function will receive an struct that contains all the exchanged information betwen your host and the remote HTTP server.

If the previous HTTP proxy example its too simple and you need something more advanced, for example logging all the HTTP Traffic, the following Callback example is all what you need.

**proxy.cpp ( HTTP Proxy Source code ):**

```
#include "HTTP.h"

int CBLog(int cbType,HTTPAPI *api, HTTPHANDLE HTTPHandle, PHTTP_DATA  request, PHTTP_DATA response);

int main(int argc, char *argv) 
{
 HTTPAPI *api = new (HTTPAPI);	
 api->RegisterHTTPCallBack( CBTYPE_CLIENT_RESPONSE,(HTTP_IO_REQUEST_CALLBACK)CBLog,"HTTP Proxy Logger");
 api->InitHTTPProxy("127.0.0.1","8080");
 printf("[+] Proxy running. Press any key to exit\n\n");

 getchar();
 api->StopHTTPProxy();
}

int CBLog(int cbType,HTTPAPI *api, HTTPHANDLE HTTPHandle, PHTTP_DATA  request, PHTTP_DATA response)
{
 if ( (request) && (response))
 {
   char *url   = request->GetRequestedURL(); /* Extract the request url */
   char status = response->GetStatus();    /* Get the returned HTTP response code */
   char *method= request->GetHTTPMethod(); /* Extract the HTTP verb (HEAD, GET, POST, ..) */
   if (method)
   {
     printf("%-6s %-40s %.4s %3.3i %5.i %s\n",method,api->GetHTTPConfig(HTTPHandle,OPT_HTTP_HOST),api->GetHTTPConfig(HTTPHandle,OPT_HTTP_PORT),status,response->DataSize, url);
     free(method);
   }
   free(data);
 }
 return(CBRET_STATUS_NEXT_CB_CONTINUE);
}

```

Of course you can also modify the data (request and response) or block the request or response by using a return value other than CBRET\_STATUS\_NEXT\_CB\_CONTINUE.

The console version of Fhscan HTTP proxy can be downloaded at the <a href='http://code.google.com/p/fhscanhttplibrary/downloads/list'>Downloads</a> section. Fhscan Scanner includes a flag "--EnableProxy" that will execute a new proxy instance.
Its also available a proxy gui application:

<img src='http://fhscanhttplibrary.googlecode.com/svn/wiki/http_proxy.jpg' alt='Simple HTTP Proxy for Windows'>

Download and test our <a href='http://fhscanhttplibrary.googlecode.com/files/HTTP_Proxy.zip'>simple HTTP proxy for windows </a>

<h1>SSL Certificates</h1>
HTTP Proxy:<br>
<hr />

AS the the HTTP Proxy also intercepts HTTPS requests, there are three additional files needed to work:<br>
<ul><li>server certificate<br>
</li><li>root CA certificate<br>
</li><li>dh file.<br>
If you want to build your own certificate, just download <a href='http://www.openssl.org/'>openssl package</a> and type the following commands:</li></ul>

<ul><li>openssl genrsa -des3 -passout pass:password -out server_key.pem 2048<br>
</li><li>openssl req -new -key server_key.pem -out server_request.csr -passin pass:password -config openssl.cfg<br>
</li><li>openssl genrsa -des3 -passout pass:password -out ca_key.pem 2048<br>
</li><li>openssl req -new -key ca_key.pem -x509 -days 3 -out ca_cert.cer -passin pass:password -config openssl.cfg<br>
</li><li>openssl x509 -req -days 3 -in server_request.csr -CA ca_cert.cer -CAkey ca_key.pem -CAcreateserial -out server.cer -passin pass:password<br>
</li><li>@echo Creat .pem file that example wants<br>
</li><li>type server_key.pem server.cer >server.pem<br>
</li><li>@rem type ca_key.pem ca_cert.cer >root.pem<br>
</li><li>copy ca_cert.cer root.pem<br>
</li><li>openssl dhparam -check -text -2 1024 -out dh1024.pem<br>
</li><li>@rem view<br>
</li><li>openssl x509 -in root.pem -noout -text</li></ul>

As an alternative, you can use <a href='http://fhscanhttplibrary.googlecode.com/files/HTTP_Proxy_Certificate.zip'>Fhscan Certificates</a>.