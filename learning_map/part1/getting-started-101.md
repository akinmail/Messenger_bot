#Getting Started 101

Let's get into the meat of things. We are ready to begin creating our application. There are certain things necessary that
we must do to setup. These are outlined as follows :
  * Creating a page
  * Creating a Facebook app
  * Setting up a webhook
  * Getting your Page access token
  * Testing it out

At the end of this setup guide, you will have 3 components namely, a facebook page, a facebook app linked to the page and a
server project(which is your bot) that are functional and communicating together as described in the
architectural diagram provided below


##Creating a page
A facebook page is the first step because the other two components will be linked to your facebook page. Very quickly head over
to [create facebook page](https://www.facebook.com/pages/create) and just follow the simple steps required.

##Creating an app
You will then create a facebook app. Head over to [create facebook app](https://developers.facebook.com/apps) to create an app.
Click on add a new app.

##Setting up a webhook
What is a webhook? Think of webhook as a callback, essentially a webhook is a url that will be called once a event occurs, in
facebook's messenger's case, you will have to supply a url that will be called once an event(message is received from users)
occur. This means you have to implement a web server that provides a url on which you listen for messages because you
expect it to be called once a message is sent by a user to your bot. That's it, simple isn't it?

Now let's focus on creating a simple web server project that will supply us with our url. Note that your server project can
be created using any language or framework of your choice. The only requirement is to be able to spin up an http server in that
language.

  * Step 1 : Choose a secret verify token. This is a secret pass phrase that you will use to verify only facebook and not a
  malicious entity is calling your http endpoint. Because you will not share your app's verify token with any one else. All we
  have to do if someone calls our endpoint is to just verify that the pass phrase is correct.
  * Step 2 : Create a Get http endpoint
    ```javascript
    app.get('/webhook', function(req, res) {

    });
    ```
  * Step 3 : Test for the passphrase first, only people who have our secret verify token should be able to call our http endpoint.
    ```javascript
    app.get('/webhook', function(req, res) {
        if (req.query['hub.mode'] === 'subscribe' &&
              req.query['hub.verify_token'] === <VERIFY_TOKEN>) {
            console.log("Validating webhook");


          } else {
            console.error("Failed validation. Make sure the validation tokens match.");
            res.sendStatus(403);
          }
    });
    ```
_Note : Here we first checked if the query contains a variable hub.mode and if it is equal to subscribe. i.e subscription
from fb will always have this parameter.
  * Step 4 : The last thing to do is to send the challenge sent to us as response back to facebook if everything is fine
    ```javascript
        app.get('/webhook', function(req, res) {
            if (req.query['hub.mode'] === 'subscribe' &&
                  req.query['hub.verify_token'] === <VERIFY_TOKEN>) {
                console.log("Validating webhook");
                res.status(200).send(req.query['hub.challenge']);
              } else {
                console.error("Failed validation. Make sure the validation tokens match.");
                res.sendStatus(403);
              }
        });
        ```
  * Step 5 : Either host your app on a server e.g Heroku or use Ngrok to create a tunnel to your localhost. At the end of this
  step you will have a url of the endpoint we just created of the form https://examplewebsite.com/webhook or
  https://04e3df54.ngrok.io/webhook _Note : must be https.
  * Step 6 : Go to the Facebook app dashboard and click setup webhooks. Supply the url from step 5 and the token you chose in
  step 1 as verify token. Choose any of the events you will like to subscribe to. To get a description of what each event is, check
  [here](https://developers.facebook.com/docs/messenger-platform/webhook).
  Also choose the page you just created to subscribe the app to that page.

##Generating a page access token
On the facebook app dashboard, in the messenger tab, you will see the token generation section. Select the page you just created
and save your newly generated page access token.
