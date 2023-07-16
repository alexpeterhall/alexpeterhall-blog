---
title: Firebase with React Component Provider Pattern
description: Setup your React Context with the Component Provider pattern
date: 2023-07-15
readingTime: 2 minutes
tags: ["firebase", "react"]
---

{% image "./blog/2023/07/15/satellite.jpg", "Large satellite dish against landscape at sunrise", "(max-width: 640px) 100%, 100%", page.url %}

I've been making some progress on the excellent [The Joy of React](https://joyofreact.com/) course by Josh W. Comeau and was introduced to the React Component Provider pattern. This post will cover a small refactor to how I setup my Firebase context in my previous post [Add Firebase Realtime Database To Your React App](https://alexpeterhall.com/blog/2023/04/18/integrate-firebase-and-react/).

Previously, I had my Firebase context setup and exported from its own file called `context.tsx`.

```typescript
import { createContext } from 'react'

const FirebaseContext = createContext<MyFirebase | null>(null)

export default FirebaseContext
```

I also had an `index.ts` file that exported the context and my Firebase class.

```typescript
import FirebaseContext from './context'
import Firebase from './firebase'

export default Firebase

export { FirebaseContext }
```

And finally, I created an instance of my Firebase class and provided it to the context in my `App.tsx` file.

```typescript
import Firebase, { FirebaseContext } from './services/firebase/index.ts'

export const MyFirebase = new Firebase()

function App() {
  return (
    <FirebaseContext.Provider value={MyFirebase}>
      <QuoteList />
    </FirebaseContext.Provider>
  )
}
```

It was a bit of a mess...

With the Component Provider pattern, I can move all of the Firebase context related code into one file and just wrap my App in that component. The provider component exports the context to be used across the app, instantiates an instance of my Firebase class, and wraps any children with the context provider all in one place instead of across 3 different files.

```typescript
// New file FirebaseProvider.tsx
import { ReactNode, createContext } from 'react'
import Firebase from './firebase'

export const FirebaseContext = createContext<FirebaseInstance | null>(null)

function FirebaseProvider({ children }: { children: ReactNode }) {
  return <FirebaseContext.Provider value={new Firebase()}>{children}</FirebaseContext.Provider>
}

export default FirebaseProvider
```

Then, just wrap your app with the Provider component.

```typescript
// App.tsx
function App() {
  return (
    <FirebaseProvider>
      <QuoteList />
    </FirebaseProvider>
  )
}
```

I really like this way of setting up context and think it cleaned up my code with just a few small changes. Give it try!

## Sources

[The Joy of React](https://joyofreact.com/)
