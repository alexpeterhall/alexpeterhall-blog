---
title: Quickly Install Firebase Local Emulators
description: How-to get up and running with Firebase Emulators on Mac
date: 2023-04-14
readingTime: 2 minutes
tags: ["firebase", "emulator"]
---

{% image "./blog/2023/04/14/bonfire.jpg", "Bonfire on beach at night", "(max-width: 640px) 100%, 100%", page.url %}

This post will show you how to quickly install the Firebase Local Emulators on Mac. It assumes you already have a Firebase project created, an existing project you want to connect to Firebase, and are comfortable using the command line. These are the bare-bones commands you need to get the emulators up and running. I recommend you still take the time to read the documentation linked at the bottom to fully understand the emulators.

## Make Sure You Have Java

No, not coffee. The emulators need Java Developer Kit (JDK) version 11 or greater installed on your system to run. Guess how I found this out. :)

Use `javac -version` to verify your JDK version/install. If you don't have it, [download](https://www.oracle.com/java/technologies/downloads/) and run the installer before proceeding.

## Install Firebase CLI

Install the Firebase CLI `curl -sL <https://firebase.tools> | bash`.

The above command will ask for your local machine password to proceed with the install.

After installing you need to authenticate with `firebase login`.

Test it out with `firebase projects:list`. You should get back a nicely formatted list of all your Firebase projects.

## Install Firebase Emulators

Navigate to your project directory and run `firebase init` and follow the prompts to initialize Firebase for your project. This will add a few Firebase configuration .json files to your project directory. I only wanted the Realtime Database and Authentication emulators for this project so I did not install the others.

Fire (lame pun intended) it up! `firebase emulators:start`

{% image "./blog/2023/04/14/start_emulator.png", "Terminal output for start emulator command", "(max-width: 640px) 100%, 100%", page.url %}

The Firebase Emulator has a slick UI that runs on your localhost using whatever ports you setup during the initial configuration. Click the link from the above output in your console to open it in a browser.

{% image "./blog/2023/04/14/firebase_UI.png", "Screenshot of the Firebase Emulator UI", "(max-width: 640px) 100%, 100%", page.url %}

## Seed and Persist Test Data

You can easily seed test data into your emulated database so you have data to work with while developing locally. You'll need a directory you can point to that contains your test data JSON file. Then you can import this data when you start up the Firebase emulators.

```bash
# My import directory is called test_data in this example.
# You need to provide a path to a directory, not to the data file itself!
firebase emulators:start --import=./test_data --export-on-exit
```

The `--export-on-exit` flag will automatically export the emulator data with any changes you've made to the same directory you used for import when you quit the emulators. You can provide a different directory for export if you prefer as well.

You'll also need to update the database URL you're using to connect to the emulators. The endpoint will now have the same name as your test data file.

```javascript
//Ex.
let config = { databaseURL: 'http://127.0.0.1:9000/?ns=dbseeddata' }
```

Add a script to your package.json to make this easy to start up.

```json
  "scripts": {
    "firebase": "firebase emulators:start --import=./test_data --export-on-exit",
  },
```

Now you can just `npm run firebase` and you're off to the races.

## Conclusion

You are now ready to connect your app to the local emulator and start developing locally. If you are using Firebase you should do this as soon as possible, even if you are just getting started with a side project. It gives you the ability to quickly and safely develop against Firebase services locally without an internet connection. Another large benefit if you are using Firestore or Realtime Database is you can safely wipe the local database and easily re-seed test data. Now go forth and build.

## Sources

[Firebase Docs: Firebase CLI reference](https://firebase.google.com/docs/cli)
[Firebase Docs: Install, configure and integrate Local Emulator Suite](https://firebase.google.com/docs/emulator-suite/install_and_configure)
[VIDEO - The Local Firebase Emulator UI in 15 minutes](https://www.youtube.com/watch?v=pkgvFNPdiEs)
