---
title: Bots and Indexing on Pantheon
description: Information on managing bots and indexing while avoiding performance degradation on your Pantheon WordPress or Drupal site.
categories: [developing]
tags: [code, platform]
keywords: bot, bots, index, indexing, indexes, bots and indexing, bots and indexes, duplicate request, rapid fire, rapid fire requests, bot error, bots converging on error pages, bots on erroring pages
---
Bots are part of every public-facing website's lifecycle. We wouldn't be able to find a thing on the internet without them! Bots perform the hard work taken for granted when browsing the multitudes of indexed search results from any given search engine. In the wrong hands, bots can become nagging nuisances slowing down or even taking down your site.

## Bots in My Logs: Real World Scenarios and Identifiers

Bots don't browse like humans. Analyzing access patterns in the Nginx log is one of the quickest ways to determine the presence of bots.

## Rapid Fire Requests/Duplicates

In the log snippet below, there are multiple requests coming in for the same path in rapid fire succession. The time stamp reflects 5 identical requests at the same millisecond. You should investigate these requests.

    127.0.0.1 - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 0.848 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"
    unix: - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 1.059 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"
    127.0.0.1 - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 1.059 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"
    unix: - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 1.271 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"
    127.0.0.1 - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 1.271 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"
    unix:\xC8\xFB\x7F - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 1.481 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"
    127.0.0.1 - - [11/Nov/2013:19:05:24 +0000] "POST /index.php?q=comment/reply/545 HTTP/1.0" 500 588 "http://www.mywebsite.com/node/545?page=399" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; MRA 4.3 (build 51720))" 1.482 "195.200.54.200, 10.200.200.21, ::ffff:127.0.0.1,::ffff:127.0.0.1"

## Bots Converging on Erroring Pages

