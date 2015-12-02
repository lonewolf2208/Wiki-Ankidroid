This page contains a collection of tips for formatting your cards on AnkiDroid. The document assumes that you know how to edit card templates using Anki Desktop. Please check [this tutorial video](https://www.youtube.com/watch?v=F1j1Zx0mXME) for an introduction.

### Make flashcards vertically center aligned
The recommended way to center-align your cards is to add the following code to the `.card` class in the "Styling" section of you card templates

```css
.card {
  ...
  position: absolute;
  width: 100%;
  height: 100%;
  display: -webkit-box;
  -webkit-box-align: stretch;
  -webkit-box-pack: center;
  -webkit-box-orient: vertical;
}
```

If you only want the cards to be centered on AnkiDroid and don't care about consistency with other clients, a simpler way is to go to "Settings -> Reviewing -> Center align" and enable the check box.

### Make long words "wrap"

Since Android 4.4 Kit-Kat, Android no longer automatically breaks up long words so that they don't stretch past the edge of the screen. This can be particularly troublesome if you have gestures enabled, because you'll sometimes have to swipe left twice (once to scroll to the edge of the Webview, and again to actually get the swipe to register).

You can fix this issue by adding the following to the `.card` class in the "Styling" section of you card templates:

```css
.card {
 ...
 word-wrap: break-word;
}
```

### Use formatting like bold and italic with custom fonts

Some custom fonts (especially for Asian languages like Japanese) have separate font files for applying bold, etc. You can get these multiple files working using the  official "font-face" method, by using the sample below for the "Styling" section of the card template:


```css
.card {
 ...
 font-family: NotoSansJP;
}

@font-face { font-family: "NotoSansJP"; src: url('_NotoSansJP-Regular.otf'); }
@font-face { font-family: "NotoSansJP"; src: url('_NotoSansJP-Bold.otf'); font-weight: bold; }
```


### Remove the audio play button from flashcards
If you prefer to use the "replay audio" action in the menu, and have the play button hidden on the flashcards, add the following to the bottom of the "Styling" section of your card template:

```css
.replaybutton {display: none;}
```

### Customize night-mode colors
AnkiDroid contains a very basic color inverter that e.g. changes white to black and black to white when night mode is enabled. If you prefer to disable the inverter and setup your own colors, you should include a `.night_mode` class in your styling (takes effect in AnkiDroid v2.5+). For example the following will use a dark grey background instead of black when night mode is enabled:

```css
.card.night_mode {
 color: white;
 background-color: #303030;
}
```

### Hide the input box on cards using the "type in the answer" feature
```css
.mobile input#typeans{
display: none;
}
```

### Remove the margin when displaying images
You can adapt the [following CSS template](https://groups.google.com/d/topic/anki-android/TjakbVGJLmk/discussion) on AnkiDroid 2.5+
```css
.card {
font-family: 'Linux Biolinum O';
font-size: 24px;
text-align: center;
color: #657b83;
background-color: #fdf6e3;
margin: 1ex 0.5em;
}
.card img {max-width: 100%; max-height: none;}
.mobile .card img {max-width: 100%; max-height: none;}
.mobile .card {margin: 0.5ex 0em;}
.mobile #content {margin: 0.5ex 0em;}
```