# Chatbots
15-112 Optional Lecture
-----------------------

By: [Jacob Strieb](http://jstrieb.github.io) and [Zuhayer Quazi](https://www.zuhayer.me/)

Published on November 6, 2018

## Sections
1. [JSON](#json)
2. [APIs](#apis)
3. [GroupMe Bot API](#groupme-bot-api)
4. [Facebook Messenger Bot API](#facebook-messenger-bot-api)
5. [Running Code on a Server](#running-code-on-a-server)
6. [Ideas](#ideas)

## Code downloads
1. [GroupMe.py](GroupMe.py)

---

# JSON
"JSON" is an acronym used to describe JavaScript Object Notation. This is essentially a format for transmitting data in the form of "objects" (think object-oriented programming). The notation is very similar to Python's syntax for dictionaries. Generally, JSON objects can be thought of as a text form of dictionaries.

Here is an example of a JSON object:
```json
{
  "firstName": "John",
  "lastName": "Smith",
  "isAlive": true,
  "age": 27,
  "address": {
    "streetAddress": "21 2nd Street",
    "city": "New York",
    "state": "NY",
    "postalCode": "10021-3100"
  },
  "phoneNumbers": [
    {
      "type": "home",
      "number": "212 555-1234"
    },
    {
      "type": "office",
      "number": "646 555-4567"
    },
    {
      "type": "mobile",
      "number": "123 456-7890"
    }
  ],
  "children": [],
  "spouse": null
} 
```

Parsing text formatted as JSON objects is very easy in Python. Using the `json` library, and calling the `json.loads` method on a properly-formatted string will return a regular Python dictionary.

Assuming that the above text is stored as `jsonString`, we can do the following to turn it into a dictionary in Python:
```python
import json

jsonDict = json.loads(jsonString)

# Prints: 27
print(jsonDict["age"])

# Prints: 3
print(len(jsonDict["phoneNumbers"]))

# Prints: dict
print(type(jsonDict["address"]))
```

In order to create JSON text strings from Python dictionaries, we can use the `json.dumps` method. Calling this method will return a properly-formatted JSON string assuming that each item in the dictionary is well-formed. If you are working with custom objects, you will need to tell the `json` library how to turn those objects in to `json` strings.

Additionally, the `indent` optional parameter to the `json.dumps` method is very useful for printing and debugging JSON strings. This allows the output string to be formatted nicely. The `indent` parameter is an integer representing the number of spaces to indent each successive nested layer of the dictionary. Typical values are 2 or 4.

Here is an example of modifying some values from the JSON string above and returning the modified JSON string:
```python
import json

jsonDict = json.loads(jsonString)

# Change the age
jsonDict["age"] = 69

# Add children
jsonDict["children"].append("Kiddo1")
jsonDict["children"].append("Little Boi")

# Add a field
jsonDict["favNumber"] = 420

# Modify the address
jsonDict["address"]["postalCode"] = "15213"

# Get a nicely formatted JSON string
newJsonString = json.dumps(jsonDict, indent=2)
print(newJsonString)
```

Running this code prints the following:
```json
{
  "firstName": "John",
  "lastName": "Smith",
  "isAlive": true,
  "age": 69,
  "address": {
    "streetAddress": "21 2nd Street",
    "city": "New York",
    "state": "NY",
    "postalCode": "15213"
  },
  "phoneNumbers": [
    {
      "type": "home",
      "number": "212 555-1234"
    },
    {
      "type": "office",
      "number": "646 555-4567"
    },
    {
      "type": "mobile",
      "number": "123 456-7890"
    }
  ],
  "children": [
    "Kiddo1",
    "Little Boi"
  ],
  "spouse": null,
  "favNumber": 420
}
```

More information about JSON can be found at the following links:

- [Python JSON Documentation](https://docs.python.org/3/library/json.html)
- [Official JSON Standard Website](https://www.json.org/)
- [JSON Wikipedia Page](https://en.wikipedia.org/wiki/JSON)

# APIs
"API" is an acronym that stands for Application Programming Interface. APIs are designed to allow programs to interact with other programs as easily as possible. Many modern Internet-based APIs use JSON to transfer data between servers and client software. This is typically done using network requests.

In simple terms, network requests are when data is sent to a particular URL. Network requests come in one of two forms: `GET` or `POST`. So-called `GET` requests pass parameters to the destination page via the URL. A typical `GET` request URL might look like the following:
```
http://jstrieb.github.io/some_page?variable1=value1&varable2=value2&variable3=value3
```

Unlike `GET` requests, `POST` requests send data to the destination URL within the body of the request. When reading API documentation, pay attention to whether or not the requests must take the form of `GET` or `POST` requests and write your code accordingly.

Requests to URLs can be made using Python very easily using the `requests` library. These requests both send data to the destination URL and receive a response back. We will write an example that uses the GIPHY API to get certain types of GIF images.

When trying to write code using a particular API, the first step is to locate the API documentation, which will guide how we write our code. In this case, the GIPHY API documentation can be found [here](https://developers.giphy.com/docs/). Many websites strongly encourage the use of their services via the API and have associated tutorials in various languages teaching how to use them. Many APIs also require that the software using them is registered with the service and has an "API key" which is just a special string passed along with the request made to the API so that the service can track who is using it.

To get started with the GIPHY API, we must first register our app using their online form. We do this by going to the [GIPHY developer page](https://developers.giphy.com/) and registering, then hitting the "Create an App" button and following the instructions given. Once registered, we will get an API key that looks like the following:
```
exiOXgECL7g582dV2Mt85vYZoSs1Kb0m
```

Generally, it is a good idea to keep API keys secret and safe. This is because anything that could be done with a user account on a website could also be done with the API key.

Now that we have our API key, we can make requests to the GIPHY API. The API Docs say that we can get GIFs using the Search Endpoint. More information about this endpoint can be found [here](operation--gifs-search-get). The Docs specify that we need to make a `GET` request to the following URL: `http://api.giphy.com/v1/gifs/search` with the following parameters:

- `api_key` which will be our API key
- `q` which will be the term we want to search for
- `offset` which offsets the results (which we can use to get a random result of a specific type)
- A number of other optional parameters that we don't care about at the moment

The Docs for the API also specify what the response will look like, but for now we will just make some requests and see what we can extract from the response. For this example, we will get a random cat GIF and print the link to the screen.

In the following example, we import the `requests` library, assemble the data we want to send in a `data` dictionary, and make a `GET` request. Then we print the JSON output to the screen:
```python
import requests
import random

data = {
         "api_key" : "exiOXgECL7g582dV2Mt85vYZoSs1Kb0m",
         "q" : "cat",
         "offset" : random.randint(0, 500)
       }

r = requests.get("http://api.giphy.com/v1/gifs/search", params=data)

print(r.text)
```

The example above searches GIPHY for "cat" and randomly offsets the search results. The code will print a bunch of JSON data with the returned GIFs. Now, we want to parse this data and extract meaningful information. In our case, we want the URL of the first GIF.

Alternatively, it is also possible to make `GET` requests using the browser by manually encoding our parameters in the URL and then navigating there. For example, by entering the following URL into our browser, we can see the same output that the code above returns:
```
http://api.giphy.com/v1/gifs/search?api_key=exiOXgECL7g582dV2Mt85vYZoSs1Kb0m&q=cat
```

By manually inspecting the output of the above code, we see that within the `data` attribute each element has a `bitly_gif_url` attribute that has a shortened link to the GIF. The following code extracts this URL and prints it to the screen:
```python
import requests
import random

data = {
         "api_key" : "exiOXgECL7g582dV2Mt85vYZoSs1Kb0m",
         "q" : "cat",
         "offset" : random.randint(0, 500)
       }

r = requests.get("http://api.giphy.com/v1/gifs/search", params=data)
url = r.json()["data"][0]["bitly_gif_url"]
print(url)
```

Note that the above code uses the JSON parser built natively into the `requests` library. When run the first time, this code linked to the following fun cat GIF:

![](http://i.imgur.com/0Y1xISa.gif "Lit Cat GIF")

# Running Code on a Server
Once your code to process and send messages has been written and tested locally, you can upload it to a server so that it can run while your computer is off. We will include a brief tutorial below for setting up the code you have written on the cloud platform Heroku.

Most of the following setup steps were taken from [this tutorial](http://www.apnorton.com/blog/2017/02/28/How-I-wrote-a-Groupme-Chatbot-in-24-hours/). Feel free to follow it instead.

Note: putting code on Heroku requires the use of `git` in addition to installing the `heroku` command-line interface from [here](https://devcenter.heroku.com/articles/heroku-cli). Please make sure you can use the `git` and `heroku` commands before proceeding.

First, create a new folder and initialize a new `git` repository. Then create a new `heroku` app in the folder with the repository. To do this, run the following commands in `cmd`/Terminal/`bash` depeding on whether you are on Windows, Mac, or Linux (respectively):

```bash
mkdir bot
cd bot
git init .
heroku apps:create bot
git remote
```

Once the repository has been created, you will need to add a `Procfile`, `runtime.txt` and `requirements.txt` file to the repo so that Heroku knows how to set up your environment. Put the following things in each of the files:

#### `Procfile`
```
web: gunicorn app:app --log-file=-
```

#### `runtime.txt`
```
python-3.6.0
```

#### `requirements.txt`
```
Flask==0.12
gunicorn==19.6.0
```

Once the above files have been added to the repository, the last step is to add your code and tell Heroku to run it. In order to add your code, insert it into a file called `app.py` formatted as the code below is:
```python
import json
import requests
from flask import Flask, request

app = Flask(__name__)
botId = "<your bot ID here>"

@app.route('/', methods=['POST'])
def webhook():
    msg = request.get_json()

    # Ignore messages sent by the bot
    if msg["sender_type"] != "bot":
        process(msg)

# Insert any helper functions used by the process() function here
# For example, the send() function:
def send(text):
    data = { "bot_id" : botId, "text" : text }
    requests.post("https://api.groupme.com/v3/bots/post", data=data)

def process(msg):
    # Insert your process() function that you wrote as with the test code above
    pass
```

Once `app.py` has been created, it is time to send the code to Heroku to be run. To do this, run the following commands:
```bash
git add .
git commit -am 'updated code'
git push heroku master
```

The last step when that has been done is to get the address of your Heroku project and set it as the "Callback URL" on the GroupMe Bots page [here](https://dev.groupme.com/bots).

More information about running a GroupMe Bot (and other code) on Heroku can be found here:

- [GroupMe Bot Heroku Tutorial](http://www.apnorton.com/blog/2017/02/28/How-I-wrote-a-Groupme-Chatbot-in-24-hours/)
- [Getting Started with Heroku](https://devcenter.heroku.com/articles/getting-started-with-python)
- [Getting Started with Flask](https://scotch.io/tutorials/getting-started-with-flask-a-python-microframework)

# GroupMe Bot API
Now that we know how to use APIs in general, we can begin to build a chatbot for the GroupMe messaging platform that will send messages in a group chat when we run the code. Doing this is largely the same as the procedure we went through in order to get GIFs from the GIPHY API.

First, we must register a bot with a group. This can be done at [this page](https://dev.groupme.com/bots/new). Once the bot is registered, as with the GIPHY API, we will be given an access token that should be kept secret and safe. Then, sending a message is as easy as using the requests library as before:
```python
import requests

data = {
         "bot_id" : "b52841049bfccdfe5217512b63",
         "text" : "This is a test"
       }

r = requests.post("https://api.groupme.com/v3/bots/post", data=data)
```

Notice that this time, when we send the message, we make a `POST` request as opposed to a `GET` request. Also note that we use the optional parameter `data` instead of `params`. 

More information on creating bots can be found at the official [GroupMe Bots documentation here](https://dev.groupme.com/tutorials/bots).

Simply sending messages is very useful; if you need to leave code running for a long time it can be helpful to get a notification on your phone when it has completed. Likewise, if you run code on a server, it can be helpful to have diagnostic information sent back to your phone.

Now that we know how to send messages, we will make our bot respond to messages. There are two ways to do this. One way involves running your code locally on your computer, the other involves setting it up on a server that will remain running all of the time. Running it on your computer is useful for testing bot functionality, but when actually deploying a bot it is best to let it run on a server. See the [Servers](#running-code-on-a-server) section for more information.

The reason that doing this is more complex than having the bot simply send messages is that we need to regularly check whether or not new messages have been sent in the group. The strategy that we will use here involves regularly checking the GroupMe server that will let us know if there are new messages. Doing this requires an access token in addition to a Bot token (they are different for GroupMe). An access token can be obtained by clicking the "Access Token" button in the top right corner of the page where you registered your GroupMe Bot. It may be necessary to also register the bot as an [application](https://dev.groupme.com/applications)

To poll the server for new messages, we will use the GroupMe push service. In order to use this service, our code must first get a clientId, and then must "subscribe" to a channel before being able to poll it for new messages. The following code does all of these steps before entering the main loop of the function where it polls for new messages. After seeing new messages, it makes sure it is responding to messages in the correct group, and makes sure that the bot isn't responding to itself (which could result in an infinite loop).

This simple bot merely repeats whatever is sent in its group:
```python
import requests
import time
import json

#*******************************************************************************
################################################################################
# 
# Created by Jacob Strieb for the Chatbots optional lecture given as part of
# the 15-112 Fall 2018 semester at Carnegie Mellon University
# 
################################################################################
#*******************************************************************************

##################################################
# Global Variables
##################################################

# All of these can be found on the bot registration page here:
# https://dev.groupme.com/bots/
accessToken = "<your API key here>"
botId = "<your bot ID here>"
groupId = "<your bot's group ID here>"

##################################################
# GroupMe API Helper Functions
##################################################

# Send a message as the bot
def send(text):
    data = { "bot_id" : botId, "text" : text }
    requests.post("https://api.groupme.com/v3/bots/post", data=data)

# Get my user ID for later API calls
def getUserId(accessToken):
    data = { "access_token" : accessToken }
    r = requests.get("https://api.groupme.com/v3/users/me", params=data)
    return r.json()["response"]["user_id"]

# Get a client ID to use for this session
def getClientId():
    # Copied data from tutorial here: https://dev.groupme.com/tutorials/push
    data = [ {
               "channel" : "/meta/handshake",
               "version" : "1.0",
               "supportedConnectionTypes" : ["long-polling"],
               "id" : "1"
             } ]
    r = requests.post("https://push.groupme.com/faye", json=data)
    return r.json()[0]["clientId"]

# Subscribe to the group channel -- bind the client ID to the user ID
def subscribe(accessToken, clientId):
    # Copied data from tutorial here: https://dev.groupme.com/tutorials/push
    data = [ {
               "channel" : "/meta/subscribe",
               "clientId" : clientId,
               "subscription" : "/user/%s" % getUserId(accessToken),
               "id" : "2",
               "ext" : {
                         "access_token" : accessToken,
                         "timestamp" : int(time.time())
                       }
             } ]
    r = requests.post("https://push.groupme.com/faye", json=data)
    if not r.json()[0]["successful"]: raise Exception("Subscription failed")

# Get new messages from the server
def getNew(clientId, numCalls):
    # Copied data from tutorial here: https://dev.groupme.com/tutorials/push
    data = [ {
               "channel" : "/meta/connect",
               "clientId" : clientId,
               "connectionType" : "in-process",
               "id" : "%d" % numCalls
             } ]
    try:
        r = requests.post("https://push.groupme.com/faye", json=data, stream=True)
    except:
        print("There was a problem getting the next messages.")
        return
    for line in r.iter_lines():
        preProcess(line.decode("utf-8"))

# Ensure each message sent to the right group and not from the bot
def preProcess(line):
    data = json.loads(line)
    for response in data:
        # Make sure it is a message type
        if "data" not in response: continue
        if "type" not in response["data"]: continue
        if response["data"]["type"] != "line.create": continue
        if "subject" not in response["data"]: continue

        # Make sure it was sent to the correct group and not from the bot
        msg = response["data"]["subject"]
        if msg["group_id"] != groupId: continue
        if msg["sender_type"] == "bot": continue

        # If none of the above skipped, actually process the message
        process(msg)

##################################################
# Bot functionality
##################################################

# Process the msg dictionary containing sender and message information
def process(msg):
    send(msg["text"])

    # Uncomment the following line to print the whole message dict
    # print(json.dumps(msg, indent=2))

##################################################
# Main Function
##################################################

if __name__ == "__main__":
    # Try subscribing
    clientId = ""
    try:
        clientId = getClientId()
        subscribe(accessToken, clientId)
    except:
        print("There was a problem while trying to subscribe to the message"
              "feed.")
        exit()

    # Poll the server for new messages
    numCalls = 3
    while True:
        getNew(clientId, numCalls)
        numCalls += 1
```

Note that the code above as a space for you to enter your bot's details. Also note that the only part of the code that actually processes messages is the `process` function near the bottom of the code file. The rest is responsible for handling the connection to the GroupMe API.

For your convenience, the code above can be downloaded from [here](GroupMe.py).

The code above should not be used for complete projects, but should only be used for testing bot functionality before deploying on a server. Likewise for large projects, the code above should be imported as a module and separated from the `process` function to make the code more readable.

More information about GroupMe APIs can be found here:

- [Register Bots](https://dev.groupme.com/bots)
- [Main Documentation Link](https://dev.groupme.com/)
- [Bots Tutorial](https://dev.groupme.com/tutorials/bots)
- [Tutorial for Push Connections](https://dev.groupme.com/tutorials/push)
- [Download the Code Above](GroupMe.py)

# Facebook Messenger Bot API
###Dependencies
- Flask `pip install flask`
- pymessenger `pip install pymessenger`
- ngrok (hosting service)

###Flask Application
Flask is a service (written in python) that runs an application (in this case, the application is our Lil Pump Facebook Page Messenger). In our Flask application we will be handling *all* of the following:
- Receiving messages
- Verifying authenticity through tokens
- Get user information
- Send message to user

```python
#Python libraries that we need to import for our bot
import random
from flask import Flask, request
from pymessenger.bot import Bot

app = Flask(__name__)
ACCESS_TOKEN = 'fdskafndsfjslafdsakjfdsaklfsaldf' #my token! :)
VERIFY_TOKEN = 'esketit' #some random verify token
bot = Bot(ACCESS_TOKEN)

#We will receive messages that Facebook sends our bot at this endpoint 
@app.route("/", methods=['GET', 'POST'])
def receive_message():
    if request.method == 'GET':
        """Before allowing people to message your bot, Facebook has implemented a verify token
        that confirms all requests that your bot receives came from Facebook.""" 
        token_sent = request.args.get("hub.verify_token")
        return verify_fb_token(token_sent)
    #if the request was not get, it must be POST and we can just proceed with sending a message back to user
    else:
        # get whatever message a user sent the bot
       output = request.get_json()
       for event in output['entry']:
          messaging = event['messaging']
          for message in messaging:
            if message.get('message'):
                #Facebook Messenger ID for user so we know where to send response back to
                recipient_id = message['sender']['id']
                if message['message'].get('text'):
                    response_sent_text = get_message()
                    send_message(recipient_id, response_sent_text)
                #if user sends us a GIF, photo,video, or any other non-text item
                if message['message'].get('attachments'):
                    response_sent_nontext = get_message()
                    send_message(recipient_id, response_sent_nontext)
    return "Message Processed"


def verify_fb_token(token_sent):
    #take token sent by facebook and verify it matches the verify token you sent
    #if they match, allow the request, else return an error 
    if token_sent == VERIFY_TOKEN:
        return request.args.get("hub.challenge")
    return 'Invalid verification token'


#chooses a random message to send to the user
def get_message():
    sample_responses = ["ESKETITTTTTT", "100 on my wrist, 80 on a brick, Lil Pump never spend money on a b**ch", "Gucci Gang Gucci Gang Gucci Gang Gucci Gang Gucci Gang", "I sold crack on PayPal",
              "LIL PUMP IN THE WHITE HOUSE, MADE WHITE HOUSE TURN TO A TRAP HOUSE", "Took 4 xans now I'm feeling like a hero.. And my auntie on P.O."]
    # return selected item to the user
    return random.choice(sample_responses)

#uses PyMessenger to send response to user
def send_message(recipient_id, response):
    #sends user the text message provided via input response parameter
    bot.send_text_message(recipient_id, response)
    return "success"

if __name__ == "__main__":
    app.run()
```

###How to Set Up Your Bot
- Create Facebook Page (literally anything!)
- Link your facebook account to FB Developers
- Create Messenger App
- Get API token from the dashboard (link your fb page)
- Integrate Webhook using **ngrok** link.

###How to Host it
- `python app.py` will run your Flask App and return something like this:  `* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)`
- `ngrok http 5000` will give you a link like `https://813123d.ngrok.io`
- Paste that into the Webhook above, subscribe your page.

# Ideas
Now that you have the means to make a chatbot, what can you do with it? Below is a list of some chatbot features that I have personally implemented (or one day hope to add to my chatbots):

- Check the weather
- Define terms
- Look terms up on Urban Dictionary
- Caption images
- Insult members of the group chat
- Compile a to-do list
- Do simple math
- Add songs to a Spotify playlist
- Tell jokes
- Say "that's what she said"
- Play 20 questions
- Set reminders
- Send GIFs
- Get posts from social media

Basically any API that exists can be integrated with a chatbot opening a world of possibilities for making group chats more interesting.

Here is a brief list of some advanced chatbots:

- [Bucket](http://wiki.xkcd.com/irc/Bucket) built by the creator of [XKCD](https://xkcd.com/)
- [Hello Vote](https://www.hello.vote/) to help and remind people to vote
- [Literally Thousands of Others](http://bfy.tw/KiHX)
