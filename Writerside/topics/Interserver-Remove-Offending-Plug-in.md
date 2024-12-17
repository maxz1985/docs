# Interserver Remove Offending Plugin

Often after `PHP` version or plugin updates, a WordPress Site becomes unavailable.

You can't get into `WP-Admin` and the site is down. 

Use this procedure to disable plugins that crash the site.

* Log into `CPanel` and navigate to `Terminal` tool.
* Check the `WordPress` error log file
```bash
cd public_html
```
```Bash
tail -f error_log
```
```Bash
[16-Dec-2024 17:17:42 UTC] PHP Parse error:  syntax error, unexpected ')' in /home/{sitename}/public_html/wp-content/plugins/booking/includes/page-setup/setup_support.php on line 259
```
* rename the offending plugin 
```Bash
cd wp-content/plugins
```
```Bash
ls
akismet                         dk-pricr-responsive-pricing-table  php-compatibility-checker   vafpress-framework-plugin
before-after-image-slider-lite  favpress                           shortpixel-image-optimiser  wordpress-importer
booking                         index.php                          suevafree-essential-kit     wp-content-copy-protector
contact-form-7                  nextgen-gallery                    sunshine-photo-cart
```
```Bash
mv booking booking-old
```
* log into `WP-Admin`