# What are jackets?

Jacket for [Simplify](http://mmth.us/simplify/) is a simple bundle with HTML, CSS and JS files inside. Jackets are skins for Simplify's desktop widgets. 

A jacket is similar to any HTML web-page with CSS styles and JS scripts. If you can mark-up and style web-pages, you can also 'knit' your own jacket for Simplify. 

# Jacket folder structure and `index.html`

A jacket is a folder. It may contain any number of files. You can place HTML, CSS, JS, or even images inside the folder. 

Every jacket must have one mandatory file called `index.html` in jacket's root folder. This file contains all information about your jacket: name, author, link to web-site, and some other important properties. Also, this file is used to mark-up your jacket and include cascading style sheets and scripts. 

You do not need to include jQuery (currently 1.9.1 is included) into your jacket, because it is already included by Simplify. You may, however, include other frameworks, if you like, or disallow Simplify to include jQuery. 

Note that `index.html` file must be valid HTML (XHTML, HTML4 or HTML5).

## Overall structure of `index.html`

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

### jacket.title

*Required.* Name of your jacket. You can use any letters, numbers, white-space characters and Unicode characters. Avoid very long names. 

Example:

```
<meta name="jacket.title" content="My Jacket" />
```

### jacket.uid

*Required.* Unique identifier of your jacket. This property was introduced since Simplify 2.6 and it is required. The identifier is represented in reverse DNS notation and it is recommended to use your company name and jacket name to create it.  

Example:

```
<meta name="jacket.uid" content="com.mmth.solar" />
```

### jacket.version

*Required.* Version of your jacket. Simplify heavily relies on this number to track new versions of installed jackets. You can use `W.X.Y.Z`, `X.Y.Z`, or `X.Y` notation to specify version of jacket. When you update your jacket, just increment the proper number. 

Example:

```
<meta name="jacket.version" content="1.0" />
```

### jacket.bounds

*Required.* Size of your jacket. Specify it in form `WIDTHxHEIGHT`. User will be able to move your jacket on desktop by clicking on its non-transparent area only.

Example:

```
<meta name="jacket.bounds" content="300x250" />
```

You can also specify initial location of your jacket on screen by using the next expression: `WIDTHxHEIGHT at TOPxLEFT`. Note that (0, 0) point of OS X screen is lower-left point of your screen.

Example:

```
<meta name="jacket.bounds" content="300x250 at 20x20" />
```

### jacket.variations

*Required.* Every jacket may have a number of representations or light modifications. For example, it may be 'light' or 'dark', 'big' or 'small'. Do not create multiple versions of your jacket if you just want to switch colours inside it, or make it bigger or smaller. Use CSS styles and variations. 

You can specify any number of variations delimited by comma. You can also specify bounds for some of your variations by using the following string: `My Variation Name (WIDTHxHEIGHT)`. If bounds were not specified, `jacket.bounds` is used instead.

Example:

```
<meta name="jacket.variations" content="Light Colours, Dark Colours (200x150)" />
```

### jacket.settings

*Deprecated since Simplify 2.6.* Comma-separated list of visibility settings for current jacket. If jacket can handle track title visibility, specify `can_handle_track_title_visibility`, if track can handle album cover visibility, specify 'can_handle_cover_visibility'. This preference is used by Simplify to enable/disable appropriate menu-items and preferences inside Simplify itself to allow users turn on or off track/artwork visibility.

**This property was removed in Simplify 2.6. Use `variations` if you need to control visibility of track title or album cover.**

### jacket.options

*Optional.* Comma-separated list of options to apply to your jacket. Use the option from the following list: 1) `no-jquery` disallows Simplify to include bundled jQuery, 2) `bowtie-compatibility` enables compatibility with Bowtie API (this is useful to port to or develop Bowtie themes for Simplify), 3) `no-dragging` locks your jacket on screen and does not allow user to change its screen location.

Example:

```
<meta name="jacket.options" content="no-jquery, no-dragging" />
```

### jacket.author

*Optional.* Author of jacket. Your company's or your own name. 

Example:

```
<meta name="jacket.author" content="Semibold Mammoth" />
```

### jacket.url

*Optional.* URL to web-site of jacket's creator. 

Example:

```
<meta name="jacket.url" content="http://mmth.us/" />
```

## Skeleton for `index.html`

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
		<meta name="jacket.options" content="no-dragging" />

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

# Jackets JavaScript API 

