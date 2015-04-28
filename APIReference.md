# Api Documentation: #

FHSCAN HTTP API is C++ library that can be easily embedded into different projects just by adding an include file. IF you need more information about what is currently supported take a look to <a href='http://code.google.com/p/fhscanhttplibrary/wiki/fhscan'>FHScan Wiki</a>

The main HTTP Class is named HTTPAPI. Each instance, once initialized, have their own connection and handle pools so most times you will only need an HTTP instance.

## HTTP API Constructor: ##
Here is an example of a functional program that initializes Fhscan HTTP API. Note that under windows a new HTTPAPI instance will also initialize the wsasockets.

```
#include "HTTP.h"

void main(int argc, char *argv[])
{
 HTTPAPI *api = new HTTPAPI;
 //Do stuff
 delete api;
}
```

## HTTP HANDLES: ##
To continue working with HTTP we first need to learn about Handles. On Fhscan a handle, defined as **HTTPHANDLE** is an structure that contains information about a remote host like:
  * Hostname
  * TCP Port
  * SSL Protocol required (for example for HTTPS)
Of Course there are a lot of additional information stored there but we will look at it later.

An HTTPHANDLE is required for managing HTTP requests and responses and the initialization of the HANDLE is not going to stablish any connection, its just storing and allocating sensitive information into internal structures. Here is en example of how an HTTPHANDLE is allocated.

```
#include "HTTP.h"

void main(int argc, char *argv[])
{
 HTTPAPI *api = new HTTPAPI;

 HTTPHANDLE Handle = api->InitHTTPConnectionHandle("www.google.com",80);
 if (Handle!=INVALID_HHTPHANDLE_VALUE)
 {
   //Do stuff
  api->EndHTTPConnectionHandle(Handle);
 }

 delete api;
}
```

Note that the main HTTPHANDLE allocator is the function  	 `api->InitHTTPConnectionHandle()` and once the work have been finished the HTTPHANDLE must be released with `api->EndHTTPConnectionHandle()`. The maximum number of HTTPHANDLEs that could be allocated by each api instance is 4096 (defined at HTTP.h)

InitiHTTPConnectionHandle() requires two or three parameters (the last one is optional):
  * **Host**: Null Terminated string containing the ip or dns name of the remote host.
  * **Port**: TCP port where the remote webserver is listening (for example 80)
  * **SSL**: (OPTIONAL) If the remote host is running an HTTPS server you must set the value to '1'.

If all goes well, the returned value will be equal or greater than 0, otherwise **INVALID\_HHTPHANDLE\_VALUE** is returned (for example if the remote dns couldnt be resolved or SSL is not available).

## Send HTTP Requests: ##

Once we have our handle we can deal with requests. The function for sending HTTP requests is **SendHttpRequest()**. easy ? :)
There are different function prototypes however we are going to explain just the first one here:
```
PREQUEST   SendHttpRequest(HTTPHANDLE HTTPHandle,HTTPCSTR HTTPMethod,HTTPCSTR lpPath);
PREQUEST   SendHttpRequest(HTTPHANDLE HTTPHandle,HTTPCSTR HTTPMethod,HTTPCSTR lpPath,HTTPCSTR PostData);
PREQUEST   SendHttpRequest(HTTPHANDLE HTTPHandle,HTTPCSTR HTTPMethod,HTTPCSTR lpPath,HTTPCSTR PostData,HTTPCSTR lpUsername,HTTPCSTR lpPassword);
PREQUEST   SendHttpRequest(HTTPHANDLE HTTPHandle,PHTTP_DATA request);
PREQUEST   SendHttpRequest(HTTPHANDLE HTTPHandle,PHTTP_DATA request,HTTPCSTR lpUsername,HTTPCSTR lpPassword);
PREQUEST   SendHttpRequest(HTTPHANDLE HTTPHandle,HTTPCSTR VHost,HTTPCSTR HTTPMethod,HTTPCSTR lpPath,HTTPCSTR PostData,unsigned int PostDataSize,HTTPCSTR lpUsername,HTTPCSTR lpPassword);	
PREQUEST   SendHttpRequest(HTTPCSTR Fullurl);
	
```

