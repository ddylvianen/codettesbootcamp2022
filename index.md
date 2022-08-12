# Welcome to Dylan Vianen's Documentation

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

```html
<div class="nothing" id="chatapp">
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

```javascript
app = {
connection: function () {
```

sendMsg and sendmessage does the same thing

When they are called they get the username from the html page and publish the message that was typed by the user and then empty the input

If a message comes in it will be filtered for pings and pongs and if there is non of both it is displayed in the chatlog on the html page

```javascript
function sendMsg(ele) {
  if (event.key === 'Enter' && toggle == 0) {
    var USER = document.getElementById('login').value;
    var userName = USER;//document.getElementById("loginName").value; // || "anonymous user"
    if (ele.value == "whoami") {
      console.log(userName);
      client.publish(mqttTopic, "Your name is " + userName + ", silly");
    }
    else if (ele.value == "disconnect") {
      var t = document.getElementById("toggle");
      console.log("disconnected user " + USER + " from topic " + mqttTopic)
      t.value = "disconnected";
      toggle = 1;
      console.log("off")
      console.log("disconnected")
      client.subscribe("", { qos: 0 })
    }
    client.publish(mqttTopic, userName + " says: " + ele.value);
    //alert(ele.value);
    ele.value = ""; // reset the input after entering
    // sendPing();
  }
}
```

This handels all the pongs that the users send and extracts the username and the clientId

```javascript
app = {
    client.on('message', function (topic, message, packet) {
      if (toggle == 0) {
        msg = message.toString(); // library delivers  buffer so convert to strig first
        console.log("onMessageArrived: " + msg);
        // if it has JSON payload do NOT add to chat
        try {
          msgObj = JSON.parse(message.toString()); // t is JSON so handle it how u want
          // if message has Pin of Pong in it send it to the PingPongHandler
          if (Object.keys(msgObj)[0] == "ping") { sendPong(); };
          if (Object.keys(msgObj)[0] == "pong") { handlePong(msgObj.pong); }; // pong value is an object!!
          // other handlers for control messages below
        } catch {
          document.getElementById("chatlog").innerHTML += "<br>" + msg;
          sendPing();
        }
      }
    })

```

Here the userlist is updated if there if a new user that is not yet in the list

### The final moment

Did I or did I not waste my time?

That is the golden question.

anddd the answer is…………………………….

YESSS it does
here is the login page of the webapp
![](loginpage.png)
and here is the mainpage of the webapp
![](mainpage.png)

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

```javascript
try {
    msgObj = JSON.parse(message.toString());
 
    // t is JSON so handle it how u want
    // if message has Pin of Pong in it send it to the PingPongHandler
    if (Object.keys(msgObj)[0] == "ping") {
      sendPong("codettes2022", "Dylan's Server", "cb_12345678");
    };
    if (Object.keys(msgObj)[0] == "pong") {
      handlePong(msgObj.pong);
    }; // pong value is an object!!
```

The for loop checks if there is a : is the message that will help is separate the name of the user and the message from each other

```javascript
 for (var i = 0; i < lengt; i++) {
      if (incomming[i] == ":") {
        var com = i;
        var place_name = i - 5;
        var place_inmes = i + 2;
        var login = false;
        console.log(i);
      }
    }
```

The substr function removes a little peace out of a large string this helps us get only the username in the variable name and only the message in the variable name

```javascript
var name = incomming.substr(0,place_name);
var inmessage = incomming.substr(place_inmes,lengt);
```

If the message is a welcome message it will be stored under a login key and send to be saved in a collection for login messages

```javascript
if (inmessage == incomming) {
        var myobj = {  login: inmessage, time: Date() };
        var collection = "login";
      }
```

If the message is not a welcome message the username, message and the time it was send will be stored under there separate keys and send to be saved in a collection for user messages

```javascript
else{
  varmyobj= { name:name, message:inmessage, time:Date()};
  varcollection="messages";
    }
```

This hosts the chatapp so when someone goes to the link this will serve up the html page and everything it needs

```javascript
constpublicDirectoryPath=path.join(__dirname,'data')
app.use('/',express.static(publicDirectoryPath))
```

### The final moment

Did i or did i not waste my time?

That is the golden question.

anddd the answer is…………………………….

YESSS it does

![]()

Here the chatapp is hosted on the nodejs server

![](https://lh6.googleusercontent.com/HGsSuywM7dfvo1DMtIhLJxLyp4lKcz9-IaxrFxzXGfz4Ue0dcdDQmkiKOA-FBY_tA9_NwHEFpVnjdfdFy8zXup7-7s-uUXJFo6xvDqnf2M6T4YmdEPb6GH9wa2RK3uyBXBcORyS0y6gnPrxbXx22R28)

Here the nodejs server is saving the messages in mongoDB

![](https://lh6.googleusercontent.com/Db7L2l-cQgWFefJUhCEfKEakqfhCLxNMSoMWZZWzGzCDHwWwJuXFqHa9ZxgwdpuoT4s8TM3x101s9kdNfMDRc5cAEQNZ6zDj2d5A09IuQIAAtY2a0NLozS3i3F88qCossTNns-88jG8PBAaz0mLRK2U)

Here the login message can be seen saved in mongoDB

![](https://lh6.googleusercontent.com/eEJB8Ml5opX87s0Ds8h80AblDzdlEPEkEcpyfceaty23F6Cr0Cpk-CuitR4Ci4PtWvERmQDH0Ut9yF8kMAdwVDkh49oQ1Y5p4tzPl0gi5DLFMOtBtjahCBUd_1sF0Ht_mTDRiRX_4EsrwzQhIkHHX3E)

Here the user messages can be seen saved in mongoDB

![](https://lh5.googleusercontent.com/L-KeHX91pxZsUcjqAnQpc8saMoEJGHRu6KC1N0XcP0fo8XvmrRUDKkNbgjq_Ig-789CuS_k48YhQ2RPj4C5KMNM7NO_Wgryec0Nnm8apSUHbpqPv4unN2XbHOoVfpPwvOk9GnFM-nxLMSHl1_r_1Q8o)

## 3D printing

#### Introduction to 3D printing

3D printing, also known as additive manufacturing, is a method of creating a three dimensional object layer-by-layer using a computer created design. 3D printing is an additive process whereby layers of material are built up to create a 3D part.

#### Introduction to 3d printing filaments

The most used filaments are PLA and ABS

PLA and ABS are both thermoplastics. PLA is stronger and stiffer than ABS, but poor heat-resistance properties means PLA is mostly a hobbyist material. ABS is weaker and less rigid, but also tougher and lighter, making it a better plastic for prototyping applications.

### Installing cura and setting up the printer

I opened the setup wizard and followed the steps

![](https://lh5.googleusercontent.com/tLiIM_dFPaoSNLQ7pHhTlvWDXMz5qJD4nHFK6vn8cV9AbIFp6nZoDLXC1ZN-mY0bS7qY-X9ZGvFkRy_iUzV_e9atijJJ1AIy1pqimon6Aq_NhXIXGiKqSUjdWrIlu7bKeRB2h-3vypcV04EYiGJR1t4)

After all that i opened the app and setup the printer

First i went to settings and pressed add printer

![](https://lh5.googleusercontent.com/Q3-qFu8823TJU1-k2PvqEIkYTTYcilMt8TjthuJpj9PmBgKnlrLBa1lkDfYeiSmXVtb1OpId6zLvQlHOeh6tn7Rrr0_x9SphwuWplHClm3epomp79xuEYadxiwuKFG0nHUa0AFgPTuD-4WnxcBhz4oY)

And then i searched for the anycubic predator because we are using that

![](https://lh5.googleusercontent.com/SnIxv4Qy_zRTsO0w6sw6kTOarMkS2ZhU3_wjp1QuiepKbDkMRhYQxc7jtPFEdYiwZ2ukAsvx7tmJM---EVutxYdbLfArwWLG_k-yZiw4oVouooaAbQT-84Z0a112xLCf7BqiIZv__pP7x-0XqOu4RA)

### Setting up the cura parameters

Most of the time you can pick one of these profiles

![](https://lh4.googleusercontent.com/_ERIrCqRVEw1dIM9KzIuOs2djRz4kba0NSmIkM7naiUWKPa3CeymW8IGKKtqqq-My30-LOOmnNQ7_0dQ-Fp_RLwfXcQT25v-iB_8S6OfgiXTY54BAZwtEHxeP9sRabcjgkIq-MNgkRUAtni-MUMdKmM)

but because we are using pla we have to change some settings like

Support: the support has to be everywhere

![](https://lh5.googleusercontent.com/RC7l2dGeVwydJTcTez2hiQYVjvSY4cjx8pGSkiyQ75aglSUAwkN4Kj9BftF36w1dXgJbHKKg2nQuTG2_Go0WWVJSSpe_IoLjupF0wRIG-cpFuwYBKgj96UhhSRwHTw-4Rzu7WhHp-J96TgXiNMMM9lQ)

Build plate: the build plate has to be on brim

![](https://lh5.googleusercontent.com/YXcT6QfvxFt1jCEv7P-GjJRvh4H3uKTfHJKeOTpVppOxT146nQ1isNYYgAViEWwM9P8DGGae88BP1Ce-QIdB_zyBtoRIjyGF5FZ1vhZQdzM2zYQU5CPT01JTgSJJ95TvqhmYwllE49pnJcwQ27I7Gk0)

Material: the printing temp has to be on 190C and the build plate temp has to be on 60C

![](https://lh3.googleusercontent.com/RcCDrCjDDUL7L1qD031ZzInjBelz1mMoc5k9KoqpEjaz-Qnsq9yQctE8JYPJOS6joUtM2k9nwkUwO8Ocks1Jps-c0PliZhar--cuc-XDwIMPKZd4VzBbJj_cY5bg_6BVKlo9Kbm9g1496ZQyLxka_BI)

### Creating a 3D model for printing with tinkercad

First i open tinkercad and just dragged a random object

And then i exported it as an stl file to use later in cura

![](https://lh3.googleusercontent.com/q6_HhLDAsUCaBwqEzEHzQ86AsKkQ0T6MtGBRLq4O_PFE3ZxWhdc4xZjcMgdiflm18lhKOx3Jc3XCkpySSE5VAZxEQOhQW77-LqoMA-rlScwrqj3gQBqw3BFFK1K4sE7IsK0aVBkHaMNFqhVXp9hT8w)

![](https://lh6.googleusercontent.com/p3wD9EDBvxBN0uv1NJEXRFcTh8gGGLSvw9dZ90wCllU3Zu6q3Bu2B2uv4UYiNg4cE8Z9I34IeviE3KFium5s23cuiTrrBKjfS6-3WkvmOF7ZwfnVtONg2mJW_9RSxcDEOVNCVCXL_tURgpjN0ikAbg)

### Making the Gcode

In cura i opened the stl file that i made in tinkercad

![](https://lh5.googleusercontent.com/vp5S8O-TeDE7KRFQtuf9u1eyDHnnqxeUnTB7w5d2uIPHqZiXeIyMbGtFK9JqoUrePuDpaZn34U6MTKrNRPrPuozYQ3UJJ8aP6TrjXAObu1yuK8W9KioTxNjJMyHlbTc-SdFj9g-WR0z0ONn1JPUpx6Y)

![](https://lh3.googleusercontent.com/1OT6xpgc-HI_1rEPRLCBX_lUm6NDgD2lplTWDc_TVzzTmfqhcIxUhCMaDF6NSW80h677nebreluyVDo7qg5dYj2IqxoL5OTJqR0DvWcE3sMK2Lzd-b464KPw6sspy4szoufbEfwIwCwon2e9BZ2Z33I)

And then i sliced it and saved it as a Gcode

![](https://lh4.googleusercontent.com/j513_dHTVsv2Vcz65Z_DTTufI__Ndrb4p_GcRD9LVcerKh4TmyGhWLJ7Um0JaBKKrdNN7bcn62P0FRHL2ZReTxR-I8UF6kY4iMAj0fXW5lQE04_8LnvNcCWk5FHTyBw9VDyfU59sfoxgejvbRKmyPQ)

## Busines model canvas

#### introduction to Business Model Canvas

The Business Model Canvas is a strategic management template used for developing new business models and documenting existing ones. It offers a visual chart with elements describing a firm's or product's value propositio, infrastructure, customers, and finances, assisting businesses to align their activities by illustrating potential trade-offs.

A business model canvas is basicely a business plan(Plan for successful operation of a business or startup that identifies Sources of Revenue and Target Customer Base combined with details of Financing) oversite in one page.

a business model canvas is good for pitching,  its Customer Focused,  Easy for Presenting and many more

Canvas were initially proposed in 2005 by Alexander Osterwalder

### The parts of a business model canvas

a business model canvas can be split in 9 different parts

and these are

. Key partners

here you specify who you need as a partner to run you business. The less partners you have the better because then you have less people to pay

.  Key Activities

here you specify what processes or tasks need to be done to keep your business running

.  Key Resources

here you specify what resources you need for your key activities

.  Cost structure

here you specify all your costs to keep your business up and running(opperational expensis)

.  Value proppositions

here you specify what kind of services or problem solvers your business has to ofer to the customer

. Costomer relationships

here you specify what you have to over that can keep your costomers hooked to your business(what sets you out from the others)

.  Channels

here you specify how your business is going to bring there product or service to the costomers

. Customer Segments

here you specify your target audionce(the people who are mostly going to buy your product or service)

. Revenue Streams

here you specify all your income streams(all the ways that your business earns monney)

here is my [business canvas](https://next.canvanizer.com/canvas/rBCFJIdPN3Fyr)

![](businescanvas.png)

### Pitch deck/Poster

the pitch deck is used for presenting or giving people a quice look into what your trying to do or doing

your pitch deck can have anything you want on it but it usualy has everything abput your project or business and it should attract people to your booth

here is my pitch deck

** instert when done **

### pitching

when your pitching your business you have to have a impact story about the pain your trying to solve at the beginning becuase that will draw the peoples attention and make them remember you business longer. use a lot of visualisations in your powerpoint

there is my [powerpoint](https://docs.google.com/presentation/d/12t8hK0dObHDi4-inl7LIgl4JUvSRfCgX/edit?usp=sharing&ouid=110256527228353879293&rtpof=true&sd=true)



# 2d and 3d designing

### Installing freecad

I went to [this](https://github.com/FreeCAD/FreeCAD/releases) site and scrolled down till i saw this  And then i pressed on the 2.0 windows 64 bit version and waited for the download to finish

![](https://lh3.googleusercontent.com/Bc1lUjh6T5TMyFO1FUMgJiF9hJWcHO9JWbXFkNkQ8yJFws6EUxQW6k8c3FhTBvWAKhjMq0XYHAnkYOH9SW32L497LcsLefmpA04PEJaloXF453Tv2zl0EXHievSxitJDKS4kec9O0oZj1FYjsYUegUw)

After it was done i open the installer and went through the steps![](https://lh4.googleusercontent.com/Y7N10hY4rs_f9GyKkD4tOv2Av6R2Uj9i5dqH1DPj-IMJkyq28I77_1VRMfrGYGHPMEQdUxf4yPVVXliPYIF3PZBQIm3hCvI93nQaZB1sUoeuVAGY5NnNV6xNGkpzBy2B2LsmIS6ch0thmk6XtnvCr8w)

## Making a basic 3d shape

After it was done installing i opened freecad and made a new file, after that i pressed on the section toggle and pressed on part design

![](https://lh6.googleusercontent.com/opjf49Phna_OdPh1gkqWXvylAsBClha2PfUd4BU62Z5836BO6ko4kZCrHmqSqW5r0OBnm6k52l3fF-BmaOU_KqkWMPHX-hZDPN5Bu-ShHUJ_qPuRcA12hjplm8LKG0_tPFuuM93E9etiXX5zfj2pwW0)

After that i made a new sketch

![](https://lh3.googleusercontent.com/NkwJP10bpw_t-W7vS3ijqS5MN9JJZ_XpdmcN3dqdDsa5eNgPI6KtN6kITlY-Tjfmh4spMxptXtJ3s5zoI3Z5rHdi5K6vcOt4BL4fEDi_KCUMSKL4i1juoYTovubmC48F6WcRI8e8_sVf4ITsKh7GgQk)

Where i choose the xy_plane

![](https://lh5.googleusercontent.com/RWJqxXFj50Vt9ih2WDABkqN-MsE-fShDWoQl0UM3C0Gm7M_vp31LGDoJdk4OV9HHWNoBSIAsg0mNib7tdw6Ie3NZYb5J8tYsOZwWDjiM-hkpPTufsTUo_sXk3zTUIehKWW_TaJgGbfzdd-Vst1faARw)

And here i made a basic block

![](https://lh3.googleusercontent.com/_hEhjjDy6UuL0_i8ePXj9x_nOWF5hHdPQ30v1_5TUb6PnYw4HsJpazwlXyDRWiTV69JqB8a9ouDk1rbJrGYXa2ncXohoX6m24IsaKk-EVrFSF51kEqQUZB_jZ-CZbvWRHcNZbaDnN1J8u5qrVegZoRg)

![](https://lh4.googleusercontent.com/e7S5EBSTuiz6Ce7bBwaeaBTq2L8n4k6XoY2yTOVARVLCOU1F2DbrzLlrrJ--L_Dg5LZ7M6IOXgNseDd1tHOlK5INNJ9IbkBcfmSvONIYEQ_jhEDfNMxBhzxX_1xvOZFXOD2yZyX2SF5z3Un-FZQcZJU)

Then i choose constrain for horizontal and vertical

![](https://lh3.googleusercontent.com/fFQoLQ1DW9EOk5l5BNSHq1m4o9nh0BHuBHW3alGeyo7thB-llcVES-fdz3S9KC1Cr4BldqJVVRi7CSvviXYBbkMSB_tz-NVs3eQdjiu__qK9JQmQzi-cVapLtVLTsKMNk030410fFyM0ep3c4XflARE)

And i set them both to 100mm

![](https://lh5.googleusercontent.com/goSIqPdViWPVONHFU2BZZisid8JxWj9UPPQSl61DyGL97-rtPcHy2XGsMvfFaCG2T3ePXw8tV8v5dAont9g8ucbzqO2Zj8yi1KGkWdfEHlhN1EBwVuDNezdZUyQB1tBYFqqGQZmayA3p7tRfrFl_RVc)

![](https://lh6.googleusercontent.com/tJdDDtwOFNEToHQeWDGlW5vD2CosPEXzztTj-4nI_mWfhxYuQSBy3oKQgWpR-6xlgJGMkK62Ubbhx_g8HrisPkBMvNMnTrTRFRf2keDRnMGhtU9ddARvSLg9amGNku5rpaUT43SPtoyNBb2K5S53WZc)

And then you get this basic square

![](https://lh6.googleusercontent.com/yZzyCnUCPcFibQtR7bnukRpawY5my7E7-LjHVVnKHnr51ScvRi8A6l44m5_0A4t9Gubp3iWSbwRRCyh25Of0O-BgU3888Sgcb7t858f2Bm6b3qrmSxVAxhZ_v-GA1DQfV85VjiDvgZds-qSY955dzb0)

When you have a square you press on Pad(that will make it a 3d schape)

![](https://lh6.googleusercontent.com/PACvLTHOoVKKBdCno-dNJNcx8gwkj6hqmVSkvlpBBC42ARuCSkVpuIzxaunqFX1W3Nc9sn7qD_UCKVQPbEVfzuPQZ52vn9S03ZRsUmA7qqaaDjlu1v2LM017YKb0rRjAFHXV5tWi8kFQl_gGR8iXKUI)

And i set the dimension to 10mm

![](https://lh3.googleusercontent.com/IYgV9BSpV6UZRUBwL4LGZzUVrjlNsN0DwKgQ4WOHmpuFfffGhvHx3HBTX8jB4Ya-Zm3nncJ1acUlbmpP-RLedUw1T4N8q0TROkIeTDP73HZAgryZYbUp27nSDBEuI0dLIAWqGBRztmDMrD5G10Cr9jk)

And when you do that you get this 3d shape

![](https://lh6.googleusercontent.com/sJQbnaBkijvy21G-nu-HUsEE6FkgTgn_nEoeZeCRywIV9ESyGBe5LCZDFZ2Z46_6oWznbu4kbt8JOuJRir10vUPgjs2EmMDguQxgy9T1PDJ3dW9iIlDqlW1apOUQbp1tfTroFv2qKQXdfMV4ceEh3sE)

### Making a pocket in the basic 3d shape

I made a new schape in the XY_Plain

![](https://lh4.googleusercontent.com/bmfMHC2eLMFxwcnCUZUqdlSE2GkuzCasEhBQWNjMhggUVF89lLU6OuB-HXR-0PjZUL5zgMW5NJTWvfzN9bQG9UYmSF3uZL2TqlXRVmK8CWIXtZ-HD4BEA9gtqd184QOlQNQhroKj_ONjHAmBnT3_ae4)

And pressed on create circle

![](https://lh6.googleusercontent.com/ETYxK0i0NQlUkkiFOuwI5S404i5o9Ezc04L0mPcs3a3n4ZarBdL7LD-BeAMTiD-OtPjsQxKXF7wLmeUY-_pT0Xvw0bOjWXfz9hxPGP_P10vJurz3rbOvsMRQA6sPzR8H9hSfp154o-nIu8HhtqGC9rw)

And added s circle on the 3d shape

![](https://lh6.googleusercontent.com/HqEkycLgtEf3vwBo8hdyVoHWAUBjg1Dn45dtpnCM1CeBxYr_oLFEEtu6RI8tJanVMyBwUDXuPOEa7zXlATXBMX0WJpIzerMTyPwINBtHT-3ToTcVm-j0hUr90C2kA5U44E5y-RX_s14zbwaBV_XSRVo)

After that i pressed on pocket

![](https://lh5.googleusercontent.com/OampRAa71IZrDhpH83jFPHeGFsaoKrAbAYtYEWj72wA55pd01WcjWgs2XaUBsfQqyiEkhQ_J66Hi66fDJSyJj5HmMv-m8nkhJ2h-AMf6x993WaPZfI2-nzEDJP2bLk0ZTR-YbzorooJh5f3sY3A-apE)

And checked symmetric to plane or reversed

![](https://lh3.googleusercontent.com/wK4cpOfZsfOC3nOOooHLVPDk_DmG8zN-Asxm1iquUhdBFJws0zIeBS0PT80SPn2pgxdE_vBEFh2X_4yiLehiEW8orvpLR0HJR6CVqMup6gZQzbmN7Nr97XVaz0Pkkw-n-cWREWMZL5k4oC7bdh22tQs)

And the you schoul have a pocket!!

![img](https://lh5.googleusercontent.com/3PF_-xSnT-AOm_OUdfzIhhVsseVmoRgnFrbXnTC1_JFT1VaBAOhUrG3CouGlkn3RECkBThMQQrkkT6cYr1gxF6bIlSEsMAL3bldM-yiwTCd3qpl3PTAbUEP4KuZC1HSGY3I0GMxPC1XtZenNp5xAuMo)
