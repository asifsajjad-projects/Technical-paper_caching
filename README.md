
# Technical report 


### Topic: Caching 
 By: Asif Sajjad 

## * Executive summary

The project was going through some performance and scaling issues. There might be many reasons for it and after analysis by the team lead, it was suggested to use == better caching approaches ==. This report is created in order to investigate different caching approaches. Various caching methods and their importance are recorded in this report to help the team deploy them efficiently. The performance and scalability of the app is expected to increase significantly after deployment of these techniques. Apart from that, the data cost is also expected to reduce for the end user. 

## * Index

### Table of contents

| Sl.No. | Title |
|------- | ----- |
| 1 | == Introduction == |
| 1.1 | CDN Server cache |
| 1.2 | DNS Server cache |
| 1.3 | Web browser cache |
| 2 | == HTTP caching == |
| 3 | ==Methodology== |
| 3.1 | Cache controlling |
| 3.1.1 | Cache-Control header |
| 3.1.2 | The Pragma header |
| 3.1.3 | ETag |
| 4 | ==Suggestions== |
| 5 | ==Conclusion and further improvements== |
| 6 | ==References== |



##1. Introduction

Basically caching is a process of storing copies of files locally in a cache or temporary files at location where the fetching of these data generally becomes faster than to fetch from original source. As per wikipedia "~In computing, a cache is a hardware or software component that stores data so that future requests for that data can be served faster; the data stored in a cache might be the result of an earlier computation or a copy of data stored elsewhere. A cache hit occurs when the requested data can be found in a cache, while a cache miss occurs when it cannot~". Caching is used in many aspects of computer technology viz. processor cache, cache in RAM, cache for programs, web cache etc. Here in this report we are going to discuss cache in web technology. Web browsers in our devices cache HTML files, images , JavaScript and fonts in order to load pages more quickly.

Examples of hardware caches are:
* CPU cache
* GPU cache

Now, Since we are interested here in web cache. There are basically three types of web caches:
1. CDN Server cache
2. DNS Server cache
3. Web Browser cache

###1.1 CDN Server cache

A content delivery network (CDN) refers to a geographically distributed group of servers which work together to provide fast delivery of Internet content. A CDN server cache basically copies the content of the website to one or more servers at various locations and automatically provides the requested web content from the nearest server to the requested location. In addition to increasing loading speed of websites, the CDNs also helps save bandwith to the origin server by distributing its load. CDNs can also be helpful in maintaining uptime of certain sites when some unplanned outage happens to the origin server or in case of certain DDoS attacks.

###1.2 DNS server cache

The Domain Name Server (DNS) is like the phonebook of internet. It is responsible to convert human readable web address to its ip address so that browsers can load internet resources. A cache of DNS happens at different stages:
* Browser DNS Caching : Here, more frequently visited sites DNS are cached in the browser itself for faster resolving.
* OS DNS caching : Operating systems also frequently makes cache of DNS internally. The process inside your operating system that is designed to handle this query is commonly called a “stub resolver” or DNS client. 

###1.3 Web browser cache

When we visit any website, the web contents including HTML, JS, images etc. are downloaded from the server to the browser. Suppose every time we visit the same website and if the same things are downloaded everytime, the wesite will be much slower. To mitigate this, our web browser stores the downloaded content in its storage for a specific period of time determined by its TTL (Time To Live). In this report, further optimization of web browser caching is presented.

##2. HTTP caching

HTTP caching is used to make web pages more responsive, scalable by caching large websites and allowing only small amount of data to flow between host and server. This approach also saves server bandwidth significantly and result in more stable websites.

### There are two ways to perform this cache
1. ==Shared proxy cache== : Local ISPs often make a copy of content of the websites then setup a web proxy so that other hosts in its network can access it. This reduces network traffic and latency.

2. ==Private Browser cache== : Our web browsers often make a copy of the content of web-site when we first visit them. This helps us in navigating forward and backward easily without consuming additional data. 

The following image sourced from MDN docs helps to illustrate the above ways in a better way:

![Image showing 2 ways of caches](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching/http_cache_type.png) 

##3. Methodology

As a developer, caching can be controled by introducing certain tags in our HTML file which defines how a web browser will perform cache and check for the freshness of content. The contents which are naturally static such as font type, company logo, basic javascripts etc. are assigned very long TTLs and thus they are prevented from server traffic whenever the page reloads. In this way only certain dynamic data and contents are transferred over network which results in less bandwidth consumption from both server and host side. Since Most of the web site is already cached at user place, the interrupted or failed network request will not significantly reduce user experience on the site.

HTTP cache responses are typically added to ~GET~ request method and the following status codes are returned by the server:
* Successful results of a retrieval request: a ~200 (OK)~ response to a GET request containing a resource like HTML documents, images or files.
* Permanent redirects: a ~301 (Moved Permanently)~ response.
* Error responses: a ~404 (Not Found)~ result page.
* Incomplete results: a ~206 (Partial Content)~ response.

###3.1 Cache controlling

There are several ways by which we can implement control over cache. They are as follows:

* The ~Cache-Control~ header 
* The ~Pragma~ header
* Etag

###3.1.1 Cache-Control header

The Cache-Control header is a general header, that specifies the caching policies of server responses as well as client requests. Basically, it gives information about the manner in which a particular resource is cached, location of the cached resource, and its maximum age attained before getting expired i.e. time to live.

The syntax for Cache-Control header is as follows:

>> ~Cache-Control: <directive1>, <directive2>, ... ~

The following are directives explained in brief (*sourced from geeks for geeks*) :

1. ==Public==: This directive indicates that the response can be stored by any cache without any restriction. In case the response is non-cacheable, it can still be cached.

2. ==Private==:It indicates that only browser cache is eligible to store the response.

