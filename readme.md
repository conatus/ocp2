# Optimus Cache Prime

Optimus Cache Prime (OCP) is a smart cache preloader for websites with XML
sitemaps. It crawls all URLs in a given sitemap so the web server builds
cached versions of the pages before visitors or search engine spiders
arrive.

Since Google began penalizing websites with long response times in their
rankings, serving all of your pages quickly has become more important
than ever. Optimus Cache Prime helps you do that by making sure your
cache — be it an in-memory cache like memcached or APC, or a flat file
cache like WP Super Cache or W3 Total Cache — is primed so random
requests are served lightning fast.

## Download

  Platform          Version            Package                                                                                                                                 Size
  ----------------- ------------------ --------------------------------------------------------------------------------------------------------------------------------------- --------
  Linux (32-bit)    2.4 – 2011-12-22   [ocp-2.4-i386.tar.gz](http://patrickmylund.com/files/tools/ocp2/linux/ocp-2.4-i386.tar.gz "Optimus Cache Prime for Linux (32-bit)")     981 KB
  Linux (64-bit)    2.4 – 2011-12-22   [ocp-2.4-amd64.tar.gz](http://patrickmylund.com/files/tools/ocp2/linux/ocp-2.4-amd64.tar.gz "Optimus Cache Prime for Linux (64-bit)")   962 KB
  Windows           2.4 – 2011-12-22   [ocp-2.4.zip](http://patrickmylund.com/files/tools/ocp2/windows/ocp-2.4.zip "Optimus Cache Prime for Windows")                          882 KB
  Other platforms   1.1 – 2010-12-20   [Legacy Python Version](http://patrickmylund.com/projects/ocp/legacy/ "Optimus Cache Prime - Legacy")                                   
  Git repository    Development        [https://github.com/pmylund/ocp2](https://github.com/pmylund/ocp2 "ocp2 - GitHub")                                                      

## Usage Examples

  Command                                                                                                        Explanation
  -------------------------------------------------------------------------------------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------
  `./ocp /home/patrick/sitemap.xml`                                                                              Prime all URLs in a local sitemap
  `./ocp http://mysite.com/sitemap.xml`                                                                          Prime all URLs in a remote sitemap
  `./ocp -c 10 http://mysite.com/sitemap.xml.gz`                                                                 Prime all URLs in a remote sitemap, priming up to 10 URLs at once
  `./ocp -l /var/www/mysite.com/wp-content/cache/supercache/mysite.com http://mysite.com/sitemap.xml`            Prime each URL in a remote sitemap only if a cached version of the page (<-l path\>/<page name\>/index.html) doesn’t already exist
  `./ocp -l /var/www/mysite.com/wp-content/w3tc/pgcache/ -ls _index.html http://mysite.com/sitemap.xml`          Prime each URL in a remote sitemap only if a cached version of the page (<-l path\>/<page name\>/\_index.html) doesn’t already exist
  `./ocp -l /var/www/mysite.com/wp-content/cache/supercache/mysite.com --max 20 http://mysite.com/sitemap.xml`   Prime each URL in a remote sitemap only if a cached version of the page doesn’t already exist, and stop after priming 20 (uncached) pages
  `./ocp --print http://mysite.com/sitemap.xml | xargs curl -I`                                                  Don’t prime, but run `curl -I` to get the response header from each of the URLs in a sitemap or set of nested sitemaps

Run `./ocp` without any parameters to get an overview and explanation of
all available options.

## Features

-   Cache checking
-   Nested sitemap/sitemap index support
-   Priming an arbitrary number of URLs simultaneously
-   HTTP KeepAlive and session re-use (when applicable)
-   Doubles as a general-purpose (nested) sitemap parser for use with
    external commands (`--print` flag)

Run OCP on any machine specifying a target sitemap — e.g. one that links
to all of your pages, or one that lists only the high-priority pages —
and Optimus primes the links therein.

If run locally on your web server, OCP can probe your static file cache
before making any requests to your web server, reducing the amount of
requests and redundant log messages drastically. Pages are only crawled
if they aren’t already cached.

OCP checks up to 10,000 pages per second with local mode enabled if the
cache is mostly primed from previous runs. It was designed for use with
W3 Total Cache and WP Super Cache for WordPress, but will work with any
system that uses a URL-relative flat file cache (i.e. /about/ is cached
as e.g. ‘about’ or ‘about/index.html’ on the disk.)

## FAQ

**Q:** How do I install and use OCP?\
 **A:** On Windows, download and extract the zip file above, then run
ocp.exe either through a command prompt, or by right-clicking ocp.exe,
making a shortcut, changing the parameters for that shortcut (in
Properties) to e.g.:
`"D:\Apps\ocp\ocp.exe" -c 10 http://mysite.com/sitemap.xml`, and then
running it. On Linux, copy the link for your architecture above, then
run `curl -s <link> | tar xvz`, and you’re good to go. `cd ocp`, and run
e.g. `./ocp -c 10 http://mysite.com/sitemap.xml`.

**Q:** How do I make an XML sitemap?\
 **A:** You can use an online sitemap generator like
[XML-Sitemaps.com](http://www.xml-sitemaps.com/ "XML-Sitemaps.com"). For
WordPress I highly recommend [Arne Brachhold’s XML Sitemap
Generator](http://www.arnebrachhold.de/projects/wordpress-plugins/google-xml-sitemaps-generator/ "Google (XML) Sitemaps Generator for WordPress").
You can also make one manually — here’s [an
example](http://patrickmylund.com/files/tools/ocp2/example-sitemap.xml.txt "Example Sitemap.xml").

**Q:** Do I need to have WordPress, W3 Total Cache, WP Super Cache,
memcached, … to use OCP?\
 **A:** No. All you need is an XML sitemap. To use Local mode you need
something which stores its cached pages with file/directory names that
are relative to the original URLs. (Both W3 Total Cache and WP Super
Cache do just that.)

**Q:** Can you demonstrate how to use Local mode?\
 **A:** You can run `ocp` with parameters like:

-   WordPress with W3 Total Cache:

    `./ocp -l /var/www/patrickmylund.com/wp-content/w3tc/pgcache -ls _index.html /var/www/patrickmylund.com/sitemap.xml`

    Translation: Look for already-cached files in
    /var/www/patrickmylund.com/wp-content/w3tc/pgcache, where the cached
    file for e.g. http://patrickmylund.com/about/ is
    <path\>/about/\_index.html

-   WordPress with WP Super Cache:

    `./ocp -l /var/www/patrickmylund.com/wp-content/cache/supercache/patrickmylund.com /var/www/patrickmylund.com/sitemap.xml`

    Translation: Look for already-cached files in
    /var/www/patrickmylund.com/wp-content/cache/supercache/patrickmylund.com,
    where the cached file for e.g. http://patrickmylund.com/about/ is
    <path\>/about/index.html

**Q:** How do I know if Local mode is working?\
 **A:** You shouldn’t see any requests from “Optimus Cache Prime” in
your web server’s access log, and runs subsequent to the first should
complete in less than a second.

**Q:** How do I preload the cache regularly?\
 **A:** The easiest way is to set up a cron job. On most Linux
distributions you can do this by adding a cron entry using `crontab -e`.
The entry can be e.g.
`*/5 * * * * /home/patrick/ocp http://patrickmylund.com/sitemap.xml`,
which will run OCP every five minutes. For more information, see
Ubuntu’s [Cron
Howto](https://help.ubuntu.com/community/CronHowto "Cron How To").

(Note that Cron’s environment/path is very minimal, and you might need
to use full paths to your commands.)

## Changelog

Version

Changes

2.4 – 2011-12-22

-   New command-line flag, `--max [number]`, which makes OCP exit after
    priming e.g. 5 (uncached) pages
-   It is now apparent from the log shown with `-v` whether a GET
    request is sent to the web server, or if the cached page already
    exists on disk

2.3 - 2011-11-23

-   New command-line flag, `--print` (used exclusively), which causes
    OCP to simply print all of the URLs read from the sitemap (or set of
    nested sitemaps) sorted by priority. This can be used with `xargs`
    to run arbitrary commands on the URLs, e.g.
    `ocp --print sitemap.xml | xargs curl -I`

2.2 - 2011-11-17

-   OCP now reports dead/broken links and pages that can't be loaded
    (e.g. HTTP status 500 is received from the web server). The warnings
    can be turned off with the `--no-warn` flag

2.1 - 2011-11-17

-   Sitemapindex/nested sitemaps support; OCP will now prime all URLs in
    all listed sitemaps of a Sitemapindex XML file

2.0 - 2011-08-06

-   Rewritten in Go; use KeepAlive/existing sessions, optional probing
    concurrency

1.1 - 2010-12-20

-   Switched XML parsing library to improve compatability

1.0 - 2010-11-12

-   Probe all URLs in XML sitemaps