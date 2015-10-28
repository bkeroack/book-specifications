## Kubernetes Book Project

### Specifications
The project should be small enough to do fairly easily and in a small amount of time, but also extensive enough to showcase core concepts of Kubernetes and microservice architectures in general. This is my attempt at a project that covers both of these aspects, but feel free to make suggestions and modifications.

#### Service #1: Tweet Service

This service is responsible for the ability to list and generate tweets (and also retweet them). There will be three endpoints:

- `PUT /users/:username` Create a new user
- `DELETE /users/:username` Delete a user
- `GET /users` List all users
- `POST /users/:username/tweets` Create a new tweet for username.
- `GET /users/:username/tweets` Get all tweets for username.
- `POST /users/:username1/tweets/:tweet_id/retweet?user=username2` Retweet a tweet originally by username1 for username2

Note that I am the only 23 year-old on the planet who doesn't understand what twitter is or how it works.

#### Service #2: Datastore

The datastore consumed by the other services. Single replica RC.

Possible options:

- MySQL
- Postgres
- Cassandra

#### Service #3: Email Service

This service is responsible for sending emails when tweets are retweeted. Instead of calling the email service through REST from the tweet service, we can simply use `Redis` pubsub or something similar to do asynchronous messaging. Whenever a tweet is created, it broadcasts a `tweet-retweeted` event that this service listens into. There is a small database of user ids mapped to email settings. The end result is that users get an email notification when their tweets are retweeted (if they opt in).
