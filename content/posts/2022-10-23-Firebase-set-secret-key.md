---
title: "Firebase Set Secret Key"
date: 2022-10-23T17:12:45+02:00
draft: true
---



Firebase allows to use a secret key values.
To add some secret use

## Add secret to firebase config
```sh
firebase functions:config:set sercvice.token="xxxxxx"
```
## Display secret
```sh
firebase functions:config:get
```
## Use secret on the server
```js
const functions = require("firebase-functions");
const express = require("express");
const cors = require("cors");
const axios = require("axios");

const app = express();

app.use(cors({ origin: true }));
app.use(express.json());

app.get("/payment_url", (req, res) => {
  axios
    .post(
      "https://some_url.com",
      ...
      {
        headers: {
          "X-Token": functions.config().monobank.token,
        },
      }
    )
    .then((response) => {
      	...
    })
    .catch((error) => {
		...
    });
});

exports.api = functions.https.onRequest(app);
```








