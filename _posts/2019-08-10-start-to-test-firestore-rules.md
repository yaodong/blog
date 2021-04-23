---
title: Start to Test Firestore rules
tags: firestore, testing
---

Assume that you have initialized your firebase project. If not, read [this article](https://firebase.google.com/docs/hosting/quickstart) first. Learning how to test Firebase rules is pretty straightforward. First, check out [the official guide](https://firebase.google.com/docs/firestore/security/test-rules-emulator). Then go to [firebase/quickstart-nodejs](https://github.com/firebase/quickstart-nodejs/tree/master/firestore-emulator/javascript-quickstart) to try the quick start project. Finally, watch the video titled [Testing Firestore Security Rules With the Emulator](https://angularfirebase.com/lessons/testing-firestore-security-rules-with-the-emulator/).

The above resources are detailed and precise, so I will not repeat them. The following are some details of Firebase rules I find useful.

Do not use the web-based Firestore Security Rules Simulator. It is very annoying and not convenient.

The object `request.resources` contains the data is like if the request is succeed. So that, don't assume that the request resource data only contains the updated key like this:

```
function only_title_can_updated() {
        return request.resource.data.keys().size() == 1 &&
                request.resource.data.keys().hasAll(['title']);
}
```

The same reason, you can check if the data is changed by comparing `request.resource` with `resource`.

```
function user_id_can_not_be_change() {
	return request.resource.data.userId == resource.data.userId;
}
```

Sometimes, the `npm test` doesn't catch up with the changes to the file. So if the test result doesn't make sense to you. Try to restart the test or save the file again to trigger the test will save you time.

One tutorial on AngularFirebase ([link is here](https://angularfirebase.com/lessons/testing-firestore-security-rules-with-the-emulator/)) saying that you can load testing data before Firestore rules. However, this is not working base on my test:

```
const setup = async (auth, data) => {
  const projectId = `rules-spec-${Date.now()}`;
  const app = await firebase.initializeTestApp({
    projectId,
    auth
  });
  const db = app.firestore();

  await db.doc('collection_name/item_id').set({attr: 'preloaded data'}); 
  // FirebaseError: 7 PERMISSION_DENIED: No matching allow statements

  await firebase.loadFirestoreRules({
    projectId,
    rules: fs.readFileSync('firestore.rules', 'utf8')
  });
  return db;
};
```

Haven't find any other information of preloading data for Firestore rules testing. Will post if founded.