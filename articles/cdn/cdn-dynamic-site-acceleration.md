---
title: Dynamic site acceleration via Azure CDN
description: Azure CDN supports dynamic site acceleration (DSA) optimization for files with dynamic content.
services: cdn
author: duongau
manager: kumudd
ms.service: azure-cdn
ms.topic: how-to
ms.date: 02/27/2023
ms.author: duau
---
# Dynamic site acceleration via Azure CDN

With the explosion of social media, electronic commerce, and the hyper-personalized web, a rapidly increasing percentage of the content served to end users is generated in real time. Users expect a fast, reliable, and personalized web experience, independent of their browser, location, device, or network. However, the very innovations that make these experiences so engaging also slow page downloads and put the quality of the consumer experience at risk. 

Standard content delivery network (CDN) capability includes the ability to cache files closer to end users to speed up delivery of static files. However, with dynamic web applications, caching that content in edge locations isn't possible because the server generates the content in response to user behavior. Speeding up the delivery of such content is more complex than traditional edge caching and requires an end-to-end solution that finely tunes each element along the entire data path from inception to delivery. With Azure CDN dynamic site acceleration (DSA) optimization, the performance of web pages with dynamic content is measurably improved.

**Azure CDN from Edgio** offers DSA optimization through the **Optimized for** menu during endpoint creation. Dynamic site acceleration from Microsoft is offered via [Azure Front Door Service](../frontdoor/front-door-overview.md).

> [!IMPORTANT]
> For **Azure CDN from Edgio** profiles, you can't change the optimization of a CDN endpoint after it has been created.

## CDN endpoint configuration to accelerate delivery of dynamic files

To configure a CDN endpoint to optimize delivery of dynamic files, you can either use the Azure portal, the REST APIs, or any of the client SDKs to do the same thing programmatically. 

**To configure a CDN endpoint for DSA optimization by using the Azure portal:**

1. In the **CDN profile** page, select **Endpoint**.

   ![Add a new CDN endpoint](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   The **Add an endpoint** pane appears.

2. Under **Optimized for**, select **Dynamic site acceleration**.

    ![Create a new CDN endpoint with DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. For **Probe path**, enter a valid path to a file.

    Probe path is a feature specific to DSA, and a valid path is required for creation. DSA uses a small *probe path* file placed on the origin server to optimize network routing configurations for the CDN. For the probe path file, you can download and upload the sample file to your site, or use an existing asset on your origin that is about 10 KB in size.

4. Enter the other required endpoint options (for more information, see [Create a new CDN endpoint](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), then select **Add**.

   After the CDN endpoint is created, it applies the DSA optimizations for all files that match certain criteria. 

## DSA Optimization using Azure CDN

Dynamic Site Acceleration on Azure CDN speeds up delivery of dynamic assets by using the following techniques:

-	[Route optimization](#route-optimization)
-	[TCP optimizations](#tcp-optimizations)

### Route Optimization

Route optimization is important because the Internet is a dynamic place, where traffic and temporarily outages are constantly changing the network topology. The Border Gateway Protocol (BGP) is the routing protocol of the Internet, but there may be faster routes via intermediary Point of Presence (PoP) servers. 

Route optimization chooses the most optimal path to the origin so that a site is continuously accessible and dynamic content is delivered to end users via the fastest and most reliable route possible. 

The Edgio network uses a combination of Anycast DNS, high capacity support PoPs, and health checks, to determine the best gateways to best route data from the client to the origin.
 
As a result, fully dynamic and transactional content is delivered more quickly and more reliably to end users, even when it's not cacheable. 

### TCP Optimizations

Transmission Control Protocol (TCP) is the standard of the Internet protocol suite used to deliver information between applications on an IP network.  By default, several back-and-forth requests are required to set up a TCP connection, and limits to avoid network congestions, which result in inefficiencies at scale. **Azure CDN from Edgio** handles this problem by optimizing in three areas: 

 - [Eliminating TCP slow start](#eliminating-tcp-slow-start)
 - [Leveraging persistent connections](#leveraging-persistent-connections)

#### Eliminating TCP slow start

TCP *slow start* is an algorithm of the TCP protocol that prevents network congestion by limiting the amount of data sent over the network. It starts off with small congestion window sizes between sender and receiver until the maximum is reached or packet loss is detected.

**Azure CDN from Edgio** profiles eliminate TCP slow start with the following three steps:

1. Health and bandwidth monitoring is used to measure the bandwidth of connections between edge PoP servers.
    
2. Metrics are shared between edge PoP servers so that each server is aware of the network conditions and server health of the other PoPs around them.  
    
3. The CDN edge servers make assumptions about some transmission parameters, such as what the optimal window size should be when communicating with other CDN edge servers in its proximity. This step means that the initial congestion window size can be increased if the health of the connection between the CDN edge servers is capable of higher packet data transfers.  

#### Leveraging persistent connections

When you're using a CDN, fewer unique machines connect to your origin server directly compared with users connecting directly to your origin. Azure CDN also pools user requests together to establish fewer connections with the origin.

As previously mentioned, several handshake requests are required to establish a TCP connection. Persistent connections, which get implemented by the `Keep-Alive` HTTP header, reuse existing TCP connections for multiple HTTP requests to save round-trip times and speed up delivery. 

**Azure CDN from Edgio** also sends periodic keep-alive packets over the TCP connection to prevent an open connection from being closed.

## Caching

With DSA, caching is turned off by default on the CDN, even when the origin includes `Cache-Control` or `Expires` headers in the response. DSA is typically used for dynamic assets that shouldn't be cached because they're unique to each client. Caching can break this behavior.

If you have a website with a mix of static and dynamic assets, it's best to take a hybrid approach to get the best performance. 

With **Azure CDN Standard from Edgio** profiles, you can turn on caching for specific DSA endpoints by using [caching rules](cdn-caching-rules.md).

To access caching rules:

1. From the **CDN profile** page, under settings, select **Caching rules**.  
    
    ![CDN Caching rules button](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    The **Caching rules** page opens.

2. Create a global or custom caching rule to turn on caching for your DSA endpoint. 

For **Azure CDN Premium from Edgio** profiles only, you turn on caching for specific DSA endpoints by using the [rules engine](./cdn-verizon-premium-rules-engine.md). Any rules that are created affect only those endpoints of your profile that are optimized for DSA. 

To access the rules engine:
    
1. From the **CDN profile** page, select **Manage**.  
    
    ![CDN profile manage button](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    The CDN management portal opens.

2. From the CDN management portal, select **ADN**, then select **Rules Engine**. 

    ![Rules engine for DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)

Alternatively, you can use two CDN endpoints: one endpoint optimized with DSA to deliver dynamic assets and another endpoint optimized with a static optimization type, such as general web delivery, to delivery cacheable assets. Modify your webpage URLs to link directly to the asset on the CDN endpoint you plan to use.

For example, `mydynamic.azureedge.net/index.html` is a dynamic page and is loaded from the DSA endpoint.  The HTML page references multiple static assets such as JavaScript libraries or images that are loaded from the static CDN endpoint, such as `mystatic.azureedge.net/banner.jpg` and `mystatic.azureedge.net/scripts.js`.
