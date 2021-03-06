---
title: PANTHEON_STRIPPED GET Parameter Values
description: Learn how Pantheon's edge cache uses the request URL as the cache key.
category:
  - developing
---
## Overview

Pantheon's edge cache uses the request URL as the cache key. To allow requests for the same site content but distinct Google Analytics parameters to be served from cache, Pantheon replaces a specific subset of GET parameter values with `PANTHEON_STRIPPED` to normalize the cache location.

The `PANTHEON_STRIPPED` behavior is completely compatible with JavaScript-based tracking code, such as Google Analytics. Our recommendation is to have Google Analytics track across both domains with the same code and cookie, which will let you track users as they move from one site to the other.

#### What GET parameters are affected?

Any GET parameter that starts with the following will have its value replaced:

- `utm_`
- `__` (two underscores)

This will prevent PHP from reading the values of the affected parameters, but JavaScript will still be able to read the value from the URL.

## Reading GET Parameters with JavaScript

Most use cases where values need to be read from GET parameters that are affected by the cache-friendly behavior can and should be handled in JavaScript. The following example for reading GET parameters was adapted from an answer found on [stack overflow](http://stackoverflow.com/a/439578):

    function getQueryParams(qs) {
      qs = qs.split("+").join(" ");
      var params = {}, tokens, re = /[?&]?([^=]+)=([^&]*)/g;


      while (tokens = re.exec(qs)) {
        params[decodeURIComponent(tokens[1])] = decodeURIComponent(tokens[2]);
      }


      return params;
    }


    var query_params = getQueryParams(document.location.search);
    console.log(query_params);

## PHP Redirects

If you redirect a request in PHP that contains the replaced values, the URL will contain `PANTHEON_STRIPPED` values. Therefore, if you want to direct traffic to your Pantheon site with using a campaign containing `utm` or similar GET parameters, avoid sending them to a page that redirects in PHP.
