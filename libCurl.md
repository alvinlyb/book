LibCurl基本框架
===
[libcurl](http://curl.haxx.se/)是一个跨平台的网络协议库，支持http, https, ftp, gopher, telnet, dict, file, 和ldap 协议。libcurl同样支持HTTPS证书授权，HTTP POST, HTTP PUT, FTP 上传, HTTP基本表单上传，代理，cookies,和用户认证。想了解更多相关介绍可以登录官网https://curl.haxx.se/。
在基于LibCurl的程序里，主要采用callback function （回调函数）的形式完成传输任务，用户在启动传输前设置好各类参数和回调函数，当满足条件时libcurl将调用用户的回调函数实现特定功能。下面是利用libcurl完成传输任务的流程：

LibCurl主要函数
===
### **curl_global_init(long flags)**
描述：这个函数只能用一次。(其实在调用curl_global_cleanup 函数后仍然可再用)
如果这个函数在curl_easy_init函数调用时还没调用，它讲由libcurl库自动完成。
参数：flags
* CURL_GLOBAL_ALL：初始化所有的可能的调用。
* CURL_GLOBAL_SSL ： 如果libcurl在编译时被设定支持SSL，那么该参数用于初始化相应的SSL库。初始化支持 安全套接字层。
* CURL_GLOBAL_WIN32：初始化win32套接字winsock库，只能应用于windows平台，如果没有正确初始化，应用程序就不能使用socket.
* CURL_GLOBAL_NOTHING：没有额外的初始化。

### **curl _global_cleanup(void)**
描述：在结束libcurl使用的时候，用来对curl_global_init做的工作清理。类似于close的函数。

### **curl_easy_init()**
描述：curl_easy_init用来初始化一个CURL的指针(有些像返回FILE类型的指针一样). 相应的在调用结束时要用curl_easy_cleanup一般curl_easy_init意味着一个会话的开始. 它的返回值一般都用在easy系列的函数中.函数清理.

### **curl_easy_cleanup()**
描述：在结束libcurl使用的时候，用来对curl_global_init做的工作清理。类似于close的函数。释放内存。

### **curl_easy_setopt(CURL \*handle,CURLoption option,parameter)**
描述： 这个函数非常重要.几乎所有的curl 程序都要频繁的使用它.它告诉curl库.程序将有如何的行为. 比如要查看一个网页的html代码等.(这个函数有些像ioctl函数)参数:
* CURL \*handle类型的指针，是由curl_easy_init()返回的句柄。
* CURLoption 类型的选项:


* parameter 这个参数既可以是函数的指针，或者是一个curl_off_t类型，也可以是某个对象的指针，也可以是个long型的变量。它用什么这取决于第二个参数.CURLoption这个参数的取值很多，具体可以查看man手册。


### **curl_easy_perform(CURL \*handle)**
描述：这个函数在初始化CURL类型的指针 以及curl_easy_setopt完成后调用. 就像字面的意思所说perform就像是个舞台.让我们设置的option 运作起来.它将连接到远程站点，执行必要的命令并接收传输，每当它接收数据，它调用我们以前设置的回调函数，该函数可以一次获得一个字节或许多字节。传输完成后函数返回一个参数:CURL类型的指针.
该函数完成curl_easy_setopt指定的所有选项，本节重点介绍curl_easy_perform的返回值。返回0意味一切ok，非0代表错误发生。主要错误码说明：
1. CURLE_OK
  任务完成一切完好
2. CURLE_UNSUPPORTED_PROTOCOL
  不支持的协议，由URL的头部指定
3. CURLE_COULDNT_CONNECT
  不能连接到remote主机或者代理
4. CURLE_REMOTE_ACCESS_DENIED
  访问初拒绝
5. CURLE_HTTP_RETURNED_ERROR
  http返回错误
  6.CURLE_READ_ERROR
  读本地文件错误
  7.CURLE_REMOTE_FILE_NOT_FOUND
  访问文件没有找到文件不存在

### **curl_easy_getinfo()**

### **curl_version**

```c++
curl_version()
```

描述：打印当前curl库的版本。

### **curl_getdate()**

### **curl_formadd()**

### **curl_formfree()**

### **curl_slist_append()**



### **slist_free_all()**

### **curl_easy_escape()**

### **curl_easy_unescape()**
***
curl_easy_setopt中的CURLoption宏解释
===
## *BEHAVIOR OPTIONS*
***
### CURLOPT_VERBOSE
在使用该选项且第 3 个参数为 1 时，curl 库会显示详细的操作信息。这对程序的调试具有极大的帮助。

### CURLOPT_HEADER
使用该选项时，第 3 个参数设置为 1，那么会通知 curl 库在输出时要同时包含 "头部“ 和 "主题内容" 两个部分。该选项仅是对那些同时包含了”头部“和”主题内容“这两部分的协议而言(如 HTTP)。

### CURLOPT_NOPROGRESS
该选项关闭下载进度，为使用CURLOPT_PROGRESSFUNCTION被调用，此参数必须被设置为false;
### CURLOPT_NOSIGNAL
屏蔽其它信号
### CURLOPT_WILDCARDMATCH

启用目录通配符传输

## *CALLBACK OPTIONS*
***
### CURLOPT_WRITEFUNCTION
使用该选项时，要求第 3 个参数中的回调函数必须是下面的函数原型：
```c
size_t function( char *ptr, size_t size, size_t nmemb, void *stream);
```
在启动会话时，一旦检测到有需要接收的数据时，回调函数被调用。ptr 所指向的数据大小由 size 和 nmemb 的乘积获得。函数最后需要返回接收数据的大小。如果不使用该函数，那么接收到的数据会直接打印到终端；使用该函数，那么接收到的数据保存在 ptr 所执向的区域，可以利用此来保存接收下来的数据。
如果你没有通过CURLOPT_WRITEFUNCTION属性给easy handle设置回调函数，libcurl会提供一个默认的回调函数，它只是简单的将接收到的数据打印到标准输出。你也可以通过CURLOPT_WRITEDATA属性给默认回调函数传递一个已经打开的文件指针，用于将数据输出到文件里。

### CURLOPT_WRITEDATA
使用该选项时，第 3 个参数作为用户数据的指针而传递到使用  CURLOPT_WRITEFUNCTION 选项时指定的回调函数中(第 4 个参数)。如果不想用回调函数而保存数据，那么可以使用 CURLOPT_WRITEDATA 选项，使用该选项时，函数的第 3 个参数必须是个 FILE 指针，函数会将接收到的数据自动的写到这个 FILE 指针所指向的文件流中。

### CURLOPT_READFUNCTION
需要读取数据传递给远程主机时将调用CURLOPT_READFUNCTION指定的函数，函数原型:
```c
size_t function( char *ptr, size_t size, size_t nmemb, void *stream);
```
在 CURLOPT_READFUNCTION 的回调函数里，第 1 个参数 ptr 指针用来接收从第 4 个参数传递过来的数据(这个参数往往是个文件流指针)，而这个参数是使用 CURLOPT_READDATA 选项时传递过来的。

### CURLOPT_READDATA
表明CURLOPT_READFUNCTION函数原型中的stream指针来源。

### CURLOPT_IOCTLFUNCTION
### CURLOPT_IOCTLDATA
### CURLOPT_SEEKFUNCTION
### CURLOPT_SEEKDATA
### CURLOPT_SOCKOPTFUNCTION
### CURLOPT_SOCKOPTDATA
### CURLOPT_OPENSOCKETFUNCTION
### CURLOPT_OPENSOCKETDATA
### CURLOPT_CLOSESOCKETFUNCTION
### CURLOPT_CLOSESOCKETDATA
### CURLOPT_PROGRESSFUNCTION
指定的函数正常情况下每秒被libcurl调用一次
### CURLOPT_PROGRESSDATA
批定的参数将作为CURLOPT_PROGRESSFUNCTION指定函数的参数，整个处理与下载数据回调用处理相同。
### CURLOPT_XFERINFOFUNCTION
上传或下载的字节数，使用回调函数和CURLOPT_PROGRESSFUNCTION功能一样。是在新版中加入。回调函数原型：
```c
int progress_callback(void *p,curl_off_t dltotal,curl_off_t dlnow,curl_off_t ultotal,curl_off_t ulnow)
```
dltotal需要下载的总字节数，dlnow是已经下载的字节数。ultotal是将要上传的字节数，ulnow是已经上传的字节数。如果仅仅是下载或上传其中一个将会是0。p是用户自定义参数，通过设置CURLOPT_XFERINFODATA属性来传递。此函数返回非0值将会中断传输。错误代码是CURLE_ABORTED_BY_CALLBACK.
### CURLOPT_XFERINFODATA
### CURLOPT_HEADERFUNCTION
回调函数原型：
```c
size_t function(void *ptr,size_t size,size_t nmemb,void *stream);
```
该选项与上面第 3 个选项 CURLOPT_WRITEFUNCTION 类似，只要它一接收到头部信息时，它就会执行回调函数。需要注意的是，回调函数里处理的头部包含了所有收到的响应的头部信息，而不只是最后一次的响应。如果需要处理其中的一个头部，那么自己需要在所收集的头部信息中进行区分。

### CURLOPT_HEADERDATA
和CURLOPT_WRITEDATA 选项功能一样，表示在接收到头部信息并调用回调函数时，给回调函数传递第 4 个参数。
该指针表明CURLOPT_HEADERFUNCTION函数的stream指针来源。

### CURLOPT_DEBUGFUNCTION
### CURLOPT_DEBUGDATA
### CURLOPT_SSL_CTX_FUNCTION
### CURLOPT_SSL_CTX_DATA
### CURLOPT_CONV_TO_NETWORK_FUNCTION
### CURLOPT_CONV_FROM_NETWORK_FUNCTION
### CURLOPT_CONV_FROM_UTF8_FUNCTION
### CURLOPT_INTERLEAVEFUNCTION
### CURLOPT_INTERLEAVEDATA
### CURLOPT_CHUNK_BGN_FUNCTION
### CURLOPT_CHUNK_END_FUNCTION
### CURLOPT_CHUNK_DATA
### CURLOPT_FNMATCH_FUNCTION
### CURLOPT_FNMATCH_DATA
### CURLOPT_SUPPRESS_CONNECT_HEADERS

## *ERROR OPTIONS*
***
### CURLOPT_ERRORBUFFER
### CURLOPT_STDERR
### CURLOPT_FAILONERROR
### CURLOPT_KEEP_SENDING_ON_ERROR

## *NETWORK OPTIONS*
***
### CURLOPT_URL
这个选项后面接实际要处理的 URL ，这个 URL 是一个以 '\0' 结尾的字符串或参数指针。如果 URL 参数不写上协议头(如 		  "http://" 或者 "ftp:// 等等 )，那么函数会自己进行猜解所给的主机上用的是哪一种服务协议。假如你给的这个地址是一个不被 支持的协议，那么在其后执行 curl_easy_perform()函数或 curl_multi_perform() 函数时， libcurl 将返回错误(CURLE_UNSUPPORTED_PROTOCOL)。这个选项是唯一一个必须在 curl_easy_perform() 调用之前就要设置的选项。
```c
curl_easy_setopt(handle,CURLOPT_URL,"http://domain.com")
```
URL标识了你要在此处访问的远程资源。

### CURLOPT_PATH_AS_IS
### CURLOPT_PROTOCOLS
### CURLOPT_REDIR_PROTOCOLS
### CURLOPT_DEFAULT_PROTOCOL
### CURLOPT_PROXY
###  CURLOPT_PRE_PROXY
### CURLOPT_PROXYPORT
### CURLOPT_PROXYTYPE
### CURLOPT_NOPROXY
### CURLOPT_HTTPPROXYTUNNEL
### CURLOPT_CONNECT_TO
### CURLOPT_SOCKS5_AUTH
### CURLOPT_SOCKS5_GSSAPI_SERVICE
### CURLOPT_SOCKS5_GSSAPI_NEC
### CURLOPT_PROXY_SERVICE_NAME
### CURLOPT_SERVICE_NAME
### CURLOPT_INTERFACE
### CURLOPT_LOCALPORT
### CURLOPT_LOCALPORTRANGE
### CURLOPT_DNS_CACHE_TIMEOUT
### CURLOPT_DNS_USE_GLOBAL_CACHE
### CURLOPT_BUFFERSIZE
### CURLOPT_PORT
### CURLOPT_TCP_FASTOPEN
### CURLOPT_TCP_NODELAY
### CURLOPT_ADDRESS_SCOPE
### CURLOPT_TCP_KEEPALIVE
### CURLOPT_TCP_KEEPIDLE
### CURLOPT_TCP_KEEPINTVL
### CURLOPT_UNIX_SOCKET_PATH
### CURLOPT_ABSTRACT_UNIX_SOCKET

## *NAMES and PASSWORDS OPTIONS (Authentication)*
***
### CURLOPT_NETRC
### CURLOPT_NETRC_FILE
### CURLOPT_USERPWD
### CURLOPT_PROXYUSERPWD
### CURLOPT_USERNAME
### CURLOPT_PASSWORD
### CURLOPT_LOGIN_OPTIONS
### CURLOPT_PROXYUSERNAME
### CURLOPT_PROXYPASSWORD
### CURLOPT_HTTPAUTH
### CURLOPT_TLSAUTH_USERNAME
### CURLOPT_PROXY_TLSAUTH_USERNAME
### CURLOPT_TLSAUTH_PASSWORD
### CURLOPT_PROXY_TLSAUTH_PASSWORD
### CURLOPT_TLSAUTH_TYPE
### CURLOPT_PROXY_TLSAUTH_TYPE
### CURLOPT_PROXYAUTH
### CURLOPT_SASL_IR
### CURLOPT_XOAUTH2_BEARER

## *HTTP OPTIONS*
***
### CURLOPT_AUTOREFERER
### CURLOPT_ACCEPT_ENCODING
### CURLOPT_TRANSFER_ENCODING
### CURLOPT_FOLLOWLOCATION
### CURLOPT_UNRESTRICTED_AUTH
### CURLOPT_MAXREDIRS
### CURLOPT_POSTREDIR
### CURLOPT_PUT
### CURLOPT_POST
### CURLOPT_POSTFIELDS
### CURLOPT_POSTFIELDSIZE
该选项要求第 3 个参数 parameter 是一个 void * 指针，它指向一个向 HTTP 服务器 POST 出去的一段数据，这段数据要根据服务器的具体要求填写。
### CURLOPT_POSTFIELDSIZE_LARGE
### CURLOPT_COPYPOSTFIELDS
### CURLOPT_HTTPPOST
### CURLOPT_REFERER
### CURLOPT_USERAGENT
该选项要求传递一个以 '\0' 结尾的字符串指针，这个字符串用来在向服务器请求时发送 HTTP 头部中的 User-Agent 信息，有些服务器是需要检测这个信息的，如果没有设置 User-Agent，那么服务器拒绝请求。设置后，可以骗过服务器对此的检查。
### CURLOPT_HTTPHEADER
### CURLOPT_HEADEROPT
### CURLOPT_PROXYHEADER
### CURLOPT_HTTP200ALIASES
### CURLOPT_COOKIE
### CURLOPT_COOKIEFILE
### CURLOPT_COOKIEJAR
### CURLOPT_COOKIESESSION
### CURLOPT_COOKIELIST
### CURLOPT_HTTPGET
### CURLOPT_REQUEST_TARGET
### CURLOPT_HTTP_VERSION
### CURLOPT_IGNORE_CONTENT_LENGTH
### CURLOPT_HTTP_CONTENT_DECODING
### CURLOPT_HTTP_TRANSFER_DECODING
### CURLOPT_EXPECT_100_TIMEOUT_MS
### CURLOPT_PIPEWAIT
### CURLOPT_STREAM_DEPENDS
### CURLOPT_STREAM_DEPENDS_E
### CURLOPT_STREAM_WEIGHT

## *SMTP OPTIONS*
***
### CURLOPT_MAIL_FROM
### CURLOPT_MAIL_RCPT
### CURLOPT_MAIL_AUTH

## *TFTP OPTIONS*
***
### CURLOPT_TFTP_BLKSIZE
### CURLOPT_TFTP_NO_OPTIONS

## *FTP OPTIONS*
***
### CURLOPT_FTPPORT

### CURLOPT_QUOTE
给 FTP 或 SFTP 传递命令。这些命令应该放在 struct slist 链表中存储，使用时需要用 curl_slist_append() 函数将这些命令打包起来，然后一起发送出去。

### CURLOPT_POSTQUOTE
和CURLOPT_QUOTE功能类似，它们的共同点都是给 FTP 或 SFTP 传递命令。这些命令应该放在 struct slist 链表中存储，使用时需要用 curl_slist_append() 函数将这些命令打包起来，然后一起发送出去。
它们的不同点是：CURLOPT_QUOTE 选项要求命令要在 FTP 传输请求之前就要发送到库，而 CURLOPT_POSTQUOTE 则可以在 FTP 传输请求发送完后发送。

### CURLOPT_PREQUOTE

### CURLOPT_APPEND
设置为1的参数告诉curl追加到远程文件，而不是覆盖它，这只有在上传到FTP站点时才有用。
### CURLOPT_FTP_USE_EPRT

### CURLOPT_FTP_USE_EPSV

### CURLOPT_FTP_USE_PRET

### CURLOPT_FTP_CREATE_MISSING_DIRS

如果ftp服务器上不存在对应路径的目录就创建对应目录

### CURLOPT_FTP_RESPONSE_TIMEOUT

### CURLOPT_FTP_ALTERNATIVE_TO_USER

### CURLOPT_FTP_SKIP_PASV_IP

### CURLOPT_FTPSSLAUTH

### CURLOPT_FTP_SSL_CCC

### CURLOPT_FTP_ACCOUNT

### CURLOPT_FTP_FILEMETHOD

## *RTSP OPTIONS*
***
### CURLOPT_RTSP_REQUEST

### CURLOPT_RTSP_SESSION_ID

### CURLOPT_RTSP_STREAM_URI

### CURLOPT_RTSP_TRANSPORT

### CURLOPT_RTSP_CLIENT_CSEQ

### CURLOPT_RTSP_SERVER_CSEQ

## *PROTOCOL OPTIONS*
***
### CURLOPT_TRANSFERTEXT
指定libcurl的FTP传输模式，后面的值为1，则为ASCII,0为二进制Binary,如果使用了错误模式，在下载过程中服务器会给你一个553

### CURLOPT_PROXY_TRANSFER_MODE

### CURLOPT_CRLF

### CURLOPT_RANGE
断点续传，指定传输分片，格式"0-200"
### CURLOPT_RESUME_FROM

### CURLOPT_RESUME_FROM_LARGE

### CURLOPT_CUSTOMREQUEST

### CURLOPT_FILETIME

返回文件的修改时间。

### CURLOPT_DIRLISTONLY
该选项列出服务器上的不是目录的文件列表。和list命令一样。设置为1时有效。

### CURLOPT_NOBODY
使用该选项时，若第 3 个参数设为 1，这样在输出中就不会包含主体内容部分。这仅是对在传输的所有内容中，含有“头部”和“主题内容”两部分的协议而言。如 HTTP(S) 服务器，在这种情况下将会使 libcurl 库仅发出一个头部请求。

### CURLOPT_INFILESIZE
当向服务器上传文件时，该选项用来告诉 curl 库期望上传的文件的大小。使用该选项时，应该给函数第 3 个参数的应该是个 long 型变量。如果用的是 SCP 传输，那么该选项强制使用 CURLOPT_INFILESIZE_LARGE 。

### CURLOPT_INFILESIZE_LARGE
该选项和 CURLOPT_INFILESIZE 功能一样，但是它要求函数的第 3 个参数必须是个 curl_off_t 类型。curl_off_t 为 int64_t 类型，而 int64_t 定义在 stdint.h 中：
if __WORDSIZE == 64 

typedef long int                int64_t;

\# else 

__extension__ 

typedef long long int           int64_t;
由上面定义知，一般的，如果是 32 位平台，它就是 long long 型，表示 64 位的；如果是 64 位平台，它就是 long 型，也为 64 位。总之，就是个 64 位的。

### CURLOPT_UPLOAD
在使用该选项时，第 3 个参数设置为 1，表示要准备上传文件。这个参数往往会配合 CURLOPT_READDATA，CURLOPT_INFILESIZE_LARGE，以及 CURLOPT_INFILESIZE 这几个选项一起用。如果是使用 HTTP 协议，那么使用 PUT 的方法进行上传，除非另有指定。

### CURLOPT_MIMEPOST

### CURLOPT_MAXFILESIZE

### CURLOPT_MAXFILESIZE_LARGE

### CURLOPT_TIMECONDITION

### CURLOPT_TIMEVALUE


## *CONNECTION OPTIONS*
***
### CURLOPT_TIMEOUT

### CURLOPT_TIMEOUT_MS


### CURLOPT_LOW_SPEED_LIMIT


### CURLOPT_LOW_SPEED_TIME


### CURLOPT_MAX_SEND_SPEED_LARGE


### CURLOPT_MAX_RECV_SPEED_LARGE


### CURLOPT_MAXCONNECTS


### CURLOPT_FRESH_CONNECT


### CURLOPT_FORBID_REUSE


### CURLOPT_CONNECTTIMEOUT


### CURLOPT_CONNECTTIMEOUT_MS


### CURLOPT_IPRESOLVE


### CURLOPT_CONNECT_ONLY


### CURLOPT_USE_SSL


### CURLOPT_RESOLVE


### CURLOPT_DNS_INTERFACE


### CURLOPT_DNS_LOCAL_IP4


### CURLOPT_DNS_LOCAL_IP6


### CURLOPT_DNS_SERVERS


### CURLOPT_ACCEPTTIMEOUT_MS


## *SSL and SECURITY OPTIONS*
***
### CURLOPT_SSLCERT

### CURLOPT_PROXY_SSLCERT

### CURLOPT_SSLCERTTYPE

### CURLOPT_PROXY_SSLCERTTYPE

### CURLOPT_SSLKEY

### CURLOPT_PROXY_SSLKEY

### CURLOPT_SSLKEYTYPE


### CURLOPT_PROXY_SSLKEYTYPE


### CURLOPT_KEYPASSWD


### CURLOPT_PROXY_KEYPASSWD


### CURLOPT_SSL_ENABLE_ALPN



### CURLOPT_SSL_ENABLE_NPN



### CURLOPT_SSLENGINE



### CURLOPT_SSLENGINE_DEFAULT



### CURLOPT_SSL_FALSESTART



### CURLOPT_SSLVERSION



### CURLOPT_PROXY_SSLVERSION



### CURLOPT_SSL_VERIFYHOST


### CURLOPT_PROXY_SSL_VERIFYHOST


### CURLOPT_SSL_VERIFYPEER



### CURLOPT_PROXY_SSL_VERIFYPEER



### CURLOPT_SSL_VERIFYSTATUS


### CURLOPT_CAINFO


### CURLOPT_PROXY_CAINFO

### CURLOPT_ISSUERCERT


### CURLOPT_CAPATH


### CURLOPT_PROXY_CAPATH


### CURLOPT_CRLFILE

### CURLOPT_PROXY_CRLFILE

### CURLOPT_CERTINFO

### CURLOPT_PINNEDPUBLICKEY


### CURLOPT_PROXY_PINNEDPUBLICKEY

### CURLOPT_RANDOM_FILE


### CURLOPT_EGDSOCKET


### CURLOPT_SSL_CIPHER_LIST

### CURLOPT_PROXY_SSL_CIPHER_LIST

### CURLOPT_SSL_SESSIONID_CACHE

### CURLOPT_SSL_OPTIONS

### CURLOPT_PROXY_SSL_OPTIONS

### CURLOPT_KRBLEVEL

### CURLOPT_GSSAPI_DELEGATION


## *SSH OPTIONS*
***
### CURLOPT_SSH_AUTH_TYPES

### CURLOPT_SSH_COMPRESSION

### CURLOPT_SSH_HOST_PUBLIC_KEY_MD5

### CURLOPT_SSH_PUBLIC_KEYFILE

### CURLOPT_SSH_PRIVATE_KEYFILE

### CURLOPT_SSH_KNOWNHOSTS

### CURLOPT_SSH_KEYFUNCTION

### CURLOPT_SSH_KEYDATA

## *OTHER OPTIONS*
***
### CURLOPT_PRIVATE


### CURLOPT_SHARE



### CURLOPT_NEW_FILE_PERMS


### CURLOPT_NEW_DIRECTORY_PERMS


## *TELNET OPTIONS*
***
### CURLOPT_TELNETOPTIONS



## *RETURN VALUE*
***
[CURLE_OK](https://curl.haxx.se/libcurl/c/libcurl-errors.html#CURLEOK) (zero) means that the option was set properly, non-zero means an error occurred as <curl/curl.h>defines. See the [libcurl-errors](https://curl.haxx.se/libcurl/c/libcurl-errors.html) man page for the full list with descriptions.

If you try to set an option that libcurl doesn't know about, perhaps because the library is too old to support it or the option was removed in a recent version, this function will return [CURLE_UNKNOWN_OPTION](https://curl.haxx.se/libcurl/c/libcurl-errors.html#CURLEUNKNOWNOPTION). If support for the option was disabled at compile-time, it will return [CURLE_NOT_BUILT_IN](https://curl.haxx.se/libcurl/c/libcurl-errors.html#CURLENOTBUILTIN).

## EXAMPLE

CURL *curl = curl_easy_init();
if(curl) {
  CURLcode res;
  curl_easy_setopt(curl, CURLOPT_URL, "http://example.com");
  res = curl_easy_perform(curl);
  curl_easy_cleanup(curl);
}

## SEE ALSO

 CURL FTP命令
 ===

#### RNFR

```c++
#define UPLOAD_FILE_AS "caipan_yeti02.mb";
static const char buf_1 [] = "RNFR " UPLOAD_FILE_AS;
slist = curl_slist_append(slist, buf_1);
slist = curl_slist_append(slist, buf_2);
curl_easy_setopt(curl, CURLOPT_POSTQUOTE, slist);
curl_slist_free_all(slist); //释放卷轴
```

RNFR为重命令的文件名。

#### RNTO

```c++
#define RENAME_FILE_TO "sc03/hello.mb";
static const char buf_2 [] = "RNTO " RENAME_FILE_TO;
```

RNTO为重命名的目标文件名。可以在名字前加上文件夹路径。

#### MKD

```c++
slist = curl_slist_append(slist, "MKD ");
strcat(slist,"sc01");    //连接成命令
curl_easy_setopt(curl, CURLOPT_POSTQUOTE, slist);
curl_slist_free_all(slist); //释放卷轴
```

MKD为在ftp服务器创建文件夹。