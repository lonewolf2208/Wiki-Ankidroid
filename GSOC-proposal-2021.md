# AnkiDroid GSOC proposal

AnkiDroid is a free Android flashcards app which makes remembering things easy.

It is [spaced repetition](https://en.wikipedia.org/wiki/Spaced_repetition) software for Android. Because it's a lot more efficient than traditional study methods, you can either greatly decrease your time spent studying, or greatly increase the amount you learn.  
Anyone who needs to remember things in their daily life can benefit from Anki. Since it is content-agnostic and supports images, audio, videos and scientific markup (via [Mathjax](https://en.wikipedia.org/wiki/MathJax) or LaTeX), the possibilities are endless.

For example:
- Learning a language
- Studying for medical and law exams
- Memorizing people's names and faces
- Brushing up on geography
- Mastering long poems
- Even practicing guitar chords!

We are proud to have 4.8 stars on the play store, and 2 million active installs (as of January 2021). It has an active community of developers, with 34 contributors to code in 2020, two new maintainers, and even new language translations. Many of the contributors did their first android programming with AnkiDroid and we are always happy to help someone learn how to contribute, independent of Google Summer of Code.

You may reach us on the [discord #dev-ankidroid](https://discord.gg/qjzcRTx), Ankidroid’s [forum](https://groups.google.com/g/anki-android), on the Anki's [subreddit](http://reddit.com/r/Anki) though for now it is best to just add contact details to our [list of interested participants](https://github.com/ankidroid/Anki-Android/wiki/Google-Summer-of-Code-2021#list-of-interested-participants)

# Guidelines for Participation

It will help your application if you have already contributed to AnkiDroid. Please note these guidelines for people new to contributing:

- Please feel free to post new issues, but only core contributors may label an issue as a "bug"
- Please only work on issues that are labeled as a "bug", so we know that only issues we agree are important receive focus
- You should strongly consider choosing bugs in existing features for the first few PRs prior to considering any enhancements
- We will expect some level of automated testing to cover any work done on the Projects we list below. You should consider running the existing automated test suite and examining the coverage report, then adding new unit tests that cover parts of the code not already covered as a great way to make a first PR
- Reviewing other developer's code is a fundamental skill for any developer. You should consider performing a PR review (where you reproduce the error locally, then verify that the PR fixes the bug, and you review the code itself to make sure it is understandable for the future) as an excellent way to contribute to a project

Please understand that the interest in contributing to AnkiDroid for the Google Summer of Code is very high. We have around 150 people interested so far, and likely only have the ability to accept one or two official participants total, as an organization.

# Projects

This list of projects is not exhaustive. Discussion with the intern may lead to new projects. In particular, if the intern uses the app and wants some new feature that does not exist yet, we may consider helping them create those features.
Not all projects have the same difficulty level. We will choose a project to work on first, and if the intern does the project quickly we’ll be happy to work on a second simple project.

## Tests coverage

AnkiDroid really lack tests. According to https://app.codecov.io/gh/ankidroid/Anki-Android/ only 37% of the code is currently covered by tests. That means that 63% of the code is never even run during runs. We want to increase this considerably, we want to have tests that ensure that each feature work, and that warn us if anything breaks.

### Expected Outcomes
An increase in code coverage. Less regression in the future

### This task uses:
JAVA. Robolectric and/or test on device. Potentially JS

### Difficulty
From easy to very hard. As examples:
* Finding a background function not covered in /libanki with codecov and adding test for it should usually be easy. Just calling method and checking the result.
* Testing that some button properly implement a feature might require to understand more of the test tool used, and require bigger test. It will likely involve running activities.
* Figuring out a proper way to test the javascript in card might require a whole new way to create tests.


## Scoped Storage Migration
_This is vital to the project continuation_

Google Play will enforce that apps target API 30 (Android 11) later on this year. After this date, we will not be able to release upgrades to AnkiDroid on the Google Play Store until we upgrade the app.

We currently run using the [requestLegacyExternalStorage](https://developer.android.com/reference/android/R.attr#requestLegacyExternalStorage) flag. Once we target API 30, this flag will no longer work on Android 11 devices.

For us to support this flag, I believe that we need to migrate to Scoped Storage: https://developer.android.com/about/versions/11/privacy/storage. The outcome of this will change how AnkiDroid's storage works, likely moving the publicly accessible `AnkiDroid` folder, which may mean changes to:

* Collection database
* Sync outputs (collection & media)
* Camera/Microphone Outputs
* "Save Whiteboard"
* AnkiDroid API usage
* User help documentation (we advise them that uninstall/reinstall is safe currently, but it will not be in the future as it will remove their scoped storage database)

Mentors are less likely to know these new Storage APIs so this will involve collaboration and an aspect of experimentation and independent research.

### Difficulty
Likely Hard

## Loading/uploading shared decks in-app 
(ticketed - but we can do much better here by moving it all in-app)

#### Descriptions 
A deck is a set of questions and answers that you want to learn. For example, a list of all countries in the world or the 1,000 most used words in French. Currently, you need to download a deck first and then import it. So, in this feature, the idea is to allow users to download decks with deck download url, deck id, deck name etc. in order to browse, download, update and import that deck directly into AnkiDroid which improves user experiences.

Another potential feature to consider is to share decks directly from AnkiDroid and add it to the repositories of all shared decks. Currently, this can only be done from the website.

### Expected Outcomes
Option to import deck from url, deck id or deck name

### This task uses:
Experiences with JAVA

### Difficulty
Medium


## Visual Editor
(PR) Questions and answers are displayed in WebView using HTML/CSS/Javascript. Buttons are already present to allow the user to do basic formatting such as italic, bold, adding images etc. That was found to be acceptable when the app was created ten years ago, when we expected most users to create questions using the computer. Nowadays, people have smartphones and don’t necessarily have the app on their computer, thus, allowing more advanced tools becomes more important. This project already started but is not yet fully functional. The intern would work on moving this project to production.

### Expected Outcomes
Note editor in AnkiDroid with additional buttons to format decks 

### This task uses:
Experiences with JAVA, Android XML, HTML

### Difficulty 
Medium


## ~~CSV Import~~ 
**Requested outside GSoC in https://github.com/ankidroid/Anki-Android/issues/6772**
<details>
(ticketed - needs GUI)
Currently, AnkiDroid allows only to import decks in the format .apkg, a format used only by Anki. While it is great to share a deck that already exists, it is not always ideal to create a new deck. As an example, let’s say that you want a deck to help you learn Chinese and your mother tongue is Persian. There may not already exist a Chinese-Persian deck. You may want to create a CSV file, that is, a text file which contains a list of words you want to learn, adding on each line “Chinese word, Persian word”. It is easy to create manually and also easy to generate automatically. 

Currently, this feature exists only on the (free) computer version of the app.
</details>

### Expected Outcomes
The goal of this project is to add an option to import such a list of questions/answers in AnkiDroid. Option in AnkiDroid to import CSV or TSV files and add notes to database using those files

### This task uses:
Experiences with JAVA, Android XML, CSV, TSV

### Difficulty 
Medium


## Additional Dev documentation
Currently, most of the documentations are created for users who want to learn with our app. However, there is a lack of centralized documentation toward developers and deck creators. Questions can have the full power of HTML/JavaScript, and use a template system that is highly parameterizable. More documentation of the advanced features, and of the concept behind the app, would be extremely appreciated.

### Expected Outcomes
A centralized page with documentation for new AnkiDroid contributors and deck creators.

### This task uses:
Experiences with HTML and Markdown

### Difficulty 
Easy

## Onboarding
Onboarding helps new users to introduce the app. The new users may need to understand the user interface of the app. The user may even don’t know about space repetition software. So, the idea is to tell about the advantages of using the app in learning and memorising things and how AnkiDroid will help in memorising things easier.

### Notes from user feedback

* We want people to be able to understand only what they need to know and be able to quickly use the app effectively.
* Our main issues is that people don't know what spaced repetition is, and why they can't study when they want
* We do not want to force the user to log in when they open the app (but it may be an optional flow).
* Some of our users will come from Anki Desktop, it would be good if we can set these up to logon and sync.
* It would be ideal if we could show users where to get more help
* We do not want to add new fields to forms unless there is a known benefit
* We want to be able to solve the common "how do I add reversed cards" question - showing how templating works (but under an advanced section of some sort)

* (David) A user suggested `AppIntro`. I personally wouldn't see this dependency to be problematic as it seems maintained

### Expected Outcomes
Onboarding screen at first start of app to introduce about the app to new user

### This task uses:
Experiences with JAVA, Android UI using XML

### Difficulty
Easy

# Other ideas
We list other ideas, we would be happy to discuss them with a potential intern if they find them more interesting, but expect that the above-listed projects should already give an idea of what kind of projects the intern may participate in.

## WIP: UX/UI
We have various users on the Play Store suggesting that that application could improve both its UX and its UI

### Expected Outcomes
The app will receive less negative criticism about its user interface

### This task uses:
Experiences with JAVA, Android UI using XML

### Difficulty
Variable: Easy-Hard, depending on how large the scope of the change will be

## Deprecation Warnings
Google has introduced a new library for Preferences, we should move our Preferences and Deck Options screen to use this

### This task uses:
Experiences with JAVA 

### Difficulty
Medium

## UI/Application Exerciser Monkey
Google has developed a [UI/Application Exerciser Monkeyy](https://developer.android.com/studio/test/monkey) which performs automated actions to test for bugs in an application. It would improve our testing process if this is included in GitHub actions.

Care must be taken to ensure that this does not cause actions affecting external services (for example: spamming AnkiWeb/our translations services with requests).

### Outcomes

AnkiDroid has much more stronger testing suite, and there is less chance of a crash bug being seen by the public

### This task uses:
Experiences with JAVA  
GitHub Actions

### Difficulty
Easy

## TODO: Donations
* Donation drive from corporations rather than focusing on individuals (to discuss)

## Key Mappings
 (ticketed - needs GUI + GUI design)

* Milestone notifications (encourage users to share/like/donate at given intervals. Need to discuss with others on the project as it's not in Anki Desktop and don't want it to be spammy)
* Press Pack/Support for Academics using Anki(Droid) for research

### Expected Outcomes

### This task uses:
Experiences with JAVA

### Difficulty
Medium