Parameters:

  * **HTTPHandle:** HTTP Handle returned by a previous call to `InitiHTTPConnectionHandle()`.
  * **HTTPMethod:** HTTP verb. This value could be "GET","HEAD", "POST", or whatever HTTP method you want
  * **path:** HTTP path of the remote resource like "/" or "/index.html".
  * **Postdata:**   (Optional) data to be sent on a GET,POST, PUT,.. request.
  * **lpUsername:** (Optional) username to be sent if the remote host requires authentication.
  * **lpPassword:** (Optional) username to be sent if the remote host requires authentication.

So, its time for an example:

```
#include "HTTP.h"

void main(int argc, char *argv[])
{
 HTTPAPI *api = new HTTPAPI;

 HTTPHANDLE Handle = api->InitHTTPConnectionHandle("www.google.com",80,0);
 if (Handle!=INVALID_HHTPHANDLE_VALUE)
 {
  api->SendHttpRequest(Handle,"GET","/index.html");
  api->EndHTTPConnectionHandle(Handle);
 }

 delete api;
}
```

The HTTP Api will send something like:
```
GET /index.html HTTP/1.1
Host: www.google.com
User-Agent: Mozilla/5.0 (compatible; MSIE 7.0; FHScan Core 1.3)
Connection: Keep-Alive
Accept-Encoding: gzip, deflate
```

## Interact with HTTP response data: ##

From the above, you can see that each `SendHttpRequest(/*params*/)` function returns an struct named PREQUEST. PREQUEST is where all the information related to the HTTP request is stored. Lets take a look at what its stored inside:

```
typedef struct  prequest {
   HTTPCHAR hostname[256];/*!< hostname of the remote server. This is related to the vhost parameter. If no vhost is specified, hostname contains the ip address. */
   int ip;              /*!< remote HTTP ip address. */
   int port;            /*!< remote HTTP port. This value is obtained from the InitHTTPConnectionHandle() */
   int NeedSSL;         /*!< Boolean value. If this parameter is 1 then the connection is handled by openssl otherwise is just a tcp connection */
   HTTPSTR url;         /*!< path to the file or directory requested */
   HTTPSTR Parameters;  /*!< Request Parameters */
   httpdata*  request;  /*!< Information related to the HTTP Request. This struct contains both client headers and postdata */
   httpdata*  response; /*!< Information related to the HTTP response. This struct contains both server headers and data */
   HTTPSTR server;      /*!< pointer to a string that contains the server banner from the remote http server */
   HTTPCHAR Method[20]; /*!< HTTP Verb used */
   unsigned int status; /*!< status code returned by the HTTP server. Example: "200", for an STATUS OK response. */
   HTTPSTR ContentType; /*!< Response Content-Type */
public:
	prequest();
	~prequest();
	int IsValidHTTPResponse(void);
	int HasResponseHeader(void);
	int HasResponseData(void);
} REQUEST, *PREQUEST;
```

To continue explaining this against our previous example, lets see how to access to the stored data:

```
#include "HTTP.h"

void main(int argc, char *argv[])
{
 HTTPAPI *api = new HTTPAPI;

 HTTPHANDLE Handle = api->InitHTTPConnectionHandle("www.google.com",80,0);
 if (Handle!=INVALID_HHTPHANDLE_VALUE)
 {
  PREQUEST HTTP = api->SendHttpRequest(Handle,"GET","/index.html");
  if (HTTP)
  {
    printf("The remote host is: %s:%i\n",HTTP->hostname,HTTP->port);
    printf("The URL retrieved is: %s\n",HTTP->url);
    printf("The server returned an HTTP error code: %i\n",HTTP->status);
    delete (HTTP);
  }
  
  api->EndHTTPConnectionHandle(Handle);
 }

 delete api;
}
```

