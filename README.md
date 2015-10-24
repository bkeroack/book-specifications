## Kubernetes Book Project

### Specifications
The project should be small enough to do fairly easily and in a small amount of time, but also extensive enough to showcase core concepts of Kubernetes and microservice architectures in general. This is my attempt at a project that covers both of these aspects, but feel free to make suggestions and modifications.

#### Service #1: Authentication Service
Nearly all applications require some sort of authentication mechanism. Since we want all our services to be stateless, we should go with a token-based authentication mechanism. This gives us service #1, the authentication service, which is responsible for the issuing, validation, and invalidation of access tokens. Access tokens should be created through the JWT standard, where you have a token encoded with an issuer key and issuer secret that ensures the token only came from your (trusted) service. The token can also be encoded with basic user information and permission scopes. An example is given below:
````
{
  iss: <Issuer Key>,
  aud: String,
  scopes: [String],
  context: <Basic user information (e.g. user id)
} -> encode with <Issuer Secret>
````
One way to deal with authentication would be to give all the services the issuer key and secret, dealing with decoding the tokens themselves. However, this is a **bad idea** as it means that every service now has knowledge of the authentication mechanism. Ideally, the authentication service would actually be a reverse-proxy to the rest of the application. All requests automatically go through the auth proxy, where the token gets verified and the revelant user information gets tagged to the outgoing request for the other services.

#### Service #2: Tweet Service

This service is responsible for the ability to list and generate tweets (and also retweet them). There will be three endpoints:

- `POST /tweets` Create a new tweet.
- `GET /tweets` Get all your tweets.
- `POST /tweets/:tweet_id/retweet` Retweet a tweet.

Note that I am the only 23 year-old on the planet who doesn't understand what twitter is or how it works.

#### Service #3: Email Service

This service is responsible for sending emails when tweets are retweeted. Instead of calling the email service through REST from the tweet service, we can simply use `Redis` pubsub or something similar to do asynchronous messaging. Whenever a tweet is created, it broadcasts a `tweet-retweeted` event that this service listens into. There is a small database of user ids mapped to email settings. The end result is that users get an email notification when their tweets are retweeted (if they opt in).


