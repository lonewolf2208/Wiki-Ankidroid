# API Documentation

*Audience: This page is for Android app developers. AnkiDroid users can ignore it.*

## Instant-Add API
Starting From AnkiDroid v2.5, notes can be added directly to AnkiDroid's database without sending any intents via a simple API library released on the LGPL license. This is advantageous for developers and end-users, as it allows quickly adding flashcards to AnkiDroid in bulk, without any user intervention. Additionally, an app-specific custom model can be used, so that developers can ensure that their content will be formatted into sensible and beautiful flashcards.

### List of apps that support the Instant-Add API
Please add your app to this list if it supports the instant-add API

[List of Third Party Apps which integrate with AnkiDroid](Third-Party-Apps)

### Gradle dependency
First things first, you should add the following repository to your root `build.gradle` file:
```Gradle
repositories {
    maven { url "https://jitpack.io" }
}
```

Then, add the following dependency to your module's `build.gradle` file:

```Gradle
dependencies {
    implementation 'com.github.ankidroid:Anki-Android:api-v1.1.0'
}
```

### Manifest changes required on Android SDK 30+
The below snippet resolves `Failed to find provider info for com.ichi2.anki.flashcards` which occurs on or above Android SDK 30 or Android 11+:

```html
<manifest>
...
    <queries>
        <package android:name="com.ichi2.anki" />
    </queries>
</manifest>
```