When you created HTML-document for your jacket, it's time to write some JavaScript. You will be using Jackets API in order to communicate with Simplify. This API is pretty straightforward. 

Each jacket has its own JavaScript-environment. This environment contains `Simplify` object to interact with Simplify itself. `Simplify` object is ready to use, you don't need to instantiate it or initialize somehow. 

This section describes its methods, events and properties.

## Incoming events

Jacket may receive various events from Simplify. Examples are: playback state altered, track position or volume adjusted, current track or artwork changed. Your jacket may react to all of these events and modify its interface to provide user with visual feedback.

You can subscribe to an event by using `Simplify.listen(event_type, callback)` method. The first argument is the name of event to listen to. The second argument is a function that will be called when event occurs.

The following list enumerates all available events:

```
Simplify.events.ready
Simplify.events.newVariation
Simplify.events.newPlaybackState
Simplify.events.newCover
Simplify.events.newTrack
Simplify.events.newScreenResolution
Simplify.events.backgroundMouseIn
Simplify.events.backgroundMouseOut
```

### Simplify.events.ready

Fired once when jacket is loaded. Use this event instead of `onload` or `documentready` events to initially set-up your jacket. Simplify passes two arguments to your handler of this event. The first argument is jacket size restored from Simplify preferences storage. The second argument is initial playback state.

### Simplify.events.newVariation

Fired when user changes variation of jacket from menu or from preferences. Your `callback` method is provided with new variation title. Use it to modify style of jacket to reflect to user change.

For example, you may append CSS class to or remove CSS class from some element when user changes variation.