3. ==no-cache==: It indicates that the response can be stored by any cache without any restriction even if it is non-cacheable. The condition that needs to be satisfied here is that the stored response must be validated by the origin server before being used.

4. ==no-store==: It indicates that the response cannot be stored by any cache.

5. ==max-age=<seconds>==: It indicates the maximum amount of time a resource remains fresh and can be requested to be accessed.

6. ==s-maxage=<seconds>==: This directive is mainly used for shared cache by content delivery networks(CDNs). It overrides the max-age directive and expires header when present.

7. ==max-stale[=<seconds>]==: It indicates that only a stale response is accepted by the client. The optional time in seconds indicate the maximum limit of staleness which will be accepted by the client.

8. ==min-fresh=<seconds>==: It indicates that only the response which is fresh is accepted by the client for the specified duration of time specified in seconds.

9. ==stale-while-revalidate=<seconds>==: It indicates that the stale response will be accepted by the client along with asynchronously looking for fresh responses. The time in seconds represents the duration for which the client will accept stale response

10. ==stale-if-error=<seconds>==: It indicates that the stale response will be accepted by the client if the check for fresh one fails. The time in seconds represents the duration for which the client will accept a stale response after initial expiration.

11. ==must-revalidate==: It indicates caches cannot use their stale resources after they have become stale without getting it validated from the origin server.

12. ==proxy-revalidate==: This directive is similar to must-revalidate. However, it only works for shared caches and is ignored by the private caches.

13. ==Immutable==: It indicates that the response body remains unchanged over the period of time.

14. ==no-transform==: It indicates that the resources cannot be transformed or modified into another form.

15. ==only-if-cached==: It indicates that the network cannot be used for the response by the client. It means that the cache can use either stored response or 504 status code.
 
 Some examples of the cache-control are :
 
  ~Cache-Control: no-store~
  ~Cache-Control: no-cache, max-age=0, stale-while-revalidate=300~
  
###3.1.2 The Pragma header

Pragma is an HTTP/1.0 header. ~Pragma: no-cache~ is like ~Cache-Control: no-cache~ in that it forces caches to submit requests to the origin server for validation before releasing a cached copy.
Pragma headers are only honored by few browsers and are ignored by many hence they are not effective way to control cache. It should only be used for backwards compatibility with HTTP/1.0 caches where the Cache-Control HTTP/1.1 header is not yet present.


###3.1.3 ETag

The ETag (or entity tag) HTTP response header is an identifier for a specific version of a resource. It lets caches be more efficient and save bandwidth, as a web server does not need to resend a full response if the content was not changed. Additionally, etags help to prevent simultaneous updates of a resource from overwriting each other ("mid-air collisions"). If the resource at a given URL changes, a new Etag value must be generated. ETag value can be a hash of the content, a hash of the last modification timestamp, or just a revision number.

Syntax of Etag are:
~ETag: W/"<etag_value>"~
~ETag: "<etag_value>"~

>> Here W/ is used to represent that a weak validator is used.
  
* we can avoid simaltaneous editing to a resource by assigning ETag to that resource and using ~If-Match: "<Etag >"~ in the ~POST~ request of the edit. In this way we can avoid mid air collision.

* It can also be used to validate unchanged old resources. If a user visits a given URL again (that has an ETag set), and it is stale (too old to be considered usable), the client will send the value of its ETag along in an If-None-Match header field:

~If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"~

The server compares the client's ETag (sent with If-None-Match) with the ETag for its current version of the resource, and if both values match (that is, the resource has not changed), the server sends back a 304 Not Modified status, without a body, which tells the client that the cached version of the response is still good to use (fresh). 


##4. Suggestions

After studying various caching approaches and ways of implementation, a certain best practice is deduced. It is advised to implement those to develop a better performing and scalable web application or site. Some of the best practices are listed below:

1. Build a site which is more cache aware. Listed below are some of the methods which can help to achieve this.

> ==Use URLs consistently== : When updating contents avoid changing their URLs to make the site more cache friendly.
> ==Use a common library/directory for images==
> ==Make caches store images and pages that don’t change often by using a ~Cache-Control: max-age~ header with a large value.==
> ==If a resource (especially a downloadable file) changes, change its name.== This will ensure that browser will load them again irrespective of their long cache age.
> ==Use cookies only where necessary as cookies are difficult to cache==
> ==Don’t change files unnecessarily. If you do, everything will have a falsely young ~Last-Modified date~.== When updating your site, don’t copy over the entire site. Just move the files that you’ve changed.

2. Write cache friendly scripts. The best way to make a script cache-friendly is to dump its content to a plain file whenever it changes. 

3. Avoid embedding user-specific information in the URL unless very necessary.

4. Set a low TTL and use private cache flag in case of HTML files. Or use ~Cache-Control: no-cache~ on them.

5. Use immutable for contents which will not change. eg. ~Cache-Control: max-age=31536000, immutable~ .

##5. Conclusion and further improvements

During a new project, many performance improvements and scalability issues can be solved by proper use of caching. If implemented properly cache can shift load between host and server and the amount of data exchanged between host and server can be reduced considerably and the stability of website/webapp can be ensured even in the times of network request failure. Some of the items that remained for further improvements and discusions are Varying responses, Last-Modifed, Server load balancing, CDN server caching etc.

##6. References

1. ==MDN Docs:== https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching
2. ==keyCDN:== https://www.keycdn.com/blog/http-cache-headers
3. ==Cloudflare:= https://www.cloudflare.com/learning/cdn/what-is-caching/
4. == Wikipedia:== https://en.wikipedia.org/wiki/Cache_(computing)
5. ==Geeks for Geeks:== https://www.geeksforgeeks.org/http-headers-cache-control/



