---
title: Add Firebase Realtime Database To Your React App
description: How-To Integrate Firebase Realtime Database with Your React Application
date: 2023-04-18
readingTime: 5 minutes
tags: ["firebase", "database", "react", "javascript", "typescript", "emulator"]
---

{% image "./blog/2023/04/18/cover.jpg", "Close up of a computer hard drive", "(max-width: 640px) 100%, 100%", page.url %}

This post will show you how to connect your existing React application to a Firebase Realtime Database. It assumes you already have a working React application and have setup a Firebase project with a Realtime Database with some data in it. I will not be discussing how to structure or secure your data but I've included some links at the bottom on those subjects.

There is more than one way you can go about doing this. I initially got things working by talking to Firebase directly in my component in the `useEffect()` hooks. From there, I moved the Firebase code to its own class and hooked it up to my app with `useContext()`. This post will demonstrate the second way.

## Install Firebase in your project

Navigate to your React project directory and run `npm install firebase @types/firebase`.

Now would also be a good time to [install the Firebase local emulators](https://alexpeterhall.com/blog/2023/04/14/install-firebase-local-emulator/). For this tutorial we'll be connecting directly to Firebase to make sure it's working properly.

## Setup your Firebase credentials

Open your Firebase Project in the Firebase web console and click the gear icon in the top left next to "Project Overview". Click "Project Settings".

{% image "./blog/2023/04/18/firebase_project_settings.png", "Screenshot of Project Settings location in Firebase", "(max-width: 640px) 100%, 100%", page.url %}

In the default "General" tab that opens scroll down to the "Your Apps" section. You should see your Firebase config details there.

``` json
const firebaseConfig = {
  apiKey: "your_details_here",
  authDomain: "your_details_here",
  databaseURL: "your_details_here",
  projectId: "your_details_here",
  storageBucket: "your_details_here",
  messagingSenderId: "your_details_here",
  appId: "your_details_here",
  measurementId: "your_details_here"
};
```

Create a `.env.production` file in your project root directory with your Firebase credentials. If you are using Create React App (CRA) you can just prefix the environment variables with `REACT_APP` to easily make them available to your app. If you're not using CRA you may need to take a few more steps to make the environment variables available to your app.

Add this file to `.gitignore` to make them a little harder to find but keep in mind these will get bundled into your shipped application code and be publicly visible to anyone who goes looking for them. This API key is not a secret.

```bash
REACT_APP_DATABASE_URL="your_details_here"
REACT_APP_API_KEY="your_details_here"
REACT_APP_AUTH_DOMAIN="your_details_here"
REACT_APP_PROJECT_ID="your_details_here"
REACT_APP_STORAGE_BUCKET="your_details_here"
REACT_APP_MESSAGING_SENDER_ID="your_details_here"
REACT_APP_APP_ID="your_details_here"
REACT_APP_MEASUREMENT_ID="your_details_here"
```

## Setup your Firebase code

Choose where you want to keep your Firebase code. I used `~/src/services/firebase` for this project.

{% image "./blog/2023/04/18/firebase_directory.png", "Screenshot of the author's Firebase directory structure", "(max-width: 640px) 100%, 100%", page.url %}

Create a `context.ts` file with the below code in this directory so it's easy to import and access this context wherever you need it. The context will be initialized to `null` for now.

```ts
// ~/src/services/firebase/context.ts
import { createContext } from 'react'
import { MyFirebase } from './types'

const FirebaseContext = createContext<MyFirebase | null>(null)

export default FirebaseContext
```

Create a `firebase.ts` file in this directory that will contain your Firebase code. I've added my comments inline below.

```ts
// ~/src/services/firebase/firebase.ts
import { initializeApp } from 'firebase/app'
import { get, getDatabase, ref, set } from 'firebase/database'
// Custom types for my class implementation. Yours will be different.
import { MyFirebase } from './types'

/* We'll update this later to handle connecting to the local emulator.
For now we want to test our connection to the actual Firebase service.
This will pull the data from the environment variables we setup earlier. */

let config = {
  apiKey: process.env.REACT_APP_API_KEY,
  authDomain: process.env.REACT_APP_AUTH_DOMAIN,
  databaseURL: process.env.REACT_APP_DATABASE_URL,
  projectId: process.env.REACT_APP_PROJECT_ID,
  storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_APP_ID,
  measurementId: process.env.REACT_APP_MEASUREMENT_ID,
}

// Declare the class that will contain your Firebase code.
class Firebase implements MyFirebase {
  private app
  private database
  constructor() {
    /* Initialize Firebase with your configuration
       and get a reference to your realtime database. */
    this.app = initializeApp(config)
    this.database = getDatabase(this.app)
  }

  /* Examples of some functions to make available in my app to access
     my realtime database. Your code will be different for your project.
     See the links at the bottom for Docs on the Firebase specific stuff.

     If you're using TypeScript you'll probably have custom types
     defined in a types.d.ts file somewhere. */

  getUserTodoList = async (user: string): Promise<TodoList> => {
    const todoList = await get(ref(this.database, `users/${user}/todos/`))
      .then((snapshot) => {
        if (snapshot.exists()) return snapshot.val()
        console.log('No data available')
      })
      .catch((error) => {
        console.error(error)
        throw new Error('Error GETTING from Firebase ' + error)
      })
    return todoList
  }

  updateTodoList = (user: string, list: string, items: TodoItems): void => {
    try {
      set(ref(this.database, `users/${user}/todos/${list}`), items)
    }
    catch (error) {
      console.error(error)
      throw new Error('Error WRITING to Firebase ' + error)
    }
  }
}

export default Firebase
```

Create an `index.ts` file in this same directory to export both the Context and Class we just created above.

```ts
// ~/src/services/firebase/index.ts
import FirebaseContext from './context'
import Firebase from './firebase'

export default Firebase

export { FirebaseContext }
```

## Connect Firebase up to your React app

If you haven't already, you may want to run your app in Strict Mode to detect any data syncing issues. This helped me identify some issues with my code.

```ts
// ~/src/index.tsx
root.render(
  <StrictMode>
    <App />
  </StrictMode>
)
```

Import your Firebase class and context into your `~/src/App.tsx` file.

```ts
// ~/src/App.tsx
import Todo from './components/Todo/Todo'
import Firebase, { FirebaseContext } from './services/firebase'

// Create an instance of your Firebase class
const MyFirebase = new Firebase()

function App() {
  return (
    // Provide the Firebase instance to your Context (was previously initialized to null).
    <FirebaseContext.Provider value={MyFirebase}>
      <Todo />
    </FirebaseContext.Provider>
  )
}

export default App
```

## Access your Realtime Database in your React Components

Now you can import the Firebase context to your React components and start using your Firebase Realtime Database!

```ts
// Import the Context
import { FirebaseContext } from '../../services/firebase'
// Other imports etc.

const Todo = () => {
  // Use the Context
  const Firebase = React.useContext(FirebaseContext)
  // Other state, etc.

  React.useEffect(() => {
    if (Firebase == null) throw new Error('Firebase Database context not found')
    // You can define a function and immediately invoke it or just use an IIFE like below.
    ;(async () => {
      // Call your Firebase functions defined in your class here.
      const data = await Firebase.getUserTodoList(user)
      // Do things with the data, setState, etc.
    })()
  }, [Firebase])

  // Your React Component code here.
}
```

## Use the local emulator

For development you should [install the local emulators](https://alexpeterhall.com/blog/2023/04/14/install-firebase-local-emulator/) and connect to the local database. This makes it safe for you to freely experiment with or delete data with no risk to your production data. Once you have your realtime database emulator up and running you can just update your config in `firebase.ts` to look like this.

```ts
// ~/src/services/firebase/firebase.ts
let config: {}

/* See if the App is running locally. You could also use process.env.NODE_ENV
   to differentiate between development and production environments. */
if (window.location.hostname === 'localhost') {
  // 9000 is the default port. Yours could be different if you changed it during setup.
  config = { databaseURL: 'http://127.0.0.1:9000/?ns=YOUR_DATABASE_NAME_HERE' }
} else {
  config = {
    apiKey: process.env.REACT_APP_API_KEY,
    authDomain: process.env.REACT_APP_AUTH_DOMAIN,
    databaseURL: process.env.REACT_APP_DATABASE_URL,
    projectId: process.env.REACT_APP_PROJECT_ID,
    storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
    messagingSenderId: process.env.REACT_APP_MESSAGING_SENDER_ID,
    appId: process.env.REACT_APP_APP_ID,
    measurementId: process.env.REACT_APP_MEASUREMENT_ID,
  }
}
```

## Conclusion

I hope this helped you get your Realtime Database connected to your React app. By implementing a class and using React context we can keep our Firebase code in one place, maintain a single connection to the database, and share the same functions across many React components.

## Further Reading

[Firebase Docs - Structure Your Database](https://firebase.google.com/docs/database/web/structure-data)
[Firebase Docs - Understand Firebase Realtime Database Security Rules](https://firebase.google.com/docs/database/security)
[Firebase Docs - Realtime Database JS API](https://firebase.google.com/docs/reference/js/database)
[Firebase Docs - Read and Write Data on the Web](https://firebase.google.com/docs/database/web/read-and-write)

## Sources

[Firebase Documentation](https://firebase.google.com/docs/web/setup)
[Robin Wieruch - How to use Firebase Realtime Database in React](https://www.robinwieruch.de/react-firebase-realtime-database/)
[Tutorial: Using Firebase as a Realtime Database with React](https://grotoned.medium.com/tutorial-using-firebase-as-a-realtime-database-with-react-2a3a24c1df91)
