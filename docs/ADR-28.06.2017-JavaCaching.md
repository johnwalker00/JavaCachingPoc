
*Reference: ADR-28.06.2017-JavaCaching*

# Title: Implement Java Caching with **Apache Commons JCS**  
 This document outlines the decision to use **Apache commons JCS 2.1 (Java Caching System)** for caching system params,r codes and other static data which is not expected to change very often. 
 
 This is not for storing "stateful" information which may change frequently or user session specific information.

## Status: *ACCEPTED*
Gurinder S., Simon O., Dhiren M., John W.

## Context: 
Currently, in RPE there is no formal caching framework for Java, but there is for C#/.NET code. There are various instance of using tread safe static hashmaps for caching some data. There is no mechanism to control the size of those caches or to invalidate and remove the not frequently used objects. 

## Decision: 
Use **Apache Commons JCS (Java Caching System)** in a singleton framework wrapper for caching.

Other caching frameworks considered Guava (Google), EHCache, Redis and JCache.

JCS was chosen because it was easy to integrate(just a JAR) and required no new deployment runtime processes. Being an Apache.org project, it has a large community and history of support and supportability.

## Consequences: 
1. Standardized usage of cache across the code base.
2. Configurable, high performance and robust cache.
3. More feature and benefits at this [link](https://commons.apache.org/proper/commons-jcs/).
4. Caching available in Java.
5. More memory usage in the Java applications is likely due to caching implementation.
6. Caching configuration may need optimization for Performance tuning.
7. Need to look out for "stale" caches in reviews/tests.

## POC: Github Java POC
 * [https://github.com/johnwalker00/JavaCachingPoc](https://github.com/johnwalker00/JavaCachingPoc)

## Questions: Dev/Arch Review
 * What about abstracting the cache so we can replace the implementation with a different provider?
   * Yes, the recommended design would wrap the JCS and use the Provider pattern or a static reference
   ```
   Caching.put(String key, object value);
   value = Caching.get(String key);
   ```
 * Can we pre-load some items at Web Server startup?
   * That's not inherent in the Framework, but could be added as a ServletMonitor to do this work to preload the cache(s).
 * What about caching the proxies?
   * No, Not recommended. This doesn't solve either pooling nor user specificity
 * What about flushing the cache programmatically?
   * Let's look into that.

## Tags:
Java, Cache, Apache, JCS, Architecture Decision Record, ADR