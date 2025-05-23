# Statamic Cloudflare Purge

This addon will hook into your already existing statamic invalidation and purge any pages that have been invalidated from your Cloudflare cache.

## Installation

```sh
composer require justbetter/statamic-cloudflare-purging
```

## Usage

You need a Cloudflare API key with the `Zone.Cache Purge` permission, and set it in your `.env`:

```dotenv
CLOUDFLARE_API_TOKEN="token_here"
```

You will also have to define the zone of your website:

```dotenv
CLOUDFLARE_ZONE="zone_id_here"
```

If you have a multistore setup with multiple zones, see the [Configuration](#configuration) section.

This package listens to the `UrlInvalidated` event and adds every invalidated URL to a temp file.

Then, when you run the `statamic:cloudflare:purge` command or the `PurgeCloudflareCaches` job, these files will get purged from the Cloudflare cache. As such, you should add this to your scheduler like so:

```php
Schedule::job(\JustBetter\StatamicCloudflarePurge\Jobs\PurgeCloudflareCaches::class)->everyMinute()
```

## Configuration

You can publish the config with the following command:

```sh
php artisan vendor:publish --provider="JustBetter\StatamicCloudflarePurge\StatamicCloudflarePurgeServiceProvider"
```

### Multiple zones

Using the configuration file you can define multiple zones. You have 3 ways of defining the zone in your config:

```php
// Single zone
'zone' => 'zone_id',
```

```php
// Multiple zones based on statamic site handles
'zone' => [
    'default' => 'zone_id_default',
    'french' => 'zone_id_french',
    ...
],
```

```php
// Complete freedom with a callback
'zone' => function() {
    return \App\Facades\Custom::getCloudflareZone()
},
```