The most important part of the PREQUEST struct is the request and response httpdata. Those variables are structs that contains all the important information:
  * Header: Header values
  * HeaderSize:
  * Data: Html code (for responses) or post data (for requests)
  * DataSize: length of the html data

So, in our example,
```

printf("%s\n",http->request->header); 

GET /index.html HTTP/1.1
Host: www.google.com
User-Agent: Mozilla/5.0 (compatible; MSIE 7.0; FHScan Core 1.3)
Connection: Keep-Alive
Accept-Encoding: gzip, deflate

printf("%s\n",http->response->header); 

HTTP/1.1 200 OK
Date: Sun, 01 Nov 2009 21:29:37 GMT
Expires: -1
Cache-Control: private, max-age=0
Content-Type: text/html; charset=ISO-8859-1
Set-Cookie: PREF=ID=abfd76036e627dee:TM=1257110977:LM=1257110977:S=_efNPDrT7RIt_3fD; expires=Tue, 01-Nov-2011 21:29:37 GMT; path=/; domain=.google.es
Set-Cookie: NID=28=lrmYR-vHt1U3Zr9w9Xd1MI0qxr8LY2A3JrfmtsKpE5cyiyqq2i3OAe3Rfr_6mGsSU60JlT9_Bs7E-bCzZxON9jomFaHG1VBQloAJ79xz9enEKlGg24pKy-z7qJ3DUzIi; e
xpires=Mon, 03-May-2010 21:29:37 GMT; path=/; domain=.google.es; HttpOnly
X-Content-Type-Options: nosniff
Server: gws
X-XSS-Protection: 0
Transfer-Encoding: chunked
```

Some method availables:
```
 /* Initialization */
 httpdata();
 httpdata(const char *header);
 httpdata(const char *header, int headersize);
 httpdata(const char *header, const char *lpPostData);
 httpdata(const char *header,unsigned int headersize, const char *lpPostData,unsigned int PostDataSize);
 void InitHTTPData(const char *header,unsigned int headersize, const char *lpPostData,unsigned int PostDataSize);
 void InitHTTPData(const char *header);
 void InitHTTPData(const char *header, int headersize);
 void InitHTTPData(const char *header, const char *lpPostData);


 /* Header manipulation */
 char *GetHeaderValue(const char *value,int n);
 char *GetHeaderValueByID(unsigned int id);
 char *AddHeader(const char *Header);
 char *RemoveHeader(const char *Header);	
```

Lets see some examples:
<b>Example 1:</b> Enumerate all headers.

```
 char *header;
 int i=0;
 do 
 {
  header = HTTP->response->GetHeaderValueByID(i);
  if (header)
  {
   printf("Header %i: %s\n",i,header);
   free(header);
   i++;
  }
 } (header);
```

<b>Example 2:</b> Get information about an specific header value.

```
 char *header = HTTP->response->GetHeaderValue("Content-type",0);
 if (header)
 {
  printf("Value: %s\n",header);
  free(header);
 }
```
<b>Example 3:</b> saving file to disk.

```
#include "HTTP.h"

void main(int argc, char *argv[])
{
 HTTPAPI *api = new HTTPAPI;

 HTTPHANDLE Handle = api->InitHTTPConnectionHandle("fhscanhttplibrary.googlecode.com",80,0);
 if (Handle!=INVALID_HHTPHANDLE_VALUE)
 {
  PREQUEST HTTP = api->SendHttpRequest(Handle,"GET","/files/fhscan-Scanner-1.3.0-i386-Backtrack.tgz");
  if (HTTP)
  {
    if ( (HTTP->status == 200) && (HTTP->response) )
    {
      FILE *file = fopen("release.zip","w");
      fwrite(HTTP->response->Data,HTTP->response->DataSize,1,file);
      fclose(file);
    }
    delete (HTTP);
  }
  
  api->EndHTTPConnectionHandle(Handle);
 }

 delete api;
}
```


**TODO..**: This guide isnt finished yet :)