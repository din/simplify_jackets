# What are jackets?

Jacket for [Simplify](http://mmth.us/simplify/) is a simple bundle with HTML, CSS and JS files inside. Jackets are skins for Simplify's desktop widgets. 

A jacket is similar to any HTML web-page with CSS styles and JS scripts. If you can mark-up and style web-pages, you can also 'knit' your own jacket for Simplify. 

## Jacket folder structure and `index.html`

A jacket is a folder. It may contain any number of files. You can place HTML, CSS, JS, or even images inside the folder. 

Every jacket must have one mandatory file called `index.html` in jacket's root folder. This file contains all information about your jacket: name, author, link to web-site, and some other important properties. Also, this file is used to mark-up your jacket and include cascading style sheets and scripts. 

You do not need to include jQuery into your jacket, because it is already included by Simplify. You may, however, include other frameworks, if you like or forbid Simplify to include jQuery. In this case, your responsibility is to avoid their interference with jQuery. 

Note that `index.html` file must be valid HTML (XHTML, HTML4 or HTML5).

### Overall structure of `index.html`

Every `index.html` must contain a set of predefined meta-attributes. They are used to store meta-information about your jacket. Think of this as of some kind of protocol on top of HTML. 

Basic HTML structure of `index.html` is a straightforward HTML document:

```
<!DOCTYPE html>
<html lang="en" xml:lang="en">
	<head>
		<!-- Information and includings of CSS and JS files -->
	</head>
	<body>
		<!-- Mark-up of your jacket -->
	</body>
</html>
```

You specify all necessary information using `meta` tags:

```
<meta name="property-name" content="property-value" />
```

Available properties are described in the sections below.

#### jacket.title

*Required.* Name of your jacket. You can use any letters, numbers, white-space characters and Unicode characters. Avoid very long names. 

Example:

```
<meta name="jacket.title" content="My Jacket" />
```

#### jacket.uid

*Required.* Unique identifier of your jacket. This property was introduced since Simplify 2.6 and it is required. The identifier is represented in reverse DNS notation and it is recommended to use your company name and jacket name to create it.  

Example:

```
<meta name="jacket.uid" content="com.mmth.solar" />
```

#### jacket.version

*Required.* Version of your jacket. Simplify heavily relies on this number to track new versions of installed jackets. You can use `W.X.Y.Z`, `X.Y.Z`, or `X.Y` notation to specify version of jacket. When you update your jacket, just increment the proper number. 

Example:

```
<meta name="jacket.version" content="1.0" />
```

#### jacket.bounds

*Required.* Size of your jacket. Specify it in form `WIDTHxHEIGHT`. User will be able to move your jacket on desktop by clicking on its non-transparent area only.

Example:

```
<meta name="jacket.bounds" content="300x250" />
```

#### jacket.variations

*Required.* Every jacket may have a number of representations or light modifications. For example, it may be 'light' or 'dark', 'big' or 'small'. Do not create multiple versions of your jacket if you just want to switch colours inside it, or make it bigger or smaller. Use CSS styles and variations. 

You can specify any number of variations delimited by comma. You can also specify bounds for some of your variations by using the following string: `My Variation Name (WIDTHxHEIGHT)`. If bounds were not specified, `jacket.bounds` used instead.

Example:

```
<meta name="jacket.variations" content="Light Colours, Dark Colours (200x150)" />
```

#### jacket.settings

*Deprecated since Simplify 2.6.* Comma-separated list of visibility settings for current jacket. If jacket can handle track title visibility, specify `can_handle_track_title_visibility`, if track can handle album cover visibility, specify 'can_handle_cover_visibility'. This preference is used by Simplify to enable/disable appropriate menu-items and preferences inside Simplify itself to allow users turn on or off track/artwork visibility.

**This property was removed in Simplify 2.6. Use `variations` if you need to control visibility of track title or album cover.**

#### jacket.options

*Optional.* Comma-separated list of options to apply to your jacket. Specify `no-jquery` to avoid including of Simplify's own jQuery script, `bowtie-compatibility` to enable compatibility with Bowtie API (this is useful to port to or develop Bowtie themes for Simplify).

Example:

```
<meta name="jacket.options" content="no-jquery" />
```

#### jacket.author

*Optional.* Author of jacket. Your company's or your own name. 

Example:

```
<meta name="jacket.author" content="Semibold Mammoth" />
```

#### jacket.url

*Optional.* URL to web-site of jacket's creator. 

Example:

```
<meta name="jacket.url" content="http://mmth.us/" />
```

### Skeleton for `index.html`

`index.html` with all preferences specified looks like the following document:

```
<!DOCTYPE html>
<html lang="en" xml:lang="en">
	<head>
		<meta name="jacket.title" content="My Jacket" />
		<meta name="jacket.author" content="Semibold Mammoth" />
		<meta name="jacket.url" content="http://mmth.us/" />
		<meta name="jacket.version" content="1.0" />
		<meta name="jacket.uid" content="com.mmth.myjacket" />

		<meta name="jacket.bounds" content="300x250" />
		<meta name="jacket.variations" content="Light Colours, Dark Colours (200x150)" />
		<meta name="jacket.settings" content="can_handle_track_title_visibility" />

		<link rel="stylesheet" type="text/css" href="styles/index.css"  />
		<script type="text/javascript" src="js/script.js"></script>
	</head>
	<body>
		<div id="jacket"></div>
	</body>
</html>
```

Note that you are able to create a jacket with a single `index.html` file by including all scripts and style-sheets inside it. But you can also place all your styles and scripts into external files and include them like you do on any web-page. 

When you include external files, all paths must be *relative* to the root of your jacket folder (in fact, *relative* to your `index.html` file).

## Jackets JavaScript API 

When you created HTML-document for your jacket, it's time to write some JavaScript. You will be using Jackets API in order to communicate with Simplify. This API is pretty straightforward. 

Each jacket has its own JavaScript-environment. This environment contains `Simplify` object to interact with Simplify itself. `Simplify` object is ready to use, you don't need to instantiate it or initialize somehow. 

This section describes its methods, events and properties.

### Incoming events

Jacket may receive various events from Simplify. Examples are: playback state altered, track position or volume adjusted, current track or artwork changed. Your jacket may react to all of these events and modify its interface to provide user with visual feedback.

You can subscribe to an event by using `Simplify.listenEvent(event_type, callback)` method. The first argument is the name of event to listen to. The second argument is a function that will be called when event occurs.

The following list enumerates all available events:

```
PMEvents.Ready
PMEvents.VariationChange
PMEvents.PlaybackStateChange
PMEvents.DisplayCoverChange
PMEvents.DisplayTrackTitleChange
PMEvents.InactiveMouseEnter
PMEvents.InactiveMouseExit
```

The following list enumerates deprecated events since Simplify 2.6:

```
PMEvents.TrackInformationChange
PMEvents.CoverChange
```

#### PMEvents.Ready

Fired once when jacket is loaded. Use this event instead of `onload` or `documentready` events to initially set-up your jacket.

#### PMEvents.VariationChange

Fired when user changes variation of jacket from menu or from preferences. Your `callback` method is provided with new variation title. Use it to modify style of jacket to reflect to user change.

For example, you may append CSS class to or remove CSS class from some element when user changes variation.

```
Simplify.listenEvent(PMEvents.VariationChange, function(variation) 
{ 
	if (variation == "Light Color")
	{
		$("body").addClass("light");
	}
	else
	{
		$("body").removeClass("light");
	}

});
```

Then you may style your elements appropriately using `.light` class on `body` element. 

#### PMEvents.PlaybackStateChange

Fired when user pauses, resumes playback or stops player. New playback state is passed as an argument to `callback`. API defines useful constants that you can use to compare against:

```
PMPlayerState.Playing
PMPlayerState.Paused 
PMPlyaerState.Stopped
```

You can handle this event like so:

```
Simplify.listenEvent(PMEvents.PlaybackStateChange, function(state)
{
	if (state == PMPlayerState.Paused)
	{
		//Do something on pause
	}
	else if (state == PMPlayerState.Playing)
	{
		//Do something on resume
	}
	else
	{
		//Do something when stopped
	}
});
```

#### PMEvents.TrackInformationChange

Fires on track switch (i.e. user selects new track in player, or the next track in playlist plays automatically). Your callback receives `artist`, `cover` and `album` values as arguments. 

Example:

```
Simplify.listenEvent(PMEvents.TrackInformationChange, function(artist, title, album)
{
	//Undefined attributes means stopped playback.
	//First two properties always exist
	//Album may be undefined or empty
	if (artist != undefined && title != undefined)
	{
		//Set new track title here
	}
	else
	{
		//Nothing is set, playback is stopped
	}
});
```

#### PMEvents.CoverChange

Fires when album artwork is delivered for current track. Simplify pushes either path to file with artwork on disk or data-uri encoded image to your callback.

#### PMEvents.DisplayCoverChange

*Deprecated in Simplify 2.6.* Fires when user changed cover visibility for your jacket. This event fires only if you set `can_handle_cover_visibility` in `jacket.settings` property. Callback is called with boolean value that reflects user's choice.

#### PMEvents.DisplayTrackTitleChange

*Deprecated in Simplify 2.6.* Fires when user changed track title visibility for your jacket. This event fires only if you set `can_handle_track_title_visibility` in `jacket.settings` property. Callback is called with boolean value that reflects user's choice.

#### PMEvents.InactiveMouseEnter

Fires if user moves mouse cursor into bounds of jacket.

#### PMEvents.InactiveMouseExit

Fires if user moves mouse cursor out of bounds of jacket.

### Available actions 

Your jacket can also affect behaviour of Simplify by using methods of `Simplify` object. All these methods are described in this section.

#### Simplify.currentVariation()

Returns name of current variation as a string.

#### Simplify.playerState()

Returns current playback state of player as one of predefined constant:

```
PMPlayerState.Playing
PMPlayerState.Paused 
PMPlyaerState.Stopped
```

#### Simplify.togglePlayback()

Resumes playback if player is paused and pauses playback if player is playing something.

#### Simplify.pausePlayback()

Pauses playback.

#### Simplify.resumePlayback()

Resumes playback.

#### Simplify.previousTrack()

Switches to previous track in current playlist.

#### Simplify.nextTrack()

Switches to next track in current playlist.

#### Simplify.setPlayerVolume(volume)

Adjusts current volume. `volume` must be between 0 and 100.

#### Simplify.getPlayerVolume(callback)

Retrieves current volume. This method is asynchronous. You must provide your callback function as an argument. Your callback function will be called with received volume. 

Example:

```
Simplify.getPlayerVolume(function(volume)
{
	//Do something with 'volume'
});
```

#### Simplify.setTrackSeeking(position)

Adjusts current track position. `position` must be an integer representing new position in seconds.

#### Simplify.getTrackSeeking(callback)

Retrieves current track position. This method is asynchronous. You must provide your callback function as an argument. Your callback function will be called with received position.

Example:

```
Simplify.getTrackSeeking(function(position)
{
	//Do something with 'position'
});
```

## Debugging jacket

To debug your jacket using Simplify, you need to create it right inside special directory. 

* Open Finder and navigate to `~/Library/Containers/pixelmates.Simplify/Data/Library/Application Support/Simplify`. 
* Create a new folder and name it something like `my_jacket`. Create a new `index.html` file right in that folder. 
* Fill it with appropriate information about your jacket.
* Open Simplify from command line using the next command: `/Applications/Simplify.app/Contents/MacOS/Simplify --watch-jackets`.
* Select your jacket from preferences or menu.

Now you can edit any files in jacket's directory and Simplify will automatically reload your jacket every time you save your files.

## Distributing jacket

To distribute and share your jacket, simply add to the folder with your jacket contents a '.jacket' extension. If you've got a folder 'my_jacket', just rename it to 'my_jacket.jacket'. Note that you *don't need to compress your folder before renaming it since Simplify 2.6*. 

User will be able to install your jacket from preferences or by double-clicking on `my_jacket.jacket` file.