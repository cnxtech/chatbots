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

data = { "api_key" : "exiOXgECL7g582dV2Mt85vYZoSs1Kb0m",
         "q" : "cat",
         "offset" : random.randint(0, 500) }

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

data = { "api_key" : "exiOXgECL7g582dV2Mt85vYZoSs1Kb0m",
         "q" : "cat",
         "offset" : random.randint(0, 500) }

r = requests.get("http://api.giphy.com/v1/gifs/search", params=data)
url = r.json()["data"][0]["bitly_gif_url"]
print(url)
```

Note that the above code uses the JSON parser built natively into the `requests` library. When run the first time, this code linked to the following fun cat GIF:

![](http://i.imgur.com/0Y1xISa.gif "Lit Cat GIF")

# GroupMe Bot API
Text

# Facebook Messenger Bot API
Text

# Running Code on a Server
Text
