---
title: "IIS compression error response"
date: 2019-03-05T13:53:42+02:00
draft: false
tags: ["IIS", "Orchad", "Compression", "response"]
---

I have been seeing the following error for a few days in the answers given to me by the site I have published with Orchad.

{{< figure src="/images/IIS_compression_error.jpg" title="IIS Compression error" >}}

The error is totally random in terms of frequency and in the pages on which it is given, so it has been a bit difficult to find out what was really happening. As you can see, it seems that the IIS response is returning a series of random characters.

The first inquiry I made is that it only happened when the Orchad cache was enabled, when it was disabled it seems that the error in the response was not reproduced.

The first solution to get out of the way could be to disable the cache, but it is not viable is a viable long-term solution or in places that have a lot of traffic, so by continuing to investigate find out what is happening.

It seems that the response returned by the IIS was being compressed with gZip twice. The reason seems to be that the OutputCache module of Orchad is caching the response that had already been compressed with gZip and when the IIS serves that cached page it is recompressed with gZip again.

We can check if this is our case if refreshing the page that gives us the error is reloaded correctly and the error disappears.

It seems that some Hostings, among them mine, modify the configurations to do the compression before the cache. Then we have to disable it by adding the following directive in the <system.webserver> section of the root web.config of our site.

{{< highlight xml >}}

<system.webServer>
  <urlCompression doDynamicCompression="true" doStaticCompression="true" dynamicCompressionBeforeCache="false" /> 
</system.webServer>

{{< /highlight >}}

The dynamicCompressionBeforeCache attribute is set to false by default, so this bug only happens in the servers where it was changed.

If this does not work, which in my case if it has been used, we would only have the option of setting the doDynamicCompression attribute to false, which would also disable compression of the IIS responses. And as a last step we only need to disable the OutputCache module of Orchad so that our site will not use cache. But as we have seen these solutions are not optimal but if it will not work otherwise is the only thing we could do.

Hope it helps.