Release Notes:
* [v1.1.0 (2016-03-24)](https://groups.google.com/forum/#!topic/anki-android/LbrQ7kS9Zhg)

### Simplest Example
Here is a very simple example of adding a new note to AnkiDroid. See the [sample app](https://github.com/ankidroid/apisample) for a more complete / detailed example including permission checking, duplicate checking, storing and retrieving the model / deck ID, using tags, using a custom model, etc.

```java
/** Check the AnkiDroid package is available to the API before instantiating
 *  You need to implement deckExists(), modelExists(), getDeckId(), and getModelId().
 *  Note: On SDK 23+ you must also do a permission check before calling API methods! **/
if (AddContentApi.getAnkiDroidPackageName(context) != null) {
    // API available: Add deck and model if required, then add your note
    final AddContentApi api = new AddContentApi(context);
    long deckId = deckExists()? getDeckId(): api.addNewDeck("My app name");
    long modelId = modelExists()? getModelId(): api.addNewBasicModel("com.something.myapp");
    api.addNote(modelId, deckId, new String[] {"?????????", "sunrise"}, null);
} else {
    // Fallback on ACTION_SEND Share Intent if the API is unavailable
    Intent shareIntent = ShareCompat.IntentBuilder.from(context)
            .setType("text/plain")
            .setSubject("?????????")
            .setText("sunrise")
            .getIntent();
    if (shareIntent.resolveActivity(context.getPackageManager()) != null) {
        context.startActivity(shareIntent);
    }
}
```

*Note: To add multiple notes you should use addNotes(), which can often be orders of magnitude faster than calling addNote() in a loop.*

### Permissions
The API requires the permission `com.ichi2.anki.permission.READ_WRITE_DATABASE` which is defined by the main AnkiDroid app. This permission will automatically be merged into your manifest if you included the Gradle dependency above. In order to workaround [this Android limitation](https://code.google.com/p/android/issues/detail?id=25906) we use dynamic permission checking to only enforce permissions on Android M and above, or for certain high risk API calls. See the sample app and the API javadoc for more details on working with permissions.

### Sample app
A very simple example application is [available here](https://github.com/ankidroid/apisample), which gives an expected real-world implementation of the API in the form of a prototype Japanese-English dictionary. Long-press on an item in the list to send one or more cards to AnkiDroid via the share button on the contextual toolbar.

### Testing
Perform the following manual tests to check that your app is working correctly. If your app fails to pass any of these tests, please refer to the sample app again for a reference implementation that passes all of the below tests.

**Test 1: Basic testing (Android 5.1 and below)**

0. Install the [latest dev version of AnkiDroid](https://github.com/ankidroid/Anki-Android/releases)
0. Send some flashcards from your app to AnkiDroid via the API using a deck "Your App Name"
0. Open AnkiDroid, check that a deck called "Your App Name" is there, and click on it to start studying the cards.
0. Flip through a few cards and check that they are formatted correctly
0. Open the Card Browser and check that all of the cards which you sent are there
0. Open the AnkiDroid settings, go to Advanced, and *uncheck* "Enable AnkiDroid API"
0. Try to add a card from your app again. It should correctly detect that the API is unavailable (due to disabling it in the previous step), and fallback on an intent based approach (see section on intents below).
0. Check that the front and back of the flashcard are sent to AnkiDroid in the correct order when using intents
0. Uninstall AnkiDroid
0. Check that your app does not give the user the option to send cards to AnkiDroid (since it's not installed)

**Test 2: Permissions test with Android Marshmallow**

0. Use a device or emulator running Android 6.0 or higher and latest dev version of AnkiDroid
0. Try to add cards to AnkiDroid from your app
0. Android should prompt the user whether or not they want to grant your app the `READ_WRITE_DATABASE` permission
0. Choose "deny"
0. Your app should correctly fallback on adding via an ACTION_SEND intent
0. Try to add cards to AnkiDroid from your app again
0. This time choose to grant the permission
0. Check that the cards were added to AnkiDroid correctly as per previous tests

## Sending cards to AnkiDroid via intent
While we strongly recommend using the Instant-Add API, if the user has a version of AnkiDroid that doesn't support the API, or if they've denied permission to your app to access it, you should fall back on an intent-based approach, which is supported by all versions of AnkiDroid. 

The disadvantage of the intent-based approach compared with the API, is that you can't send multiple cards at once, you leave the user on their own to format your content into flashcards, and most importantly -- the user has to go from your app to AnkiDroid, and then press some buttons to complete the add before they can resume what they were doing in your app, which detracts from the user experience. This is why we only recommend using it as a fall-back when the API is not available.

### ACTION_SEND intent
The `ACTION_SEND` intent is a universal intent for sharing data with other apps in Android. AnkiDroid expects the front text to be in the subject, and the back text to be in the main content. Use [`ShareCompat`](http://developer.android.com/reference/android/support/v4/app/ShareCompat.html) to build the intent so that information about your app is automatically sent to AnkiDroid with the intent:

```java
    Intent shareIntent = ShareCompat.IntentBuilder.from(context)
            .setType("text/plain")
            .setText("Sunrise")
            .setSubject("?????????")
            .getIntent();
    if (shareIntent.resolveActivity(context.getPackageManager()) != null) {
        context.startActivity(shareIntent);
    }
```

### ~~CREATE_FLASHCARD intent~~

*Note: CREATE_FLASHCARD is deprecated from AnkiDroid 2.5. Please use ACTION_SEND instead.*

Another intent which is currently supported by AnkiDroid for backwards compatibility is the `org.openintents.action.CREATE_FLASHCARD` intent. Fields are submitted with intent extras `SOURCE_TEXT` and `TARGET_TEXT` for the front and back respectively:

```java
Intent intent = new Intent();
intent.setAction("org.openintents.indiclash.CREATE_FLASHCARD");
intent.putExtra("SOURCE_TEXT", "?????????");
intent.putExtra("TARGET_TEXT", "Sunrise");
startActivity(intent);
```

# Sync Intent
AnkiDroid v2.5 supports background syncing via an experimental intent which can be sent from [Tasker](http://tasker.dinglisch.net/). Note: a "server is busy" error will be shown if you try to sync more often than once every 5 minutes.

```
Sync Intent [ 
 Action:com.ichi2.anki.DO_SYNC
 Cat:Default 
 Mime Type: 
 Data: 
 Extra: 
 Extra: 
 Package:
 Class: 
 Target:Activity ]
```
*Note: A previous version of this documentation had a Target type of Service instead. Since a recent (mid 2017) Tasker update this no longer works, and Target must be changed to Activity in order to function properly, as discussed at https://groups.google.com/forum/#!topic/tasker/KJISOpinu4U.*

As an example, if you import [this XML file](https://raw.githubusercontent.com/ankidroid/apisample/main/AnkiDroid_Sync.prf.xml) into a Tasker profile, it will trigger AnkiDroid to sync when the power is plugged in. See the [Tasker FAQ](http://tasker.dinglisch.net/userguide/en/faqs/faq-how.html#q) for instructions on how to import profiles.
For the App Automate you can import [this example Flow](https://llamalab.com/automate/community/flows/3963) to sync every 8 hours and a quarter hour after you closed the app.

Sync can also be triggered when your device is connected to your computer via USB, or a terminal emulator that has the `am` tool from Android Debug Bridge such as [Termux](https://github.com/termux/):

`am start -a 'com.ichi2.anki.DO_SYNC'`

# Low-level API

The Instant-add API is a high-level wrapper around the lower level [ContentProvider]() based API. The ContentProvider API contains more features than the Instant-add API, but is more complicated to use. Check the [source code](https://github.com/ankidroid/Anki-Android/tree/main/AnkiDroid/src/main/java/com/ichi2/anki/provider) itself and the accompanying [FlashCardsContract](https://github.com/ankidroid/Anki-Android/blob/main/api/src/main/java/com/ichi2/anki/FlashCardsContract.java) file in the API. The open-source [AnkiDroid-Wear](https://github.com/wlky/AnkiDroid-Wear) app may also be useful as an example of how to use the low-level API.

# React-Native API Wrapper

React-Native developers may use [React-Native-AnkiDroid](https://github.com/is343/react-native-ankidroid) to add some simple Instant-Add implementation to their React-Native apps.


We welcome contributions to create a more developer-friendly API for the features in the ContentProvider, as well as contributions to the low-level API to add new features, and of course contributions to improve / update documentation.

