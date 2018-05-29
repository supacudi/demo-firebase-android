# demo-firebase-android
A simple Android application that demonstrates how end-to-end encryption works with firebase as a backend service for authentication and chat messaging. While this is a chat app, you can reuse it in any other apps to protect user data, documents, images.

## Getting Started

Start with cloning repository to computer. For example you can use Android Studio (AS). Open AS, navigate to 'File->New->Project from Version Control->Git' and fill in 'Git Repository URL' field with: 
```
https://github.com/VirgilSecurity/demo-firebase-android
```

### Firebase set up
* Change package of project to yours. 
* Go to the [Firebase console](https://console.firebase.google.com) and create your own project.
* Select the **Authentication** panel and then click the **Sign In Method** tab.
  *  Click **Email/Password** and turn on the **Enable** switch, then click **Save**.
* Select the **Database** panel and then enable **Cloud Firestore**.
  * Click **Rules** and paste:
  ```
  service cloud.firestore {
    match /databases/{database}/documents {
      match /{document=**} {
        allow read, write: if request.auth.uid != null;
      }
    }
  }
  ```
  * Click **PUBLISH**.
* Go to the Project settings and press 'Add Firebase to your Android app' then fill in all fields.
* Download the generated google-services.json file from Project Settings and copy it to the 'app' folder, as metioned in the instructions by Firebase. You're good to go!

#### Cloud functions
* Install node if you don't have one. Firebase recommend to use v6.14.0 at the moment of the demo creation.
* Run `firebase login` to login to your firebase account. Open your terminal app and run `npm install -g firebase-tools` if you don't have it.
* After installed, run `firebase init` in the project root.
* Select `Functions: Configure and deploy Cloud Functions` with the SPACEBAR, then hit ENTER
* Select your firebase project from the list, ENTER.
* Select the following answers:
```
? What language would you like to use to write Cloud Functions? TypeScript
? Do you want to use TSLint to catch probable bugs and enforce style? Yes
? File functions/package.json already exists. Overwrite? No
? File functions/tslint.json already exists. Overwrite? No
? File functions/tsconfig.json already exists. Overwrite? No
? File functions/src/index.ts already exists. Overwrite? No
? Do you want to install dependencies with npm now? Yes
```

* We'll now run this Firebase cli command, but first replace the parameters with data from you Virgil dashboard:
```
firebase functions:config:set virgil.apiprivatekey="YOUR_API_PRIVATE_KEY" virgil.appid="YOUR_APP_ID" virgil.apikeyid="YOUR_API_KEY_ID"
```
* Log back to the [Virgil Dashboard](https://dashboard.virgilsecurity.com/),
* Create an API key: the private key will be copied on your clipboard. Paste this API key and your API Key's ID into the cli command
* Go back to the dashboard, create an application and paste the Application ID into the cli command. Run it.

* Run `firebase deploy --only functions`.
*Note: While Cloud Functions are in Beta, this command may fail with an unexpected error (HTTP 503 "The service is currently unavailable" in the log file), in which case, simply try running it again.*

* Go to the Firebase console -> Functions tab and copy your function url from the Event column
* Go to AS -> app/src/main/java/com/android/virgilsecurity/virgilonfire/di/NetworkModule.java and change variable 'BASE_URL' to:
```
https://YOUR_FUNCTION_URL.cloudfunctions.net/api/generate_jwt
```

## Build and Run
At this point you are ready to build and run the application on your real device or emulator (with google services).
* Check out what Firebase sees from your users' chats: Firebase dashboard -> Database -> Channels -> click on the thread -> Messages. This is what the rest of the world is seeing from the chat, without having access to the users' private keys (which we store on their devices).

## Credentials

To build this sample we used the following third-party frameworks:

* [Cloud Firestore](https://firebase.google.com/docs/firestore/) - as a database for messages, users and channels.
* [Cloud Functions](https://firebase.google.com/docs/functions/) - getting jwt.
* [Firebase Authentication](https://firebase.google.com/docs/auth/) - authentication.
* [Virgil SDK](https://github.com/VirgilSecurity/virgil-sdk-x) - managing users' Keys.
* [Virgil Crypto](https://github.com/VirgilSecurity/virgil-foundation-x) - encrypting and decrypting messages.
