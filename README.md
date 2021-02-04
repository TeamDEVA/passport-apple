#  Sign in with Apple for Passport.js

Passport strategy for the new Sign in with Apple feature, now with fetching profile information ✅!

⚠️ Important note: Apple will only provide you with the name and email ONCE which is when the user taps "Sign in with Apple" on your app the first time. Keep in mind that you have to store this in your database at this time! For every login after that, Apple will provide you with a unique ID that you can use to lookup the username in your database.

## Installation
Install the package via npm / yarn:
``` npm install --save passport-apple-deva ```

Next, you need to configure your Apple Developer Account with Sign in with Apple.

## Usage

Initialize the strategy as follows:

```js
const AppleStrategy = require('passport-apple-deva');
passport.use(new AppleStrategy({
    clientID: "",
    teamID: "",
    callbackURL: "",
    keyID: "",
    privateKeyLocation: "",
    passReqToCallback: true
}, function(req, accessToken, refreshToken, decodedIdToken, profile, cb) {
    // Here, check if the decodedIdToken.sub exists in your database!
    // decodedIdToken should contains email too if user authorized it but will not contain the name
    // `profile` parameter is REQUIRED for the sake of passport implementation
    // it should be profile in the future but apple hasn't implemented passing data
    // in access token yet https://developer.apple.com/documentation/sign_in_with_apple/tokenresponse
    cb(null, decodedIdToken);
}));
```
Add the login route:
```js
app.get("/login", passport.authenticate('apple'));
```

Finally, add the callback route and handle the response:
```js
app.post("/auth", function(req, res, next) {
    passport.authenticate('apple', function(err, user, info) {
        if (err) {
            if (err == "AuthorizationError") {
                res.send("Oops! Looks like you didn't allow the app to proceed. Please sign in again! <br /> \
                <a href=\"/login\">Sign in with Apple</a>");
            } else if (err == "TokenError") {
                res.send("Oops! Couldn't get a valid token from Apple's servers! <br /> \
                <a href=\"/login\">Sign in with Apple</a>");
            }
        } else {
            res.json(user);
        }
    })(req, res, next);
});
```