```
Simplify.listen(Simplify.events.newVariation, function(variation) 
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

### Simplify.events.newPlaybackState

Fired when user pauses, resumes playback or stops player. New playback state is passed as an argument to `callback`. API defines useful constants that you can use to compare against:

```
Simplify.playerState.isPlaying
Simplify.playerState.isPaused 
Simplify.playerState.isStopped
```

You can handle this event like so:

```
Simplify.listen(Simplify.events.newPlaybackState, function(state)
{
	if (state == Simplify.playerState.isPaused)
	{
		//Do something on pause
	}
	else if (state == Simplify.playerState.isPlaying)
	{
		//Do something on resume
	}
	else
	{
		//Do something when stopped
	}
});
```

### Simplify.events.newTrack

Fires on track switch (i.e. user selects new track in player). Your callback receives `artist`, `cover` and `album` values as arguments. 

Example:

```
Simplify.listen(Simplify.events.newTrack, function(artist, title, album)
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

### Simplify.events.newCover

Fires when album artwork is delivered for current track. Simplify pushes either path to file with artwork on disk or data-uri encoded image to your callback.

### Simplify.events.newScreenResolution

Fires when screen resolution was changed. Two arguments are passed into your callback. The first one is the new screen resolution, the second one is previous screen resolution.

Example:

```
Simplify.listen(Simplify.events.newScreenResolution, function(new_resolution, old_resolution)
{
	//new_resolution contains something like {"width" : 2560, "height" : 1440}
	//old_resolution contains something like {"width" : 1920, "height" : 1200}
})
``` 

### Simplify.events.backgroundMouseIn

Fires if user moves mouse cursor into bounds of jacket.

### Simplify.events.backgroundMouseOut

Fires if user moves mouse cursor out of bounds of jacket.

## Available actions 

Your jacket can also affect behaviour of Simplify by using methods of `Simplify` object. All these methods are described in this section.

### Simplify.getJacketVariation()

Returns name of current variation as a string.

### Simplify.getJacketShouldBeFixed()

Returns boolean value indicating whether jacket is fixed on screen (`true`) or not (`false`). 

### Simplify.setJacketShouldBeFixed(fixed)

You can lock your jacket position of screen, so user won't be able to move it around. Pass `true` to fix jacket position on screen, `false` to allow user to change jacket position.

### Simplify.getJacketFrame()

Returns a hash with jacket dimensions and its current location. 

Example return value:

```
{ "width" : 500, "height" : 350, "top" : 40, "left" : 80 }
```

### Simplify.setJacketFrame(dimensions)

Updates current dimensions of jacket or its location on screen. You can alter jacket's size: `width` and `height`, jacket's position: `top` and `left`. You don't need to specify all properties at the same time. 

Example:

```
Simplify.setJacketFrame({"width" : 600}); //Updates jacket width to 600px, but do not modifies its height and position
Simplify.setJacketFrame({"top" : 100, "left" : 100}); //Updates jacket position while preserving its dimensions
Simplify.setJacketFrame({"left" : 200, "height" : 400}); 
```

### Simplify.getPlayerState()

Returns current playback state of player as one of predefined constant:

```
Simplify.playerState.isPlaying
Simplify.playerState.isPaused 
Simplify.playerState.isStopped
```

### Simplify.playbackToggle()

Resumes playback if player is paused and pauses playback if player is playing something.

### Simplify.playbackPause()

Pauses playback.

### Simplify.playbackResume()

Resumes playback.

### Simplify.playbackPreviousTrack()

Switches to previous track in current playlist.

### Simplify.playbackNextTrack()

Switches to next track in current playlist.

### Simplify.setPlayerVolume(volume)

Adjusts current volume. `volume` must be between 0 and 100.

### Simplify.getPlayerVolume(callback)

Retrieves current volume. This method is asynchronous. You must provide your callback function as an argument. Your callback function will be called with received volume. 

Example:

```
Simplify.getPlayerVolume(function(volume)
{
	//Do something with 'volume'
});
```

### Simplify.getTrackLength()

Returns current track length in seconds.

### Simplify.setTrackSeeking(position)

Adjusts current track position. `position` must be an integer representing new position in seconds.

### Simplify.getTrackSeeking(callback)

Retrieves current track position. This method is asynchronous. You must provide your callback function as an argument. Your callback function will be called with received position.

Example:

```
Simplify.getTrackSeeking(function(position)
{
	//Do something with 'position'
});
```

### Simplify.getTrackPlayedAmount(callback)

Calls you callback with double value between `0` and `1` indicating amount of track played.

Example:

```
Simplify.getTrackPlayedAmount(function(amount)
{
	//Do something with 'amount'
});
```

### Simplify.getSystemScreenSize

Returns a hash with dimensions of current system screen.

Example returning value:

```
{ "width" : 1920, "height" : 1080 }
```

# Migrating to Simplify 3.0 API

You need to update event names:

```
PMEvents.Ready							-> Simplify.events.ready 
PMEvents.VariationChange			-> Simplify.events.newVariation
PMEvents.PlaybackStateChange		-> Simplify.events.newPlaybackState
PMEvents.DisplayCoverChange		-> Simplify.events.newCover
PMEvents.DisplayTrackTitleChange	-> Simplify.events.newTrack
PMEvents.InactiveMouseEnter		-> Simplify.events.backgroundMouseIn
PMEvents.InactiveMouseExit			-> Simplify.events.backgroundMouseOut
```

Playback states have new name aliases:

```
PMPlayerState.Playing -> Simplify.playerState.isPlaying
PMPlayerState.Paused  -> Simplify.playerState.isPaused
PMPlyaerState.Stopped -> Simplify.playerState.isStopped
```

Some function has changed their names, too:

```
Simplify.listenEvent 		-> Simplify.listen
Simplify.currentVariation 	-> Simplify.getJacketVariation
Simplify.playerState 		-> Simplify.getPlayerState
Simplify.togglePlayback 	-> Simplify.playbackToggle
Simplify.pausePlayback 		-> Simplify.playbackPause
Simplify.resumePlayback 	-> Simplify.playbackResume
Simplify.previousTrack 		-> Simplify.playbackPreviousTrack
Simplify.nextTrack 			-> Simplify.playbackNextTrack
```

# Debugging jacket

To debug your jacket using Simplify, you need to create it right inside special directory. 

* Open Finder and navigate to `~/Library/Containers/pixelmates.Simplify/Data/Library/Application Support/Simplify`. 
* Create a new folder and name it something like `my_jacket`. Create a new `index.html` file right in that folder. 
* Fill it with appropriate information about your jacket.
* Open Simplify from command line using the next command: `/Applications/Simplify.app/Contents/MacOS/Simplify --watch-jackets`.
* Select your jacket from preferences or menu.

Now you can edit any files in jacket's directory and Simplify will automatically reload your jacket every time you save your files.

# Distributing jacket

To distribute and share your jacket, simply add to the folder with your jacket contents a `.jacket` extension. If you've got a folder `my_jacket`, just rename it to `my_jacket.jacket`. Note that you **don't need to compress your folder before renaming it since Simplify 2.6**. 

User will be able to install your jacket from preferences or by double-clicking on `my_jacket.jacket` file.