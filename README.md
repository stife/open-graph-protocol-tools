# Open Graph protocol

![Open Graph protocol](http://ogp.me/open_graph_protocol_logo.png "Open Graph protocol logo")

[Open Graph protocol](http://ogp.me/ "Open Graph protocol community site") enhances information associated with a webpage through key-value pairs included as `<meta>` elements in your HTML. This additional data is consumed by social sharing sites, populating a story preview for shared links and referenced objects in the social graph.

This project includes tools to validate and sanitize inputs before generating Open Graph protocol markup on your webpages. This project standardizes outputs for easy indexing by consuming agents.

## Consuming agents

### Facebook

[Facebook indexes Open Graph protocol markup](http://developers.facebook.com/docs/opengraph/ "Facebook Open Graph protocol") found on pages shared in a member's social news feed. This markup also enhances social news feed stories generated by the [Like button social plugin](http://developers.facebook.com/docs/reference/plugins/like/ "Facebook Like button") and [Facebook Share](http://developers.facebook.com/docs/share/).

Open Graph protocol markup turns webpages into objects within the Facebook social graph, increasing search exposure and uniquely classifying your site and story type within the social news feed.

The [Facebook Object Debugger](http://developers.facebook.com/tools/debug) displays a Facebook interpretation of your site's Open Graph protocol content.

### mixi Check

[Mixi indexes Open Graph protocol markup](http://groups.google.com/group/open-graph-protocol/browse_thread/thread/356d722abf70001d/397ec334ca87f122 "mixi Check Open Graph protocol Google Group announcement") in its [mixi Check](http://developer.mixi.co.jp/connect/mixi_graph_api/mixi_io_spec_top/check-api/) social sharing service.

### Google+ Snippet

[Google indexes Open Graph protocol markup](https://developers.google.com/+/plugins/+1button/#plus-snippet) to populate a Google+ activity post.

## Sample code

### Core Open Graph protocol

Support for structured properties for image, video, and audio objects.

    $image = new OpenGraphProtocolImage();
	$image->setURL( 'http://example.com/image.jpg' );
	$image->setSecureURL( 'https://example.com/image.jpg' );
	$image->setType( 'image/jpeg' );
	$image->setWidth( 400 );
	$image->setHeight( 300 );

	$video = new OpenGraphProtocolVideo();
	$video->setURL( 'http://example.com/video.swf' );
	$video->setSecureURL( 'https://example.com/video.swf' );
	$video->setType( OpenGraphProtocolVideo::extension_to_media_type( pathinfo( parse_url( $video->getURL(), PHP_URL_PATH ), PATHINFO_EXTENSION ) ) );
	$video->setWidth( 500 );
	$video->setHeight( 400 );

	$audio = new OpenGraphProtocolAudio();
	$audio->setURL( 'http://example.com/audio.mp3' );
	$audio->setSecureURL( 'https://example.com/audio.mp3' );
	$audio->setType('audio/mpeg');

Declare a new `OpenGraphProtocol` object and set some properties. Add structured media objects.

	$ogp = new OpenGraphProtocol();
	$ogp->setLocale( 'en_US' );
	$ogp->setSiteName( 'Happy place' );
	$ogp->setTitle( 'Hello world' );
	$ogp->setDescription( 'We make the world happy.' );
	$ogp->setType( 'website' );
	$ogp->setURL( 'http://example.com/' );
	$ogp->setDeterminer( 'the' );
	$ogp->addImage($image);
	$ogp->addAudio($audio);
	$ogp->addVideo($video);

Output your OpenGraphProtocol object as HTML `<meta>` elements. Default configuration uses the `property` attribute from RDFa. Change to `name` if you prefer HTML specification compliance and consuming agents support the `name` attribute as a `property` fallback.

    $ogp->toHTML();

### Global objects

Build global objects and attributes. Set time values using either an ISO 8601 formatted string or a DateTime object. DateTimes will be converted to the UTC timezone before output for consistency.

    $article = new OpenGraphProtocolArticle();
	$article->setPublishedTime('2011-11-03T01:23:45Z');
	$article->setModifiedTime(new DateTime('now', new DateTimeZone('America/Los_Angeles')));
	$article->setExpirationTime('2011-12-31T23:59:59+00:00');
	$article->setSection('Front page');
	$article->addTag('weather');
	$article->addTag('football');
	$article->addAuthor('http://example.com/author.html');

Convert a global object to `<meta>` elements just as you would with `OpenGraphProtocol.`

    $article->toHTML();

### Combined

A common use case might be storing Open Graph protocol objects in a Controller for use by your web application. You can add each object to an array and later iterate through the array for `<head prefix="">` and `<meta>` outputs.

    $ogp_objects = array($ogp,$article);
	$prefix = '';
	$meta = '';
	foreach ( $ogp_objects as $ogp_object ) {
	    $prefix .= $ogp_object::PREFIX . ': ' . $ogp_object::NS . ' ';
	    $meta .= $ogp_object->toHTML() . PHP_EOL;
	}

	?>
	<head prefix="<?php echo rtrim($prefix,' '); ?>">
	<?php echo rtrim($meta,PHP_EOL); ?>
	</head>