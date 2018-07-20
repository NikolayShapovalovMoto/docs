# WordPress stack documentation

* [WordPress stack](https://cloud.wodby.com/stackhub/a54a0f59-f4fd-49af-ad16-8d9ff776c50e/overview)
* [docker4wordpress](https://github.com/wodby/docker4wordpress)

## Deployment 

See [main code deployment article](../../apps/deploy.md) to learn about code deployment options on Wodby.

### Vanilla WordPress

For demo purposes and simple WordPress installations you can use Vanilla WordPress deployment option. In this case WordPress code that comes with the Docker image will be used. In case of changes all data made to your codebase will persist but there will be no versions control.

### Direct git integration

We recommend using [Composer](https://getcomposer.org/) to manage dependencies in your repository. Dependencies will be installed via [post-deployment scripts](../../apps/post-deployment-scripts.md):

1. Fork [our boilerplate](https://github.com/wodby/wordpress-composer)
2. Create `wodby.yml` in repository root (our boilerplate already has it) with the following content:
  ```yml
    pipeline:
    - name: Install dependencies
      type: command
      command: composer install --prefer-dist -n --no-dev
      directory: $APP_ROOT
  ```
3. Enter `web` (it's a directory name with WordPress root in our boilerplate) in `Codebase dir` input on the 3rd step of new application deployment form

### Via third-party CI

* CircleCI: [`.circleci/config.yml`](https://github.com/wodby/wodby-ci/blob/master/php/circleci.yml)
* TravisCI: [`.travis.yml`](https://github.com/wodby/wodby-ci/blob/master/php/travis.yml)
* BitBucket pipelines: [`bitbucket-pipelines.yml`](https://github.com/wodby/wodby-ci/blob/master/php/bitbucket.yml)
* Custom shell script: [`custom.sh`](https://github.com/wodby/wodby-ci/blob/master/php/custom.sh)

### Wodby CI

Coming soon

## Import

There are different way to import existing WordPress website.

### From duplicator archive

Install [duplicator plugin](https://wordpress.org/plugins/duplicator/) on your existing website. Go to admin part of your WordPress website and create a new package via duplicator.

Now navigate to `Apps > Deploy` and choose duplicator archive as data source on the 3rd step.

### From separate archives

Import WordPress via separate archives for database and files. We support `.zip`, `.gz`, `.tar.gz`, `.tgz` and `.tar` archives. This option is available on the 3rd step of a new instance deployment form and also on `[Instance] > Import` page of existing instance.

### Manual import

In case your import data is huge it makes sense to import it manually from the server. Follow these steps:

1. Deploy your WordPress website from a git repository without importing data
2. Once the app is deployed, go to `Stack > SSH` and copy SSH command
3. Connect to the container by SSH
4. Copy your database archive here via `wget` or `scp`, make sure it's gzipped
5. Import unpacked database dump using `wp db import my-db-dump.sql`
6. Now let's import your files, cd to `/mnt/files/public`
7. Copy your files archive here via `wget` or `scp` and unpack the archive
8. That's it! Clear WordPress cache and remove import artifacts

### Import between instances

You can import database and files from one instance to another regardless of whether instances are on the same server or not. Go to `[Instance] > Import` tab and select an instance where you'd like to import database/files from.

## WordPress config

### wp-config.php

Wodby automatically adds include of `wodby.wp-config.php` to `wp-config.php` file in WP root. If the file does not exist Wodby will create it automatically.

Do not edit `wodby.wp-config.php`, all changes to this file will be reset.

The `wodby.wp-config.php` file contains configuration settings for integration with Wodby services such as Database, Cache storage and Reverse Caching Proxy. You can override settings specified in `wodby.wp-config.php` in your `wp-config.php` file after the include.

### Files

Files for WordPress located in `/mnt/files/public` and symlinked to `wp-content/uploads`.

### Base URL

The domain marked with primary flag will be used as a `WP_HOME` and `WP_SITEURL` in `wodby.wp-config.php` file. 

## Cache control

You can clear caches and control cache settings from `[Instance] > Cache` page. The following actions are available:

* Clear application cache
* Clear redis cache (if enabled)
* Clear varnish cache (if enabled)
* Clear all caches
* Enable/disable opcache

## Multi-site 

WordPress multi-site supported, both subdomains and subdirectories based.