Some legitimate [bots/crawlers/proxies](http://www.httpuseragent.org/list/) (such as BingBot or AdsBotGoogle) will identify themselves. Since search-indexing is desirable for most sites, tread carefully in order to avoid wreaking havoc on a site's SEO. That said, there may be instances in which crawlers/spiders converge on a page that is erroring out ( [502s](/docs/errors-and-server-responses) in the example below). These repetitive requests can increase the pageload issues by putting more load on the server. Investigate these errors immediately. When the error has been fixed, the bots/crawlers will no longer be hung-up on the give path.

    127.0.0.1 - - [26/Jul/2013:15:27:38 +0000] "GET /index.php?q=shop/kits/shebang-kit HTTP/1.0" 502 166 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" 14.188 "157.56.93.49, 10.183.252.21, 127.0.0.1,127.0.0.1"
    unix: - - [26/Jul/2013:15:27:38 +0000] "GET /index.php?q=shop/kits/shebang-kit HTTP/1.0" 502 166 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" 14.476 "157.56.93.49, 10.183.252.21, 127.0.0.1,127.0.0.1"
    127.0.0.1 - - [26/Jul/2013:15:27:38 +0000] "GET /index.php?q=shop/kits/shebang-kit HTTP/1.0" 502 166 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" 14.477 "157.56.93.49, 10.183.252.21, 127.0.0.1,127.0.0.1"
    127.0.0.1 - - [26/Jul/2013:15:26:37 +0000] "GET /index.php?q=gush/content/name-pimp-november-2008&page=17 HTTP/1.0" 502 166 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" 14.722 "157.56.93.49, 10.183.252.21, 127.0.0.1,127.0.0.1"
    10.208.128.192 - - [26/Jul/2013:15:26:37 +0000] "GET /gush/content/name-pimp-november-2008?page=17 HTTP/1.1" 502 166 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" 14.999 "157.56.93.49, 10.183.252.21, 127.0.0.1,127.0.0.1"
    unix: - - [26/Jul/2013:15:26:37 +0000] "GET /index.php?q=gush/content/name-pimp-november-2008&page=17 HTTP/1.0" 502 166 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" 14.998 "157.56.93.49, 10.183.252.21, 127.0.0.1,127.0.0.1"
     10.208.128.192 - - [26/Jul/2013:15:31:03 +0000] "GET / HTTP/1.1" 500 109 "-" "checks.panopta.com" 0.126 "5.63.145.72, 10.183.252.21, 127.0.0.1,127.0.0.1"

## Robots.txt: Indexing Your Pantheon Site

It is important to note that each of your site environments have a robots.txt associated with the bare `.pantheonsite.io` domain, or `sites.myagency.com` custom Vanity domain, that contains the following:

```
User-agent: *
Disallow: /
```
The pantheonsite.io domains are ONLY intended for development use and cannot be used for production. Robots.txt is only visible on Live with a domain, and is not available on Dev or Test. If you're testing links or SEO prior to launch, a workaround is to assign a test or beta domain to the Live environment and test your links following the alternative domain. In addition, if you run SEO toolsets locally, you can utilize a /etc/hosts file entry on your local development box to spoof your production domain on Pantheon.

You can index your site under your production domain. There are many contrib module options available for creating sitemaps for Drupal, including [XMLSiteMap](https://drupal.org/project/xmlsitemap) and [Site_Map](https://drupal.org/project/site_map). WordPress users can install the [Google XML Sitemaps](http://wpcrux.com/collectives/wordpress-xml-sitemap-plugins/) plugin or the [Yoast SEO](https://wordpress.org/plugins/wordpress-seo/), which will maintain sitemap updates automatically. It is up to you to configure the extensions to work as you desire. Pantheon does not offer support for Drupal modules or WordPress plugins.

Sitemap.xml is only visible on Live with a custom domain. The following code snippet will redirect any bots trying index using the sitemap of a Pantheon domain and redirect it to your custom domain. Please replace the URLs to reflect your domain and place this in `settings.php`.

```php
// Redirect to force indexing with custom domain sitemap
$subject = $_SERVER['HTTP_HOST'];
$pattern = '#/pantheonsite.io/sitemap.xml$/#';
preg_match($pattern, $subject, $matches);
$counted = count($matches);
if (($counted > 0 ) && (php_sapi_name() != "cli")) {
    header('HTTP/1.0 301 Moved Permanently');
    header('Location: http://yoursite.com/sitemap.xml'. $_SERVER['REQUEST_URI']);
    exit();
}
```

### Troubleshooting

#### Sitemaps Produce a White Screen of Death (WSOD)
Some modules or plugins are configured by default to fetch all URLs at once during sitemap generation which can result in a blank white page (WSOD) due to exceeding PHP's memory limit. To resolve this issue, adjust the plugin or module configuration so that URLs are fetched individually instead of all at once.

For example, if you have a Drupal site using the [XMLSiteMap](https://drupal.org/project/xmlsitemap) module, navigate to `admin/config/search/xmlsitemap/settings` and uncheck **Prefetch URL aliases during sitemap generation**. Save the configuration and clear caches for the Live environment on the Pantheon Dashboard or via [Terminus](/docs/terminus): `terminus site clear-cache`

Props to [Will Hall](https://twitter.com/HN_Will) for highlighting this solution in a related [blog post](http://www.willhallonline.co.uk/blog/get-xml-sitemaps-working-pantheon).

#### Legacy Sitemap Submissions Generating 404s
Sitemaps can (and should) be submitted directly to Google Webmaster Tools. However, if there are legacy submissions out there generating 404s, you'll need to redirect via PHP within `wp-config.php` or `settings.php`. For example, WordPress sites running the [Yoast SEO](https://wordpress.org/plugins/wordpress-seo/) plugin can use the following:

```php
// 301 Redirect from /sitemap.xml to /sitemap_index.xml
if (($_SERVER['REQUEST_URI'] == '/sitemap.xml') &&
  (php_sapi_name() != "cli")) {
  header('HTTP/1.0 301 Moved Permanently');
  header('Location: /sitemap_index.xml');
  exit();
}
```

For more examples of redirecting via PHP, see [Redirect Incoming Requests](/docs/redirects).
