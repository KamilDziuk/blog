# Authentication and Authorization (JWT)
2026-08-25

**Tags:**  `web-development` `security` `jwt` `nodejs` `authentication` `authorization` `passport` `express` `mongodb` `api`

Authentication and authorization are two related but distinct concepts. Authentication answers *"who are you?"*, while authorization answers *"what are you allowed to do?"*. One of the most popular ways to implement this in web applications is **JWT (JSON Web Token)** - a stateless token issued by the server after login, which the client then attaches to subsequent requests.

### Why JWT?

* The server doesn't need to store sessions - the token carries all the necessary data
* Easy to integrate with APIs, SPAs, and mobile apps
* Tokens can be signed and verified without a database lookup
* Scales well in a microservices architecture

### How It Works (flow)

```
1. User sends login + password  ->  POST /login
2. Server verifies credentials (e.g. Passport "local" strategy)
3. Server issues a JWT signed with a secret
4. Client stores the token and attaches it in the header:
   Authorization: Bearer <token>
5. Server verifies the token on every protected request (the "jwt" strategy)
```

### Issuing a Token After Login

Once authentication succeeds (e.g. via Passport's `local` strategy), we generate a token and return it to the client:

```js
import User from '../models/user';
import jwt from 'jsonwebtoken';

export default {
    async login (req, res, next) {
        // generate token
        const token = jwt.sign({ id: req.user._id }, process.env.JWT_SECRET, { expiresIn: 1200 });
        // return token
        return res.send({ token });
    },

    async register(req, res, next) {
        const { first_name, last_name, email, password } = req.body;
        const user = new User({ first_name, last_name, email });
        await User.register(user, password);

        res.send('User created successfully. Now you can log in.');
    }
}
```

### Setting Up the JWT Strategy (Passport)

Passport lets you separate token-verification logic from the rest of the app - you just define where to extract the token from and how to validate the payload:

```js
import passport from 'passport';
import passportJWT from 'passport-jwt';
import User from '../models/user';

const JWTStrategy = passportJWT.Strategy;
const ExtractJWT = passportJWT.ExtractJwt;

function verifyCallback(payload, done) {
    return User.findOne({ _id: payload.id })
        .then(user => {
            return done(null, user);
        })
        .catch(err => {
            return done(err);
        });
}

export default () => {
    const config = {
        jwtFromRequest: ExtractJWT.fromAuthHeaderAsBearerToken(),
        secretOrKey: process.env.JWT_SECRET
    };

    passport.use(User.createStrategy());
    passport.use(new JWTStrategy(config, verifyCallback));
}
```

### Middleware to Protect Routes

Instead of verifying the token manually in every controller, we create one reusable middleware:

```js
import passport from 'passport';

export default (req, res, next) => {
    return passport.authenticate('jwt', { session: false })(req, res, next);
}
```

### Access Control Based on the Token

Verifying the token's signature is one thing, but in practice what matters is that **access to a given resource depends on whether a valid token was even sent in the request header**. Here's how it plays out:

* if the `Authorization: Bearer <token>` header contains a **valid, non-expired token** - Passport looks up the user (`verifyCallback`), attaches it as `req.user`, and lets the request continue (`next()`) to the controller,
* if the token is **missing**, **invalid**, or **expired** - the `jwt` strategy never calls `done(null, user)`, and the request is stopped with a **`401 Unauthorized`** response before it ever reaches the controller.

This keeps authorization logic separate from business logic - a controller (e.g. `songsController.create`) can simply assume that if it's running, `req.user` is already set:

```js
import passport from 'passport';

export default (req, res, next) => {
    // missing / invalid token -> Passport responds with 401 Unauthorized on its own
    // valid token -> next() and req.user is available in the controller
    return passport.authenticate('jwt', { session: false })(req, res, next);
}
```

This middleware only needs to be applied to the routes that should be protected - the rest of the API (e.g. reading public data) can be left without it. That's exactly the scenario shown in the Postman screenshot: a `POST /api/song` request with an `Authorization: Bearer eyJhbGciOi...` header passes through to the controller, while the same request **without** that header (or with an invalid token) ends with a `401 Unauthorized` response.

> **Database:** in this project, users and resources (e.g. `Song`) are stored in **MongoDB**, with **Mongoose** handling the connection (`mongoose.connect(...)`). It's the user's `_id` from the `users` collection in MongoDB that ends up in the JWT payload (`jwt.sign({ id: req.user._id }, ...)`), and it's what the `jwt` strategy uses to look the user back up on every subsequent request (`User.findOne({ _id: payload.id })`).

### Practical Use Case - Protected Routes

The `jwtAuth` middleware can be applied selectively to specific endpoints - e.g. creating a resource requires login, while reading it stays public:

```js
import { Router } from 'express';
import jwtAuth from '../middlewares/auth';
import songsController from '../controllers/songsController';

export default () => {
    const api = Router();

    // public read
    api.get('/:slug', songsController.findOne);

    // creating requires a JWT
    api.post('/', jwtAuth, songsController.create);

    return api;
}
```

### Registering the Login Route

```js
import { Router } from 'express';
import AuthController from '../controllers/authController';
import passport from 'passport';

export default () => {
    const api = Router();

    api.post('/login', passport.authenticate('local', { session: false }), AuthController.login);
    api.post('/register', AuthController.register);

    return api;
}
```

### Tips

* Keep `JWT_SECRET` only in environment variables (`.env`), never in the repository.
* Set a sensible token expiration (`expiresIn`) - a shorter-lived token means less risk if it leaks.
* Never put sensitive data (passwords, card numbers) in the token payload - it can easily be decoded (though not forged without the secret).
* Consider adding a refresh token if you need longer sessions without forcing re-login.
* Always send tokens over HTTPS.

## Sources
[JWT.io - Introduction](https://jwt.io/introduction)
[Passport.js - Documentation](https://www.passportjs.org/tutorials/password/)
[#REST API #10|by Overment](https://www.youtube.com/watch?v=GNfxKfgGY1o)
