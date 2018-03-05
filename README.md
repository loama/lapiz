# lapiz
Communication system for education
> Slack for education

## Build Setup


### install dependencies
`npm install`

### serve with hot reload at localhost:8080
`npm run dev`

### build for deployment (output to /dist)
`npm run build`

### build and deploy to firebase
`npm run deploy`


## Models

Model        | Content        | Sub
------------ | -------------- | ------------
users        | id             | `uuid`         |
             | name           | `string`       |
             | email          | `string`       |
             | password       | `securely stored by firebase`
             | classes        | id             | `reference` classes/:class
             |                | name           | `string`
             |                | school         | `string`
             |                | threads        | `reference` threads/:thread
             |                |                | `string` name
             |                |                | `boolean` notify
classes      | id             | `uuid`         |
             | key            | `string` (short id for joining and other things) |
             | title          | `string`       |
             | school         | `reference` opt (id, name)
             | users          | reference      | `fk` users/:user
             |                | name           | `string`
             | threads        | reference      | `fk` threads/:thread
             |                | title          | `string`
schools      | id             | `uuid`         |
             | name           | `string`       |
threads      | id             | `uuid`         |
             | name           | `string`       |
             | participants   | reference      | `reference` users/:user
             |                | name           | `string`
             | messages       | id             | `uuid`
             |                | to             |  `reference` {{users_or_threads}}/:user_or_thread
             |                |                | `string` name
             |                |                | `string` type ('user' / 'thread' )
             |                | from           | `reference` :id _(of user)_
             |                | type           | `string` :text / :snippet / :file
             |                | subtype        | `string` if type = __snippet:__ :javascript / :python / :html / :latex / :css
             |                |    ↑           | if type = __file:__ :gdoc /  :gsheet / :gslide / :txt / :pdf / :image
             |                | comment        | `string` (only if subtype = snippet)
             |                | content        | `string` content of message or url of file

## Common actions

### account
- __user signs up__:
  using firebase auth, email and password / fb / google

- __user login__:
  using firebase auth

- __user logout__:
  - using firebase auth, email and password / fb / google

  - receiving a __*magic link*__ to their email and using firebase auth with [custom token](https://firebase.google.com/docs/auth/web/custom-auth?hl=es-419) over cloud functions.

### classes
- __user creates class__:
  - save in classes _collection_
  - join the user to the class


- __user joins class__:
  - save to users/:user.id/classes
  - join _general_ thread
  - send message with sender='system' saying that :user joined


- __user unjoins class__:
  - delete from users/:user.id/classes
  - delete from threads/participants/

### threads
- __user creates thread__:
  - save to classes/threads
  - join the user to the thread


- __user joins thread__:
  - save to users/:user.id/classes/:class/threads
  - save to threads/:thread/participants
  - send message with sender='system' saying that :user joined


- __user unjoins thread__:
  - delete from threads/:thread/participants
  - delete from users/:user/classes/:class/threads/:thread
  - send message with sender='system' saying that :user left


- __user mutes thread__:
  TBD


### messages
- __user sends message__:
  - save in threads/:thread/messages
  - send notification to threads/:thread/participants
