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
users        | id             | `uuid`         | _  
_            | name           | `string`       | _
_            | email          | `string`       | _
_            | password       | `securely stored by firebase`
_            | classes        | id             | `reference` classes/:class
_            | _              | name           | `string`
_            | _              | school         | `string`
_            | _              | threads        | `reference` threads/:thread
_            | _              |                | `string` name
_            | _              |                | `boolean` notify
classes      | id             | `uuid`         | _
_            | key            | `string` (short id for joining and other things) | _
_            | title          | `string`       | _
_            | school         | `reference` opt (id, name) | _
_            | users          | reference      | `fk` users/:user
_            | _              | name           | `string`
_            | threads        | reference      | `fk` threads/:thread
_            | _              | title          | `string`
schools      | id             | `uuid`         | _
_            | name           | `string`       | _
threads      | id             | `uuid`         | _
_            | name           | `string`       | _
_            | participants   | reference      | `reference` users/:user
_            | _              | name           | `string`
_            | messages       | id             | `uuid`
_            | _              | to             |  `reference` {{users_or_threads}}/:user_or_thread
_            | _              |                | `string` name
_            | _              |                | `string` type ('user' / 'thread' )
_            | _              | from           | `reference` :id _(of user)_
_            | _              | type           | `string` :text / :snippet / :file
_            | _              | subtype        | `string` if type = __snippet:__ :javascript / :python / :html / :latex / :css
_            | _              |    ↑           | if type = __file:__ :gdoc /  :gsheet / :gslide / :txt / :pdf / :image
_            | _              | comment        | `string` (only if subtype = snippet)
_            | _              | content        | `string` content of message or url of file

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
