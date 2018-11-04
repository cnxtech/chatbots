# Chatbots
15-112 Optional Lecture
-----------------------

By: [Jacob Strieb](http://jstrieb.github.io) and [Zuhayer Quazi](https://www.zuhayer.me/)

Published on November 6, 2018

### Sections
1. [JSON](#json)
2. [APIs](#apis)
3. [GroupMe Bot API](#groupme-bot-api)
4. [Facebook Messenger Bot API](#facebook-messenger-bot-api)
5. [Running Code on a Server](#running-code-on-a-server)

---

# JSON
"JSON" is an acronym used to describe JavaScript Object Notation. This is essentially a format for transmitting data in the form of "objects" (think object-oriented programming). The notation is very similar to Python's syntax for dictionaries. Generally, JSON objects can be thought of as a text form of dictionaries.

Here is an example of a JSON object:
```
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
```
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
```
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

# APIs
Text

# GroupMe Bot API
Text

# Facebook Messenger Bot API
Text

# Running Code on a Server
Text
