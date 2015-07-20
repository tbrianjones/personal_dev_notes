Using Composer: Package Manager for PHP
=======================================


Using Composer
--------------
- Installation: https://getcomposer.org/doc/00-intro.md

### Where to put it
- TBJ typically creates a `composer` folder in an App's `libraries` folder where other third party libraries would be ( eg. the cortex php api client ).
- Then TBJ installs composer like this:
  - cd to the root of the project ( eg. `cd /data/www` for the Cortex API )
  - then execute this: `curl -sS https://getcomposer.org/installer | php -- --install-dir=app/libraries/composer/`
  - the previous command will install composer in the `libraries/composer/` folder
- Once installed:
  - create the necesarry `composer.json` files to install the packages you want
  - then execute `php composer.phar install` from the `composer` fodler
  - you can update packages by executing `php composer.phar update`
- Most packages will have instructions on how to install / use them
  - generally, this will load all packages in php: `require 'vendor/autoload.php';`


Packages We Use
---------------
- URL Parser: https://github.com/jeremykendall/php-domain-parser
- Some others that TBJ is too lazy to put here right now...
