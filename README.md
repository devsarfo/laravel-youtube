Laravel Youtube 
=========

Laravel Package for the Youtube Data API v3

## Requirements

- PHP 7.0 or higher
- Laravel 5.1 or higher
- API key from [Google Console](https://console.developers.google.com)

## Installation

Run in console below command to download package to your project:
```bash
composer require devsarfo/youtube
```

## Configuration

In `/config/app.php` add YoutubeServiceProvider (Laravel < 5.5):
```php
DevSarfo\Youtube\YoutubeServiceProvider::class,
```

Do not forget to add also Youtube facade there (Laravel < 5.5):
```php
'Youtube' => DevSarfo\Youtube\Facades\Youtube::class,
```

Publish config settings:
```bach
$ php artisan vendor:publish --provider="DevSarfo\Youtube\YoutubeServiceProvider"
```

Set your Youtube API key in the file:

```shell
/config/youtube.php
```

Or in the .env file
```shell
YOUTUBE_API_KEY = KEY
```

Or you can set the key programmatically at run time :
```php
Youtube::setApiKey('KEY');
```

## Usage

```php
// use DevSarfo\Youtube\Facades\Youtube;


// Return an STD PHP object
$video = Youtube::getVideoInfo('n5Xp3M8lvzw');

// Get multiple videos info from an array
$videoList = Youtube::getVideoInfo(['n5Xp3M8lvzw','Attb0hi2Dpk']);

// Get localized video info
$video = Youtube::getLocalizedVideoInfo('Attb0hi2Dpk', 'en');

// Get multiple videos related to a video
$relatedVideos = Youtube::getRelatedVideos('Attb0hi2Dpk');

// Get comment threads by videoId
$commentThreads = Youtube::getCommentThreadsByVideoId('Attb0hi2Dpk');

// Get popular videos in a country, return an array of PHP objects
$videoList = Youtube::getPopularVideos('us');

// Search playlists, channels and videos. return an array of PHP objects
$results = Youtube::search('Android');

// Only search videos, return an array of PHP objects
$videoList = Youtube::searchVideos('Android');

// Search only videos in a given channel, return an array of PHP objects
$videoList = Youtube::searchChannelVideos('keyword', 'UCTiH4aWrbJ1u0UVmjH_rUxQ', 40);

// List videos in a given channel, return an array of PHP objects
$videoList = Youtube::listChannelVideos('UCTiH4aWrbJ1u0UVmjH_rUxQ', 40);

$results = Youtube::searchAdvanced([ /* params */ ]);

// Get channel data by channel name, return an STD PHP object
$channel = Youtube::getChannelByName('xdadevelopers');

// Get channel data by channel ID, return an STD PHP object
$channel = Youtube::getChannelById('UCTiH4aWrbJ1u0UVmjH_rUxQ');

// Get playlist by ID, return an STD PHP object
$playlist = Youtube::getPlaylistById('PLKreJXVT4v6w2PinY');

// Get playlists by multiple ID's, return an array of STD PHP objects
$playlists = Youtube::getPlaylistById(['PL590L5WQmH8fJ54F369BLDSqIwcs-TCfs', 'PL590L5WQmH8cUsRyHkk1cPGxW0j5kmhm0']);

// Get playlist by channel ID, return an array of PHP objects
$playlists = Youtube::getPlaylistsByChannelId('UCTiH4aWrbJ1u0UVmjH_rUxQ');

// Get items in a playlist by playlist ID, return an array of PHP objects
$playlistItems = Youtube::getPlaylistItemsByPlaylistId('PL590L5WQmH8fJ54F369BLDSqIwcs-TCfs');

// Get channel activities by channel ID, return an array of PHP objects
$activities = Youtube::getActivitiesByChannelId('UCTiH4aWrbJ1u0UVmjH_rUxQ');

// Retrieve video ID from original YouTube URL
$videoId = Youtube::parseVidFromURL('https://www.youtube.com/watch?v=iFshb7ZsXAg');
// result: iFshb7ZsXAg
```

## Validation Rules

```php
// use DevSarfo\Youtube\Rules\ValidYoutubeVideo;


// Validate a YouTube Video URL
[
    'youtube_video_url' => ['bail', 'required', new ValidYoutubeVideo]
];
```

You can use the bail rule in conjunction with this in order to prevent unnecessary queries.  

## Basic Search Pagination

```php
// Set default parameters
$params = [
    'q'             => 'Android',
    'type'          => 'video',
    'part'          => 'id, snippet',
    'maxResults'    => 50
];

// Make intial call. with second argument to reveal page info such as page tokens
$search = Youtube::searchAdvanced($params, true);

// Check if we have a pageToken
if (isset($search['info']['nextPageToken'])) {
    $params['pageToken'] = $search['info']['nextPageToken'];
}

// Make another call and repeat
$search = Youtube::searchAdvanced($params, true);

// Add results key with info parameter set
print_r($search['results']);

/* Alternative approach with new built-in paginateResults function */

// Same params as before
$params = [
    'q'             => 'Android',
    'type'          => 'video',
    'part'          => 'id, snippet',
    'maxResults'    => 50
];

// An array to store page tokens so we can go back and forth
$pageTokens = [];

// Make inital search
$search = Youtube::paginateResults($params, null);

// Store token
$pageTokens[] = $search['info']['nextPageToken'];

// Go to next page in result
$search = Youtube::paginateResults($params, $pageTokens[0]);

// Store token
$pageTokens[] = $search['info']['nextPageToken'];

// Go to next page in result
$search = Youtube::paginateResults($params, $pageTokens[1]);

// Store token
$pageTokens[] = $search['info']['nextPageToken'];

// Go back a page
$search = Youtube::paginateResults($params, $pageTokens[0]);

// Add results key with info parameter set
print_r($search['results']);
```

The pagination above is quite basic. Depending on what you are trying to achieve you may want to create a recursive function that traverses the results.

## Manual Class Instantiation

```php
// Directly call the YouTube constructor
$youtube = new Youtube(config('YOUTUBE_API_KEY'));

// By default, if the $_SERVER['HTTP_HOST'] header is set,
// it will be used as the `Referer` header. To override
// this setting, set 'use-http-host' to false during
// object construction:
$youtube = new Youtube(config('YOUTUBE_API_KEY'), ['use-http-host' => false]);

// This setting can also be set after the object was created
$youtube->useHttpHost(false);
```

## Run Unit Test
If you have PHPUnit installed in your environment, run:

```bash
$ phpunit
```

If you don't have PHPUnit installed, you can run the following:

```bash
$ composer update
$ ./vendor/bin/phpunit
```

## Format of returned data
The returned JSON is decoded as PHP objects (not Array).
Please read the ["Reference" section](https://developers.google.com/youtube/v3/docs/) of the Official API doc.


## Youtube Data API v3
- [Youtube Data API v3 Doc](https://developers.google.com/youtube/v3/)
- [Obtain API key from Google API Console](https://console.developers.google.com)

## Donation
If you find this project to be of use to you please consider buying me a cup of tea :)

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://paypal.me/BernardSarfoTwumasi)
## Credits
Built on code from Madcoda's [php-youtube-api](https://github.com/madcoda/php-youtube-api) and Mustapha Alaouy's [Youtube](https://github.com/alaouy/Youtube/).
.