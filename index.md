# Welcome to gellyy

## Build HTML5 Chat app

So the idea is to make a chat app that communicates with other users via MQTT

#### Introduction to HTML5

HTML (HyperText Markup Language) is the code that is used to structure a web page and its content.

#### Introduction to CSS

CSS stands for Cascading Style Sheets. CSS describes how HTML elements are to be displayed on screen, paper, or in other media. CSS saves a lot of work. It can control the layout of multiple web pages all at once. External stylesheets are stored in CSS files.

### Building the front-end

For the front-end we started with just a sketch on a piece of paper for how we want our chat app to look like.

(Insert my sketch photo)

Here’s mine

After that we started roughly coding where everything should be in the html file

#### Explanation of the code

Everything from the chat app is stored in a div with a id of chatapp and a class of nothing (with a display of none). The class will later be changed by the login button

```apache

```

### Building the back-end of the chat app

#### Introduction to MQTT

MQTT is a publish/subscribe protocol that allows edge-of-network devices to publish to a broker. Clients connect to this broker, which then mediates communication between the two devices. Each device can subscribe, or register, to particular topics.

#### Introduction to javascript

JavaScript is a scripting language that enables you to create dynamically updating content, control multimedia, animate images, and pretty much everything else. (Okay, not everything, but it is amazing what you can achieve with a few lines of JavaScript code.)

#### The ping and pong concept

A ping is a signal that is send by every user and when other users receive the ping they send a pong with their username and clientId

#### Explaining the code

This lets the chat app only connect when a button is pressed and not on startup

// app connect

sendMsg and sendmessage does the same thing

When they are called they get the username from the html page and publish the message that was typed by the user and then empty the input

If a message comes in it will be filtered for pings and pongs and if there is non of both it is displayed in the chatlog on the html page

```apache

```

This handels all the pongs that the users send and extracts the username and the clientId

```apache

```

Here the userlist is updated if there if a new user that is not yet in the list

### The final moment

Did I or did I not waste my time?

That is the golden question.

anddd the answer is…………………………….

YESSS it does

## Building a nodejs local server

The idea is to build a local nodejs server to host the chat app and store every incoming message in mongodb

#### Introduction to nodejs

Node.js is an open source server environment. Node.js is free. Node. js runs on various platforms (Windows, Linux, Unix, Mac OS X, etc.) Node.js uses JavaScript on the server.

#### Introduction to mongodb

MongoDB is a non-relational document database that provides support for JSON-like storage. The MongoDB database has a flexible data model that enables you to store unstructured data, and it provides full indexing support, and replication with rich and intuitive APIs.

#### Introduction to mongoose

Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node. js. It manages relationships between data, provides schema validation, and is used to translate between objects in code and the representation of those objects in MongoDB.

### Explaining of the code

If a message comes in this will check if its a ping(a call out for all online users), a pong( the answer to a pong with the name of the person in it)


| try{ msgObj=JSON.parse(message.toString());  // t is JSON so handle it how u want // if message has Pin of Pong in it send it to the PingPongHandler if(Object.keys(msgObj)[0]=="ping") { sendPong("codettes2022","Dylan's Server","cb_12345678");}; if(Object.keys(msgObj)[0]=="pong") { handlePong(msgObj.pong);};// pong value is an object!! |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

The for loop checks if there is a : is the message that will help is separate the name of the user and the message from each other

```apache
for (vari=0;i<lengt;i++) {

if (incomming[i] ==":") {

varcom=i;

varplace_name=i-5;

varplace_inmes=i+2;

varlogin=false;

console.log(i);
}

}
```

The substr function removes a little peace out of a large string this helps us get only the username in the variable name and only the message in the variable name

varname=incomming.substr(0,place_name);

varinmessage=incomming.substr(place_inmes,lengt);
If the message is a welcome message it will be stored under a login key and send to be saved in a collection for login messages

```apache
if (inmessage==incomming) {varmyobj= {  login:inmessage, time:Date() };

varcollection="login";

}
```

If the message is not a welcome message the username, message and the time it was send will be stored under there separate keys and send to be saved in a collection for user messages

```apache
else{
varmyobj= { name:name, message:inmessage, time:Date()};
varcollection="messages";
    }
```

This hosts the chatapp so when someone goes to the link this will serve up the html page and everything it needs

```apache
constpublicDirectoryPath=path.join(__dirname,'data')

app.use('/',express.static(publicDirectoryPath))
```

### The final moment

D id i or did i not waste my time?

That is the golden question.

anddd the answer is…………………………….

YESSS it does

Here the chatapp is hosted on the nodejs server

![](https://lh6.googleusercontent.com/HGsSuywM7dfvo1DMtIhLJxLyp4lKcz9-IaxrFxzXGfz4Ue0dcdDQmkiKOA-FBY_tA9_NwHEFpVnjdfdFy8zXup7-7s-uUXJFo6xvDqnf2M6T4YmdEPb6GH9wa2RK3uyBXBcORyS0y6gnPrxbXx22R28)

Here the nodejs server is saving the messages in mongoDB

![](https://lh6.googleusercontent.com/Db7L2l-cQgWFefJUhCEfKEakqfhCLxNMSoMWZZWzGzCDHwWwJuXFqHa9ZxgwdpuoT4s8TM3x101s9kdNfMDRc5cAEQNZ6zDj2d5A09IuQIAAtY2a0NLozS3i3F88qCossTNns-88jG8PBAaz0mLRK2U)

Here the login message can be seen saved in mongoDB

![](https://lh6.googleusercontent.com/eEJB8Ml5opX87s0Ds8h80AblDzdlEPEkEcpyfceaty23F6Cr0Cpk-CuitR4Ci4PtWvERmQDH0Ut9yF8kMAdwVDkh49oQ1Y5p4tzPl0gi5DLFMOtBtjahCBUd_1sF0Ht_mTDRiRX_4EsrwzQhIkHHX3E)

Here the user messages can be seen saved in mongoDB
