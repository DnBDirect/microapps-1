D&B Direct MicroApps
====================
The D&B Direct Javascript Library is designed to simplify integration of D&B Direct into web-based applications.


The D&B Direct Javascript Library is designed to simplify integration of D&B Direct into web-based applications. The library is composed of:

dnbapi.js
core API that exposes interfaces to D&B Direct
components/ui/ui.js
user-interface (UI) library that provides streamlined rendering of frequently used UI elements and interactions
proxies/json_facade.php
sample PHP-based API facade


## Requirements
### Client-Side Requirements
The library requires a recent version of jQuery. jQuery was chosen because of its unbiquity, extensibility, and developer support. The UI library works best on IE7+, Chrome 3.3+, Firefox 10+, Safari 5+, and Opera 11+.

### Server-Side Requirements
On the server-side, the core library communicates to the D&B Direct API by means of a facade that resides on your server. The sample facade is designed to be easily deployable with as few dependancies as possible. The included facade requires PHP 5.3 or newer (most modern LAMP stacks should work just fine). Refer to its source for additional notes.

Although a sample facade is PHP-based, it is possible to create your own facade to support other server environments. Refer to the Javascript Library Configuration section to configure the core API library to use a different facade.

## Installation

1. Download the [Source Code](https://github.com/DnBDirect/microapps-1/archive/master.zip)

### Default Installation in a PHP environment

1. Extract to a folder under your webroot, for example /var/www/microapps.

2. Create a local_settings.php file under microapps/proxies/ with the contents below, using your D&B Direct API credentials:

   ```php
   <?php
   $API_KEY =      'Your API KEY';
   $API_USERNAME = 'Your username';
   $API_PASSWORD = 'Your password';
   ```
3. For best-performance, ensure the microapps/proxies/cache folder is writable by your webprocess. On xNIX-based systems, you can typically run:

   ```bash
   chmod 666 microapps/proxies/cache.
   ```

4. Browse to http://your-server/path/to/microapps/

### Alternative Installation using Apache mod_proxy
1. Ensure Apache mod_proxy is enabled. Typically, you'd look for the following lines in your HTTPD_CONF/conf/httpd.conf file. If they are not present, add them to the bottom. If they are present but the lines begin with a comment (#) character, remove the character. Save the file afterward. Usually, the modules are already present and enabled.

   ```ApacheConf
   LoadModule proxy_module modules/mod_proxy.so
   LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
   LoadModule proxy_http_module modules/mod_proxy_http.so
   ```

2. Extract to a folder under your webroot, for example /var/www/microapps.

3. Add the following to location match to your Apache config file. This configures /dnbdirectrest as a local JSON endpoint for D&B Direct using HP's credentials: Open microapps/proxies/json_facade.php in a text editor and update the following line with your D&B Direct API credentials:

   ```ApacheConf
   <LocationMatch "/dnbdirectrest">
     ProxyPass http://dnbdirect-api.dnb.com/DnBAPI-13/rest/
     ProxyPassReverse http://dnbdirect-api.dnb.com/DnBAPI-13/rest/
     RequestHeader set Accept "application/json"
     RequestHeader set Content-type "application/json"
     RequestHeader set API-KEY "Your API KEY"
     RequestHeader set username "Your username"
     RequestHeader set password "Your password"
   </LocationMatch>
   ```
4. Restart Apache

5. Browse to http://your-server/dnbdirectrest/company/001382555 and ensure you see a JSON response. If you do not, there's an issue with the local JSON endpoint - check your Apache error log.

6. Open each microapp's HTML page, in a text editor and replace

   ```HTML
   {API_BASE:'../proxies/json_facade.php'}
   ```
   with
   ```HTML
   { API_BASE: '/dnbdirectrest' }
   ```
   and save.

7. Browse to http://your-server/path/to/microapps/

## Adding the Code to your Page
To use the core and UI library, you would typically add the following code to your page:

```html
<!-- include required jQuery -->
<script type="text/javascript" src="components/lib/jquery-1.7.2.min.js"></script>

<!-- include D&B Direct Javascript API Library -->
<script type="text/javascript" src="dnbapi.js"></script>

<!-- include D&B Direct UI Library -->
<script type="text/javascript" src="components/ui/ui.js"></script>
```

If your page already includes jQuery, you would instead add the following code after jQuery:

```html
<script type="text/javascript" src="dnbapi.js"></script>
<script type="text/javascript" src="components/ui/ui.js"></script>
```


## Javascript Library Configuration
The Javascript API can be configured to an alternate facade by using a JSON object inside the linked script:

```html
<script type="text/javascript" src="dnbapi.js">{ API_BASE: './custom/proxy' }</script>
```
It can also be configured to retry requests that were unfilled due to over-QPS errors by specifying per-client QPS and retry limits:

```html
<script type="text/javascript" src="dnbapi.js">{API_BASE: './custom/proxy', CLIENT_MAX_QPS:3, RETRY_LIMIT:5}</script>
```

## CDN Hosted Javascript Library
Google and other enterprises host jQuery on CDN networks are available for public use.
We also make the D&B Direct Javascript Library on a CDN. To hotlink to CDN-hosted libraries, use...

```html
<script type="text/javascript" src="//ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
<script type="text/javascript" src="http://cdn.dnbdirectapps.com/dnbapi.js"></script>
<script type="text/javascript" src="http://cdn.dnbdirectapps.com/components/ui/ui.js"></script>
```

... or for HTTPS ...

```html
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
<script type="text/javascript" src="https://dnbdirectapps-cdn.s3.amazonaws.com/dnbapi.js"></script>
<script type="text/javascript" src="https://dnbdirectapps-cdn.s3.amazonaws.com/components/ui/ui.js"></script>
```
