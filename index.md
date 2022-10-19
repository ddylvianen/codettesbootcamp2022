# Welcome to Dylan Vianen's Documentation

## Setup Dev Environment for ESP32 S2

First i downloaded the Arduino IDE from the [website](https://docs.arduino.cc/)

![](https://lh6.googleusercontent.com/Uyyg45dXsrwT2kp47vUTU0-Los7nU2lLdbPpBYXEHDvsUPKlQsOhyYSdgymKPy-C1ohuQV-Ck96mbSu6W_asYdafrwtxPAsAfqDLS2tiDMEOKPSVrIgZAAtOqccQLOFFig8y51O0aBwWzQhd86_Yigk)

 And waited until the download was done and opened the file and went through the steps for the installation.

![](https://lh5.googleusercontent.com/zug7R1QXY0HkudPOXpNX_kqfrttyKaEudnFUUSmk5J8raL8dUOk7JAdoVuSG5z8DtAqWIWMaQ8ObfcjL6LSOTkUD7ZA0XytVz1wj8he6h0aqImrjR-uCNTbjbKAjjcuTFdZKsXkne0qPMtEuICPbWDU)

After that i open the IDE and opened the preferences

![](https://lh6.googleusercontent.com/QliM-ZB5c7FoSKOTtsz51tbskDPUHRepnOYXKoItLdOSrBk28Q_7X2cJpBEtQibXrfbohw_zS14ExXXlPD4BpB30jpFFs9yr4YRwRk1ulRk1N7Z-8xki9t7Ow-l5yOotmLIdlOk9xnvV2e0k_CFrh7w)

And added the link for [esp32 devices](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html#installing-using-arduino-ide) on arduino IDE

![](https://lh4.googleusercontent.com/P2-QWK1cpFHfuHSwHDQnsvl24oQow-thzkBC1S0GyiO31wXn8JkowSSAyxVKCe_swWk0h0mbTXWLznHKp9p3y4ckMoSadaQzyOlaHe9y-basiIHVGS7g90xQndcV7bHgNRbnwWvz6zh4JfSXUxeW02o)

After that i downloaded [spiffs for the ESP32-s2](https://github.com/me-no-dev/arduino-esp32fs-plugin)

![](https://lh4.googleusercontent.com/IOmO2s0Y9ZPBgrtQRF-vquXWfpsFO8JYKCYTx1fzb2FwNFnCioKPBzaELruLF7Tu1CAgz_PCVbEEg90MxsFZquRnysPHrGxJ6gODk4GE1LUjwSCrqATlusFZZMcfhUqasT86q9y51jzDBd9DqDI3MZo)

And i unzipped it in the sketchbook location

And thats it!

### Build HTML5 Chat app

So the idea is to make a chat app that communicates with other users via MQTT

##### Introduction to HTML5

HTML (HyperText Markup Language) is the code that is used to structure a web page and its content.

##### Introduction to CSS

CSS stands for Cascading Style Sheets. CSS describes how HTML elements are to be displayed on screen, paper, or in other media. CSS saves a lot of work. It can control the layout of multiple web pages all at once. External stylesheets are stored in CSS files.

#### Building the front-end

For the front-end we started with just a sketch on a piece of paper for how we want our chat app to look like.

(Insert my sketch photo)

Here’s mine

After that we started roughly coding where everything should be in the html file

##### Explanation of the code

Everything from the chat app is stored in a div with a id of chatapp and a class of nothing (with a display of none). The class will later be changed by the login button

```html
<div class="nothing" id="chatapp">
```

#### Building the back-end of the chat app

##### Introduction to MQTT

MQTT is a publish/subscribe protocol that allows edge-of-network devices to publish to a broker. Clients connect to this broker, which then mediates communication between the two devices. Each device can subscribe, or register, to particular topics.

##### Introduction to javascript

JavaScript is a scripting language that enables you to create dynamically updating content, control multimedia, animate images, and pretty much everything else. (Okay, not everything, but it is amazing what you can achieve with a few lines of JavaScript code.)

##### The ping and pong concept

A ping is a signal that is send by every user and when other users receive the ping they send a pong with their username and clientId

##### Explaining the code

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

#### The final moment

Did I or did I not waste my time?

That is the golden question.

anddd the answer is…………………………….

YESSS it does
here is the login page of the webapp
![img](loginpage.png)
and here is the mainpage of the webapp
![img](mainpage.png)

### Building a nodejs local server

The idea is to build a local nodejs server to host the chat app and store every incoming message in mongodb

##### Introduction to nodejs

Node.js is an open source server environment. Node.js is free. Node. js runs on various platforms (Windows, Linux, Unix, Mac OS X, etc.) Node.js uses JavaScript on the server.

##### Introduction to mongodb

MongoDB is a non-relational document database that provides support for JSON-like storage. The MongoDB database has a flexible data model that enables you to store unstructured data, and it provides full indexing support, and replication with rich and intuitive APIs.

##### Introduction to mongoose

Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node. js. It manages relationships between data, provides schema validation, and is used to translate between objects in code and the representation of those objects in MongoDB.

#### Explaining of the code

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

#### The final moment

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

![img](https://lh5.googleusercontent.com/L-KeHX91pxZsUcjqAnQpc8saMoEJGHRu6KC1N0XcP0fo8XvmrRUDKkNbgjq_Ig-789CuS_k48YhQ2RPj4C5KMNM7NO_Wgryec0Nnm8apSUHbpqPv4unN2XbHOoVfpPwvOk9GnFM-nxLMSHl1_r_1Q8o)

#### code and referencis

the link to the [site](https://ddylvianen.github.io/) and to the [code](https://github.com/ddylvianen/ddylvianen.github.io)

### Build UI mockups for your FInal Project and HTML Layout

This was my first sketch of the Visionary glove it showed the build lay-up of one of the gloves

![](https://lh6.googleusercontent.com/s0MGZxhn1ryPchSU3NozqyXI1YhqB0z_bNVnZodddBLKwuW33rNXkA3U08maIy_UEZE50Um4bElpoXjtaxCU0hnVOhgLCnEC9ex3pjahpWDU7Kr5Yau1pZ0VGG7DRbw6pbw49-os5q9PtQmkSwrlK_Q)

And this is the final version of my sketch

![img](https://lh6.googleusercontent.com/NJRFQVPBg6MVtyvtYE0eFyyZoCIgmmQUoJvsESvDswL_8aEScWil1afzlducznwReMp8d34QFvZSuRnE7FcePAKa8tSZGrvVtM_pJ7JxPjo28ObsLexw9dsxDZ0UChGoFqPD2YbIpsKTA_z21jJJznQ)

## 3D printing

##### Introduction to 3D printing

3D printing, also known as additive manufacturing, is a method of creating a three dimensional object layer-by-layer using a computer created design. 3D printing is an additive process whereby layers of material are built up to create a 3D part.

##### Introduction to 3d printing filaments

The most used filaments are PLA and ABS

PLA and ABS are both thermoplastics. PLA is stronger and stiffer than ABS, but poor heat-resistance properties means PLA is mostly a hobbyist material. ABS is weaker and less rigid, but also tougher and lighter, making it a better plastic for prototyping applications.

#### Installing cura and setting up the printer

I opened the setup wizard and followed the steps

![](https://lh5.googleusercontent.com/tLiIM_dFPaoSNLQ7pHhTlvWDXMz5qJD4nHFK6vn8cV9AbIFp6nZoDLXC1ZN-mY0bS7qY-X9ZGvFkRy_iUzV_e9atijJJ1AIy1pqimon6Aq_NhXIXGiKqSUjdWrIlu7bKeRB2h-3vypcV04EYiGJR1t4)

After all that i opened the app and setup the printer

First i went to settings and pressed add printer

![](https://lh5.googleusercontent.com/Q3-qFu8823TJU1-k2PvqEIkYTTYcilMt8TjthuJpj9PmBgKnlrLBa1lkDfYeiSmXVtb1OpId6zLvQlHOeh6tn7Rrr0_x9SphwuWplHClm3epomp79xuEYadxiwuKFG0nHUa0AFgPTuD-4WnxcBhz4oY)

And then i searched for the anycubic predator because we are using that

![](https://lh5.googleusercontent.com/SnIxv4Qy_zRTsO0w6sw6kTOarMkS2ZhU3_wjp1QuiepKbDkMRhYQxc7jtPFEdYiwZ2ukAsvx7tmJM---EVutxYdbLfArwWLG_k-yZiw4oVouooaAbQT-84Z0a112xLCf7BqiIZv__pP7x-0XqOu4RA)

#### Setting up the cura parameters

Most of the time you can pick one of these profiles

![](https://lh4.googleusercontent.com/_ERIrCqRVEw1dIM9KzIuOs2djRz4kba0NSmIkM7naiUWKPa3CeymW8IGKKtqqq-My30-LOOmnNQ7_0dQ-Fp_RLwfXcQT25v-iB_8S6OfgiXTY54BAZwtEHxeP9sRabcjgkIq-MNgkRUAtni-MUMdKmM)

but because we are using pla we have to change some settings like

Support: the support has to be everywhere

![](https://lh5.googleusercontent.com/RC7l2dGeVwydJTcTez2hiQYVjvSY4cjx8pGSkiyQ75aglSUAwkN4Kj9BftF36w1dXgJbHKKg2nQuTG2_Go0WWVJSSpe_IoLjupF0wRIG-cpFuwYBKgj96UhhSRwHTw-4Rzu7WhHp-J96TgXiNMMM9lQ)

Build plate: the build plate has to be on brim

![](https://lh5.googleusercontent.com/YXcT6QfvxFt1jCEv7P-GjJRvh4H3uKTfHJKeOTpVppOxT146nQ1isNYYgAViEWwM9P8DGGae88BP1Ce-QIdB_zyBtoRIjyGF5FZ1vhZQdzM2zYQU5CPT01JTgSJJ95TvqhmYwllE49pnJcwQ27I7Gk0)

Material: the printing temp has to be on 190C and the build plate temp has to be on 60C

![](https://lh3.googleusercontent.com/RcCDrCjDDUL7L1qD031ZzInjBelz1mMoc5k9KoqpEjaz-Qnsq9yQctE8JYPJOS6joUtM2k9nwkUwO8Ocks1Jps-c0PliZhar--cuc-XDwIMPKZd4VzBbJj_cY5bg_6BVKlo9Kbm9g1496ZQyLxka_BI)

#### Creating a 3D model for printing with tinkercad

First i open tinkercad and just dragged a random object

And then i exported it as an stl file to use later in cura

![](https://lh3.googleusercontent.com/q6_HhLDAsUCaBwqEzEHzQ86AsKkQ0T6MtGBRLq4O_PFE3ZxWhdc4xZjcMgdiflm18lhKOx3Jc3XCkpySSE5VAZxEQOhQW77-LqoMA-rlScwrqj3gQBqw3BFFK1K4sE7IsK0aVBkHaMNFqhVXp9hT8w)

![](https://lh6.googleusercontent.com/p3wD9EDBvxBN0uv1NJEXRFcTh8gGGLSvw9dZ90wCllU3Zu6q3Bu2B2uv4UYiNg4cE8Z9I34IeviE3KFium5s23cuiTrrBKjfS6-3WkvmOF7ZwfnVtONg2mJW_9RSxcDEOVNCVCXL_tURgpjN0ikAbg)

#### Making the Gcode

In cura i opened the stl file that i made in tinkercad

![](https://lh5.googleusercontent.com/vp5S8O-TeDE7KRFQtuf9u1eyDHnnqxeUnTB7w5d2uIPHqZiXeIyMbGtFK9JqoUrePuDpaZn34U6MTKrNRPrPuozYQ3UJJ8aP6TrjXAObu1yuK8W9KioTxNjJMyHlbTc-SdFj9g-WR0z0ONn1JPUpx6Y)

![](https://lh3.googleusercontent.com/1OT6xpgc-HI_1rEPRLCBX_lUm6NDgD2lplTWDc_TVzzTmfqhcIxUhCMaDF6NSW80h677nebreluyVDo7qg5dYj2IqxoL5OTJqR0DvWcE3sMK2Lzd-b464KPw6sspy4szoufbEfwIwCwon2e9BZ2Z33I)

And then i sliced it and saved it as a Gcode

![img](https://lh4.googleusercontent.com/j513_dHTVsv2Vcz65Z_DTTufI__Ndrb4p_GcRD9LVcerKh4TmyGhWLJ7Um0JaBKKrdNN7bcn62P0FRHL2ZReTxR-I8UF6kY4iMAj0fXW5lQE04_8LnvNcCWk5FHTyBw9VDyfU59sfoxgejvbRKmyPQ)

### printing something

i choose to print my name. after i made a 3d shape of my name in thinkercad i open cura and set the above specified perams i saved it as Gcode and put it on a SD card and put the SD card in the 3d machine. in the mchin interface i searched for my Gcode and loaded it. after checking if every params where good i pressed start and waited for th print to finnish, waiting a few minutes to check that the fillament does not jam or not stick to the bottom plate.

after it was finnished this is what i got (i lost the A lol)

![img](3dprint.jpeg)

## Working with freecad

##### introduction to freecad

FreeCAD is  **an open-source parametric 3D modeler made primarily to design real-life objects of any size** . Parametric modeling allows you to easily modify your design by going back into your model history and changing its parameters.

#### Installing freecad

I went to [this](https://github.com/FreeCAD/FreeCAD/releases) site and scrolled down till i saw this  And then i pressed on the 2.0 windows 64 bit version and waited for the download to finish

![](https://lh4.googleusercontent.com/jQYVJEVfDY3oZ1Wwpfcof49svi8Fh-ikgqNqXZssJ2M8oQAKKubzkOUZh3N_z9z9LETnDYSuNszgkpSC29fAmjMj6ZAP3YxUeuyxA24HGZVyVWYJyoj-bYjnecAKlRVi3CNubrrgYuw3cB_AvF1MD04)

After it was done i open the installer and went through the steps![img](https://lh5.googleusercontent.com/wVxtaTAM4td2INSfEzGIGkeOKqV4g0rqQJ5vENa9z1fZfSDAeGE_m0eJ3EYveUXsAbELoJlb_WgCrDaXC5-K9zOEMmHs_4fTeWps5Gp5bUaKqBqTgagOfuaR2srxmYEEetf5UCGRkBM0k3SeM_uAa3g)

#### Making a basic 3d shape

After it was done installing i opened freecad and made a new file, after that i pressed on the section toggle and pressed on part design

![](https://lh3.googleusercontent.com/0zliLIFOFSrzWqW5ImyC6UcFjv1UXVSSq20vAOZlF70clK3fgdNCr9FO_RP93Edk8wMaV15dW-SWFQlpLaogOl1ZJEPNPccRr8qoQzsJ-5ZglR4KVo2MKsLpLEC9DCsUUyxggEO0AAW-kJO-1Os3lK0)

After that i made a new sketch

![](https://lh4.googleusercontent.com/R5rECutk97Bdfacy7Exm9eKoxRhkxYHDye60sstTT5lrzla5M8WBXccYXNbQFGr2fkgZb2iQVWU6Cy8neAaq2Db194AvdcxpA6funwkoB88z2vqrlGr7tuNbw4cXfuSWlSPMG8FMK9So8z2tynCOw34)

Where i choose the xy_plane

![](https://lh6.googleusercontent.com/KXJAxzAQ4xPqSQXVKE92g89oD0K54GQhj0gNgBX1RGl-_JVsNXazHPVxDqr9bie8RUHV3TsxLDsr1mmXW7WtRX5_0OB9slC9MmTeTS0NoQ8arf2XjF_0LLkCTQDk0IeXcstJPlqB3gBffAAhXQ6_Zs8)

And here i made a basic block

![](https://lh6.googleusercontent.com/gVf_fV1NHAIz_2NBvnWdDCbP5J_vk5k_2Qh2QIgaO86_8bUFb8kSpaycpF_VIJ2tEwN3Dv2SsTFCs_2mUAKS1ixehSgPI01GDV8dqqaet3_PO6v9aQsVEAqocU7oic24l166d0Y0jrxWMTH118KwlnU)

![](https://lh6.googleusercontent.com/uf7OR6m0f1CztWbE-TI_oypU71aKVObWB1_Ith_l63KJmCx-7pUBJ2HSrdmIs8EE2iKM2vWk2sanPcpCg6mijDkPict4OMBW3lHXzrnFffN5D28M3PFN0VyJsk4NCOcm5YYQ77Wi53V0C85nAQFamrY)

Then i choose constrain for horizontal and vertical

![](https://lh3.googleusercontent.com/jgvT3ftSwO7y4j5FmSN20-Lzs2EX8mU79_hKqpTElGtXAFNUCdLVFo-9EslBrN_4-uvfl9vChExpfOFNNMqfTqvtMssHyPAto9VkN8_kFNySv-_0oOvUpqmbAFGoRol4q2CuIMbBJvJ7DBTbnWLzCYA)

And i set them both to 100mm

![](https://lh5.googleusercontent.com/7-U3RkokNNYpJwpz0WfAOSiaHy2IjOWPuRouVR8zDoDvw9NJsBB7EnG0gN16K2ppnWftlRcExyofvhnnQC1JnedextME2l5n08LnWJ4t0AOckG2vm6Opw5KwZHDa7d1ZwVZ2FkB4w9F5-lRcEp7B6mo)

![](https://lh6.googleusercontent.com/Q-HFLQ2-6o62Z3d8NyrWYESD_aZgyc08QLYqTERqy7BSsZNFwa9IRZgwqavkK2vaDP9mkTxB6IrxzrsoSae4TRmqAuHbaQvu91s9LcmrxYYGucrugnmY4KUT_9bbqzPWYLXu6Q0agMFTGmxKPoVSalc)

And then you get this basic square

![](https://lh4.googleusercontent.com/tAwPrSILvkE7dOsZcpdXzcs0JR_2dridVn0lkI52JBz43NR40J-eAoPHMf8j5bW5L5ktTdTbRp2vxjNY3Kz80-tTsF2ChziawOK88U5EfamIKDF5XrvbC8yKSArHhdi2J4ItQVXrCnJZBUlgiAg6DjI)

When you have a square you press on Pad(that will make it a 3d schape)

![](https://lh6.googleusercontent.com/sdcyFrQ_ByPXRx6c2EWVYTUs4S3t0dfDw0-QCeEtz5XB5QgWH8DctgdMITKDdzgj4dvNlHeBRNJiLle7ig-muJoA5yPRvUuDEdlTmtCNd8VXIYcHrGRseE86HRYiWq4Mrq_yjEaqGewa4teozeuno9U)

And i set the dimension to 10mm

![](https://lh4.googleusercontent.com/5GFprBneiB3j9MaerzZFD8iQz2iR9hCO4WCNs_gyrx5D_ZUfoqp2wMSe5206KOjKO4xjHPlAlQU7FBqdEmUVUYuW-1WKX4ki4rmRMx0Wz5uf-v4GoBtPepPNWkx4oqBw6CRqjfjRmtVExw_du3ZgVoU)

And when you do that you get this 3d shape

![](https://lh5.googleusercontent.com/1lguysUqI-8H9Ezhp6S7ElySErtP07yiMenDu_9y95CFiQUROtNH15jmNimRZBlU_4vWZpAqSo19fmMBCx61p5gBji4cpvS1EFDv-k0gdZwD200nPmY9dBVTPEnSyrHmO9ZpYuVxUtS97Bq1h0tEX4Q)

#### Making a pocket in the basic 3d shape

I made a new schape in the XY_Plain

![](https://lh3.googleusercontent.com/spTdeBKj2TwQERQwnalBKTJjTY4NNhMu0elGCOb_1vUVCYNA2JENM9qNYQPFXGJPMckAnygleh_TGMZxzTkHsPbAU4hxeLcSbVnZuNHaYLo-Povq5N8hiLItJHoWF_Im7QAVQdfbwgSls1_EJun-DSs)

And pressed on create circle

![](https://lh3.googleusercontent.com/7ZqbCf7_LEEaPVeKJLsOtYPYk-hihPFcBVl_IYyhoENuzd_ltvb57_hWVNG1_QQkRCOF65t9L71Xp7dMBhzY23RLGjT8xZwKUdN9FlHFGwZjpZp2f-RmT5-WpndS5Yjh70SB-cK2slRuoKwXHuqMm5I)

And added s circle on the 3d shape

![](https://lh5.googleusercontent.com/gpr4jZAMBYkR0qP_8C2r9Z2XA0KVGMZ4aHsN9K32Ve1xwKkyAd2D_6ElDoduDSyoblkUE5uXIeOiu9XnVCZsrZLaE3MD27y54aSHRacaY86OXpsJLFI4lHafQMCu5j_VX78F9QeliaeSQTs04Ji5-58)

After that i pressed on pocket

![](https://lh4.googleusercontent.com/XV3eQPfjuEUoTpxaa6uglQ7pfFPTtyrnGjatLy7ck_pftGLCGJo8wZRkBfWXZl1O6uqdAI6ic3ljN818skYhZSZTQEDC47p9cR3un1Cs6QPQL1TmE-XRVdaGy5uubJbVKxhtvb53hz5Dpp0rsZI1wTo)

And checked symmetric to plane or reversed

![](https://lh4.googleusercontent.com/QqNhf83i5-Ii3GMbOl6qJGx3iG_fdtNTiJZo5DafSXNo_8R34ZYmdmOqhhFZXMdH5So3_PwemcqF3zShVfO1KvVjOg4jB0-qdK2aqrGL-Iwbk-NQQx6SNjGHihS8Ez7qasIhVa3Pq-oPV1V8CkFbQKA)

And the you schoul have a hole!!

![img](https://lh5.googleusercontent.com/shvI_u8BJhX8l78i3FBtuQxchgEHsxa8C-hLmOgv5Dczk4kZzSv0rE7NHWXEc8Yk9BnJYa-VM3b6hfHNaBrgW-uUUT28jcuCkXntiqDLdv5vn2zGqIwspo_akwiJ4orja3GwTd9gMgJPhZbOFISXXuA)

### 2d and 3d designing

#### Making a basic 3d shape

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

#### Making a pocket in the basic 3d shape

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

## CNC

##### introduction to CNC

Computer numerical control (CNC) is  **a method for automating control of machine tools through the use of software embedded in a microcomputer attached to the tool** . It is commonly used in manufacturing for machining metal and plastic parts.

### making the Gcode of the freecadshape

First we create a job

![](https://lh3.googleusercontent.com/O3fQp-8zoI6WVtHebBIkA5y9qABDe1mdKlq_HEAE5O0J4jrCYhb3LbTx-ns8WLQeEBrbfzKa1dI4PvcblOSNPbutk66XTOTC6eXMheB3eJdGOBZJesYil8yQ0gETaXyLwoNs_yJU6bqeU90tnZmaklE)

And select the body

![](https://lh3.googleusercontent.com/xsObYyLbyg4JWU0YSez0UVwl6qX9kna-yIgaWs5pr8rV486WUqNd8mesDGchWVUWbh6IE27IcJz2Bx0OTlM65_hmnm_CmKL-bC4ySQeCbzAUqBkvljuDHCH99JV6tg7hmNcMIwuB7CdV4EZPOHYMYKQ)

Then we make the outer box a bit bigger then the shape we already have

![](https://lh4.googleusercontent.com/C1hAfgHkVNU4in3gJkISJlI5SDv7F3qJxnS8bbG1f6-9qdkNLL685vx2D67nfgLTquCeIQBPndBlXCN4J1p2sRNRXrC-jRmcdA4Hm8ySPo2Mo0lvC4HZkDHmwImUak6BFXoQlsS4yj3z881D26ny5eA)

Then we scroll to the top point of the shape a press it

![](https://lh3.googleusercontent.com/fBdckqbPYYt9AQOXlUBMthZa-alVLbgNg1sZTG1ZY6_ffFpF9o9_nWV6uS67d_KKkQBn7i1y7BWve6XSh1GV-Rv_ZUJVSAnF2z6MyN8v-mGFBuP4Fb_6s5_4hBASTtbj3CSYE7KyBmFxauZOsK1Qnjg)

Then we set the origin to the top of the shape

![](https://lh6.googleusercontent.com/zgR0EASTqCNg-wEDULR41gdKBYwutffctd3r3DQA06OilaVL24po2X7I-09079vBivWF4yl23h18MlshzxfYnr6P6GSQvf1q5svasCT7K91wtvrRa91HiA8XiWWKlWLxsfBKjxC8aIc69InpoKTeyeI)

And then it will look like this

![](https://lh4.googleusercontent.com/SzV-HPEG_5D1zxSbgQwTtPoSmIK9-XPSsqUoERulqo95KQb_Qwv8cBE2TnnYaZQg921Yrzh6FR0rnb1KXG3L8Wq0rGqKuVgRol_YHheiSs3fx3wHLhyjbmmgw5XAeqBOxvxL1QQjnvNXQdmI4l1wJsg)

Then we set the output up by selecting the processor(uccnc)

![](https://lh6.googleusercontent.com/SyCqcx1TtISBs9121qXUO5oFAe0jHzGktkp0Rq6jMiOco4-J33_eUS-e3bQ1QVsBcvJWLQ6MmxUTuZ0z-NThkSLKovBOkvehabIjMqb6pQztvwwkY1ZZwfB6GyLBfllVdrFfv6JkaStTkcHtFDi-lX0)

And writing the output file

![](https://lh6.googleusercontent.com/P46BV1mSsICp9cZnSzj1z0qpAityNy90PrMe7kfe4KqE6MHAwFQhGTW9K8PG17rFJtcM7_sqltNH-b8nM5NDYNvLO0MlBAiMAFwkOUQkexCKdWk2dDd5fYG9tWYD4BecERqxyGb91BwVOCMEfuxfeZU)

After that we go to the ToolBit dock

![](https://lh4.googleusercontent.com/BL5hea3k2A6NSxtAysZYEm_toSR0_CfDRRqM8rd_oGVhqzEiOL1ZvLZt_5XdnQlRJ14g8RzgojbN99CGoy9_bzrgJuuY-gls76eEO5YSRi_TrChgh-pxu3e4fz8wp7PaMwEwP7LUofYkkcrzvOhNIKA)

And press on the 3 toolbits

![](https://lh4.googleusercontent.com/R22FbbbqNckh8xX33XrKaI06fF0qb33YQmlo89xbGYCbTgoMnlOM35a5utxT0dMl_eZJFAMJoaRSqw5ZRdaCxeX9Mg1Rius68fCkuSV7FV0y3QHUuuANO-wYG9C8zUzVItj8hEigsS83-U-nDbZHnrI)

And then of create tool because we are using a another bit

![](https://lh5.googleusercontent.com/fZndkEZC1ZPsAbWsWJua_sEs5x5zPme8nouyDX7a1l4VjPQU4ffOoCDuy6KMF7n6Elo7clywIm0BycfnUELKgMqbHCnwGd3rrRemg3I2ToLvw2TJaEX2UtUMgAEmL7Zykk4pKpFFm1ME-ympe5AR48U)

Then we choose the endmill toolschape

![](https://lh4.googleusercontent.com/0ca0YB1HwXDaxbMtbdnaBijFQuWhYG34pMYS1EnYPhzcOR9fEC3kT8lax85yx6W7UAiQgdFNVCH5HvmS2Mrk3DspebZZTl90GhSV1c52dx0Vdg6m4vLqfgEYE532vj75oKXxpohYWDNU-n65_TP6txo)

And give it a name

![](https://lh6.googleusercontent.com/EEo04J54rT8ubjvcJNh9QIwyi6EKidI5COlXdGAR-Xf_v9dzYUWGMvsV-f0luJbStwv16i2RokShq9Qb6bPoUKMm6cY_fiTDzvgLAmL0jhi1aVy-pN1CuTXWVjHy5TH3OMpzQTkPf7QU4BrZNhroyak)

Then we have to change these stuff as you see here

![](https://lh5.googleusercontent.com/F3wNkxLketAE8DXrDhpOzOewX5WIwNDQ_T5pwE28TMYNJiqzNtGUaUwM36njwTtLKMXauE-nBqEW89HnIICCkF_INXXViAZ_-d87qkwe0xge_Ycu8DlpPGg4DdHnIvCl_bc-0wTHU3R2TsN4M-nPsyQ)

Then you add it to our job and dubble press it

![](https://lh4.googleusercontent.com/Lg2voBsbKvBDx38jTtCyWIeNyX50SiGWCT2oEExOSyVaza2X0BPpkPlKw2zhNcbBSMgxY6Pd_18_qBtmitLgLuTAaoP2OuSgKE7M9inQN4ytVVftyUUwtraLfVv9FvziN7gV42SNKK172f8bIoR8cRs)

And change the feed and spindle of the ToolBit

![](https://lh3.googleusercontent.com/iXfKH1GyW_ujQwtc3Az1XVTE8MQnWyOzusFtiLlUaz7haLYZQ3muVX9gqgGKAzudDC-3_C-wnXXm92GSmYu75VObSeJq0A8ExmUUcktNl8fQkbMxrix2Y7-7g3gZj3uMpSCEfZaFXuf6IKLYuaqxVLg)

After that we press pocket schape

![](https://lh5.googleusercontent.com/tQU5Iyq7ly2MMg4UZQtyaZKlNkRbccXcfmX2U8oxpokcBGEZGlTrB20KPCun9HojiQJUDXX1ulMAOVozAXr8Qns5KzsHon18dmS7WOfqxrzNyZ4_LA85KeJw0v9kq9NuRw7BTDMPvhtl9B9bDJPQhU0)

And choose the outline of the pocket shape and add it to the base geometry

![](https://lh4.googleusercontent.com/eD4XhxjpORRlMY13i8vZWrN_anqUD15b52-Fab_E7N4tjsoyTT2W-Ltq8A4K_JcHzAnCagwRAHDqKkSEPh5pvv06paHbz2sWVRh-ujWQLW8DpiMk4hLvC9cCCg6eQVe6XTxf6GhPBhcGlm5nu_meqFc)

And then we change the start depth, final depth and the step down(half of the ToolBit)

![](https://lh6.googleusercontent.com/pyZy9umO_ut92-DLEm-wp9l9fVPt_W0GqDsJDP_Ry4me7DsgvuCUqaGdnOXW2PC_HagSzoo0kPKc1kOm2RnkrLJZnpDT09LbCB_Xq0Rn5BXGyr24tUlY7meqg1HHxg9i6Vm3B8ctpf0CaEBFqj0l3Qc)

The cut mode should be conventiu=onal and the pattern should be offset

![](https://lh6.googleusercontent.com/_KLjdUfOW7-NCRjf_eKWg9rhlQxT9ZZ6BfR8-f4Ndos9QzA9XU_CpndH5Kb84Y8k9DaCidSOUu5XLrWQOMUAht7LuOwMrWV70cVmsf9-79SVuGlqalqXKzBTxjHy5h8AaSthq6fcfbquiBIgtq2NX-c)

And Then we press on profile

![](https://lh4.googleusercontent.com/wWSz4zq7yHFwNQ01-aoRBg4wgVyGgBqgDQJPwFVk7sZ6D1Smth_7uimXcYqmm3x4Sz1LN1G0FTkrlAeTtgEhy88ozIUmrIMQAqO6NyRAvzqA2_oj54DsuWs4yXRJZFfk8-otnS1R5qN_GppkaZ5qgeM)

And then we select both hole shapes

![](https://lh6.googleusercontent.com/-Dfw1fy4gsVeiTU7koh5gl2jQOA9yGhEXQ2hBwtbnFfAjGclPYEVmmmmz91IKud3dWf3qMaMmvMvBtRxglSDddsxZCpNjRRd479vtxgorxBX5rBWxGpjNhGkGEz5z80U9JMPSyU05ci4htCWdlTZid0)

And make then inside cut

![](https://lh4.googleusercontent.com/dI1ixr4EzcOUn38gOt0f3I_O60ngxfOXdZo_cdh6_lZWBnHwtK9jl-hRmokDnCzzrCTnw-44mFuQh-b4Z0v6Ukh6zatnVpq4whjAWOY6WwOT2pqkq0L2En1q2hd5AVIPD5JiHDC6QIZv6yrTQRWt1zw)

And then we simulate everything

![](https://lh5.googleusercontent.com/dbFcGkwJv3BCutFpYEHTJNa3WnarJ_R2nTsan3AMKKNeJRplwqpnaYBQ_0qErlnFkj3V8cUGEhPY9uQ6sSlBoQ_hEv2QZniglo8alWsWmRaRwgilOTCEVvoopCtGanr7th8GsYpxAgJh8AfRZvlbqjc)

And it should look like this

![](https://lh5.googleusercontent.com/aN_NQPasIOXCI8MDaieTiTzf5tACp2wL4yr8hf6ZLg3S7JtZv5yJaygc0YSG9zLxX8vhvCQQLBqu_eCy8WamHNgox9lwfG4MSlRxQ1_A-3Hft_tOKeb-I-t8oNizzkzHsyCddf1fpxj8t5vbK1MfZac)

![img](https://lh5.googleusercontent.com/m5grUCVFSJk6rnqFfpw4SoLN2yjhaIw-2jyCg_uMLha6IPuD2_Wu7l_CGeTK04gReuT0GmFS2ynFq2Sd5w4W1HItI-GMUyGGmtpGsmMoN-LDXrWuLXsdwaewU0NRjx2TS-MIg-TNiGQ9jFR91l38U_I)

### cutting out the cnc Gcode

for ym final project i cnced a hand that a drawed in (a drawing app) and followed the same steps above to make the Gcode. after that i loaded the cnc software called stepcraft after i loaded the gcode and set the xyz origen and started the gcode. it ran without problems!

(i could not get any photos because the stepcraft didnt work on my device BUTT i maneged to get 1 photo)

here is the finall look at the Hand!

![img](glove.jpeg)

## Lasering

##### Introduction to lasering

The use of a laser beam to cut, divide, or dissolve a substance, or to treat an anatomic structure.

### making a laser sketch

To make the laser sketch I used freecad.

First i installed the laser addon for freecad

![](https://lh6.googleusercontent.com/LtR1sKBjuozbZQCfh0EcdxClJyfgj3ao_0BsixoP1Tnnss5MHwZAVn-Qm0Et0qSRdyTFROhiYxA0AEyqSjBWqLvNGUvfqheLpX-VEhI1SFXAcwwhqnvUOC8JH7UE57U0itXKQhYaHFD9dQzQSMTjxUJKEYmRMJgnkXDuFnTcFapwGKprij3sN4TBJQ)

![](https://lh3.googleusercontent.com/VJMBqNIfvalwZCjjhjGm7XfG62l4PNkCFiNPdd7HtsoU6RZ9INs6P7eXkfu_KfathUHfqRIASKB-uFnvT5aTMEpjFVqVnAKjFLNZkrSWyu-sdM1YoKebn6c-2aO0TUkbDP6g9S3DOVMnhaZQnsTfCnViArBX1NjFphRBulymtyniAOhnmYu9vkAcRQ)

![](https://lh3.googleusercontent.com/-mUp62qXNXWbrn3jBFLWZjsYouF7RSVOAWNrbbbB2_DeKZKxXus0U7Jiyx2r7Fz907BUfmJ5ENiS64Ak1OR-Co9wOu7B6RIkB85QBVJiftHMecP-iw1Zn8T-rh9fnTeboicnhSDYFUHqDIeN9cOy21mI0r-seIErYG8ZrYOOG7LrjpQGx7rmO4__5w)

![](https://lh5.googleusercontent.com/RMo0n1p5mHGtM1AlFcwl8hs3PSfqAsDPaIRvrvxZLUPK1bSykmOQzBJ-EQN7a9wa7Jqko2aa2MOZTVflByeQVIWvBn6Wd7ldpAkxwzg4lmd7Uq0QUyPH6BzLs-Xg6BqLKb0ZLvf0xrt7ZD_BtDLtub-vl5I-XFR5PZcyjowV-6xRHdNYjL922z7mHw)

![](https://lh6.googleusercontent.com/DiJImXuyDtW1L6kDAg13Mm7_KjlK4Ro2yOosD9MOLebqT6jEc8LSCKwckct_orlA38e1GTMdT914CQ72gE8bU6nI6SWt0lmNbBboKl_1M065qDOEKfhNdJYAZhUO87DKGo0kYgsL4YXyDg0qpt3z0PHaHJiK7guoDGqU8txY7Dqd0C01W809vZdUCA)

![](https://lh5.googleusercontent.com/sT8n7vnq0YzL3P-sYYqaQcry1pZ_bLbcdB838QQEsOoNTrnMrUvVpNQDrV2lUvloP-SuMgK6P_OmAYY80mhdG9-lYLmHMzC0RmPXMANX7nLGTGzDKPlE_VUHGEvJIifP97cZumotx7dGFO90RwqLwrxHOzvi_lUt8T1-rhtJ_6NrFXYJFJA2ZU3zuQ)

![](https://lh3.googleusercontent.com/-AJ94I3cBQoWiedtCIc1nree0ksbForqZrKfHcnsOxUXNkASGBMz8fWFvoImXPjMebCTV61uAytOUhlEMnL6V-dRi9ROB0juxAeoyADisgz1zw9B913t8irTueY5jsHp0aQEDFwpVNVzfEI64RjXOg4X7ZxTmCmzVr3RPbP-kk8PwVLXWJAIyqyFYA)

![](https://lh5.googleusercontent.com/caTiR8hjELQbW7ghSvbq-JcHs87_J5NFhVcwh1riB43uifFS0COh1O_3XQfCADiAMJy8hZzK5R90qkHhfryq_9SRt9RcPSHiBm7R033sBduws9aieg6uHLKZ5EIkL47ZsL5ZZdxx2I05xEY4_3x43dW1eAeEhkNVokcsx-qZu7E-v9Z7fkngYUX4jw)

![](https://lh4.googleusercontent.com/tFFZb1mNsbkB4R16BbJoUkaNzbE_vACAUoGKgVo8Q33rS4hghNPHM1grnJwm7qwcc2b4F-c7cxaEzvF-crUBYGH8118-MGfm72KYG67oyd4GPIXoqEqss66Iq6PkpY3uOmOzQrZHxaujqBrVo-3UTdnme_P-EjXdmo42TssrwcWxsDGIcEatuVvaPw)

![](https://lh3.googleusercontent.com/s95iohgLe-R8JNn_mCJDGv39BHCvu2AIsLRmXnYKRT-2MelvBuJco374lHCWIojIBpg2m3v5ddOqZS6D69hvCdQCpqE7CIzubk7W4rxhiJCSyLY8hdjTNjZzxfW0gWRXES7XeIA9L1U-k8_gdbBlIBKodRPPOjWTdoFZFgwWNeYy7gs2FqxxZg2dgg)

![](https://lh5.googleusercontent.com/QPjli2BPXrm6Xj5pgodjI9a0r-bZR55owAEXvhSBczTxRyi8kjvbD6UHxd5gWljHGBbLAxGE1ic776KsbiOhy0m8POwrMSfGBKsPofIpa_ZGZ4lnTgT6ZVbrfajyq1bkmbc8Koj1sh9ruxrQ-xNFWh47Hw05zSCkcwfSG1-EWsG7IyHGeY43At-i6Q)

![](https://lh4.googleusercontent.com/Z8n_e3MczHbxFQSXzRP8Uw9H0ZiPHNFxiex4oKSHSX4BssaUuURv8f9_vSx_Mcn80Szvm10CiDPb72xX5QtftewETj5FBy177umaqDtsUa3svV4K_R9Kwp3elVbz0A8UrYmbUdPW5Dm3EuBPPwvBoSiR5wukmdp8BeQLoPlQwRX87wB7T_msYz0PNQ)

![](https://lh5.googleusercontent.com/BTD8djxt3m8nFjOyf9dt12HhJqaHOkGFaPXlkAPBBYSdqgmmFBM77h_8DcfmDyqpf50I2h80fKKXMWCvMpQHmdsEAvBhw8ZM4Re2HxMLxLZuw_iVbX98lDqo3zayn0kdi_G5Hw9-0bU2Irb2LMIcEqNEcM6U_e_GhFlTCtIOT8_U9Lj2pY_1g-UgBQ)

## Busines model canvas

##### introduction to Business Model Canvas

The Business Model Canvas is a strategic management template used for developing new business models and documenting existing ones. It offers a visual chart with elements describing a firm's or product's value propositio, infrastructure, customers, and finances, assisting businesses to align their activities by illustrating potential trade-offs.

A business model canvas is basicely a business plan(Plan for successful operation of a business or startup that identifies Sources of Revenue and Target Customer Base combined with details of Financing) oversite in one page.

a business model canvas is good for pitching,  its Customer Focused,  Easy for Presenting and many more

Canvas were initially proposed in 2005 by Alexander Osterwalder

#### The parts of a business model canvas

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

#### Pitch deck/Poster

the pitch deck is used for presenting or giving people a quice look into what your trying to do or doing

your pitch deck can have anything you want on it but it usualy has everything abput your project or business and it should attract people to your booth

here is my pitch deck

![img](pitchdeck.jpeg)

#### pitching

when your pitching your business you have to have a impact story about the pain your trying to solve at the beginning becuase that will draw the peoples attention and make them remember you business longer. use a lot of visualisations in your powerpoint

there is my [powerpoint](https://docs.google.com/presentation/d/12t8hK0dObHDi4-inl7LIgl4JUvSRfCgX/edit?usp=sharing&ouid=110256527228353879293&rtpof=true&sd=true)

## Lasering

##### Introduction to lasering

The use of a laser beam to cut, divide, or dissolve a substance, or to treat an anatomic structure.

### making a laser sketch

To make the laser sketch I used freecad.

First i installed the laser addon for freecad

![](https://lh6.googleusercontent.com/LtR1sKBjuozbZQCfh0EcdxClJyfgj3ao_0BsixoP1Tnnss5MHwZAVn-Qm0Et0qSRdyTFROhiYxA0AEyqSjBWqLvNGUvfqheLpX-VEhI1SFXAcwwhqnvUOC8JH7UE57U0itXKQhYaHFD9dQzQSMTjxUJKEYmRMJgnkXDuFnTcFapwGKprij3sN4TBJQ)

![](https://lh3.googleusercontent.com/VJMBqNIfvalwZCjjhjGm7XfG62l4PNkCFiNPdd7HtsoU6RZ9INs6P7eXkfu_KfathUHfqRIASKB-uFnvT5aTMEpjFVqVnAKjFLNZkrSWyu-sdM1YoKebn6c-2aO0TUkbDP6g9S3DOVMnhaZQnsTfCnViArBX1NjFphRBulymtyniAOhnmYu9vkAcRQ)

![](https://lh3.googleusercontent.com/-mUp62qXNXWbrn3jBFLWZjsYouF7RSVOAWNrbbbB2_DeKZKxXus0U7Jiyx2r7Fz907BUfmJ5ENiS64Ak1OR-Co9wOu7B6RIkB85QBVJiftHMecP-iw1Zn8T-rh9fnTeboicnhSDYFUHqDIeN9cOy21mI0r-seIErYG8ZrYOOG7LrjpQGx7rmO4__5w)

![](https://lh5.googleusercontent.com/RMo0n1p5mHGtM1AlFcwl8hs3PSfqAsDPaIRvrvxZLUPK1bSykmOQzBJ-EQN7a9wa7Jqko2aa2MOZTVflByeQVIWvBn6Wd7ldpAkxwzg4lmd7Uq0QUyPH6BzLs-Xg6BqLKb0ZLvf0xrt7ZD_BtDLtub-vl5I-XFR5PZcyjowV-6xRHdNYjL922z7mHw)

![](https://lh6.googleusercontent.com/DiJImXuyDtW1L6kDAg13Mm7_KjlK4Ro2yOosD9MOLebqT6jEc8LSCKwckct_orlA38e1GTMdT914CQ72gE8bU6nI6SWt0lmNbBboKl_1M065qDOEKfhNdJYAZhUO87DKGo0kYgsL4YXyDg0qpt3z0PHaHJiK7guoDGqU8txY7Dqd0C01W809vZdUCA)

![](https://lh5.googleusercontent.com/sT8n7vnq0YzL3P-sYYqaQcry1pZ_bLbcdB838QQEsOoNTrnMrUvVpNQDrV2lUvloP-SuMgK6P_OmAYY80mhdG9-lYLmHMzC0RmPXMANX7nLGTGzDKPlE_VUHGEvJIifP97cZumotx7dGFO90RwqLwrxHOzvi_lUt8T1-rhtJ_6NrFXYJFJA2ZU3zuQ)

![](https://lh3.googleusercontent.com/-AJ94I3cBQoWiedtCIc1nree0ksbForqZrKfHcnsOxUXNkASGBMz8fWFvoImXPjMebCTV61uAytOUhlEMnL6V-dRi9ROB0juxAeoyADisgz1zw9B913t8irTueY5jsHp0aQEDFwpVNVzfEI64RjXOg4X7ZxTmCmzVr3RPbP-kk8PwVLXWJAIyqyFYA)

![](https://lh5.googleusercontent.com/caTiR8hjELQbW7ghSvbq-JcHs87_J5NFhVcwh1riB43uifFS0COh1O_3XQfCADiAMJy8hZzK5R90qkHhfryq_9SRt9RcPSHiBm7R033sBduws9aieg6uHLKZ5EIkL47ZsL5ZZdxx2I05xEY4_3x43dW1eAeEhkNVokcsx-qZu7E-v9Z7fkngYUX4jw)

![](https://lh4.googleusercontent.com/tFFZb1mNsbkB4R16BbJoUkaNzbE_vACAUoGKgVo8Q33rS4hghNPHM1grnJwm7qwcc2b4F-c7cxaEzvF-crUBYGH8118-MGfm72KYG67oyd4GPIXoqEqss66Iq6PkpY3uOmOzQrZHxaujqBrVo-3UTdnme_P-EjXdmo42TssrwcWxsDGIcEatuVvaPw)

![](https://lh3.googleusercontent.com/s95iohgLe-R8JNn_mCJDGv39BHCvu2AIsLRmXnYKRT-2MelvBuJco374lHCWIojIBpg2m3v5ddOqZS6D69hvCdQCpqE7CIzubk7W4rxhiJCSyLY8hdjTNjZzxfW0gWRXES7XeIA9L1U-k8_gdbBlIBKodRPPOjWTdoFZFgwWNeYy7gs2FqxxZg2dgg)

![](https://lh5.googleusercontent.com/QPjli2BPXrm6Xj5pgodjI9a0r-bZR55owAEXvhSBczTxRyi8kjvbD6UHxd5gWljHGBbLAxGE1ic776KsbiOhy0m8POwrMSfGBKsPofIpa_ZGZ4lnTgT6ZVbrfajyq1bkmbc8Koj1sh9ruxrQ-xNFWh47Hw05zSCkcwfSG1-EWsG7IyHGeY43At-i6Q)

![](https://lh4.googleusercontent.com/Z8n_e3MczHbxFQSXzRP8Uw9H0ZiPHNFxiex4oKSHSX4BssaUuURv8f9_vSx_Mcn80Szvm10CiDPb72xX5QtftewETj5FBy177umaqDtsUa3svV4K_R9Kwp3elVbz0A8UrYmbUdPW5Dm3EuBPPwvBoSiR5wukmdp8BeQLoPlQwRX87wB7T_msYz0PNQ)

![](https://lh5.googleusercontent.com/BTD8djxt3m8nFjOyf9dt12HhJqaHOkGFaPXlkAPBBYSdqgmmFBM77h_8DcfmDyqpf50I2h80fKKXMWCvMpQHmdsEAvBhw8ZM4Re2HxMLxLZuw_iVbX98lDqo3zayn0kdi_G5Hw9-0bU2Irb2LMIcEqNEcM6U_e_GhFlTCtIOT8_U9Lj2pY_1g-UgBQ)



## Final Project (Visionary Hands)

##### Introduction

Visionary hands is a sign language to speech bridge built inside a glove. It is a project from our HackOmation IoT Challenge 2022. The aim is to translate hand signs via strain gauges to letters using a library of known signs and an AI engine for improving detections.

The next iteration involves combining two gloves with accelerometers to also capture  gestures and convert those to words using inverse kinematics of the human upper body.

### Still to do:

* Finish the AI model for the accuracy
* Make the AI model for the accuracy
* Add a vibrating motor for relaying ambient noise levels (Calling, alarm etc)

### Material Requirements

* Pair of gloves
* Strain gauges (5 pcs per glove)([https://www.spectrasymbol.com/product/flex-sensors/#tab-distributors](https://www.spectrasymbol.com/product/flex-sensors/#tab-distributors))
* mpu9265(or any 9dof IMU would work too)
* Esp32 module(we are using the esp32-s2-wroom but any esp with wifi should do the job)
* Wires and resistors(5x 10k resistors for the Strain gauges)
* Pin Headers for MPU and uC
* Perf-board or custom PCB(py 5cmx7cm [https://www.adafruit.com/product/4786](https://www.adafruit.com/product/4786))
* Enclosure(3d printed)
* LiPo Battery (flat) (hasn't been used yet or bought yet)

Advised Parts-list for Glove Pair:

* Pair of garden or runner gloves eg [Nike Runner](https://www.amazon.com/Nike-Element-Running-Gloves-Silver/dp/B078WGKWW7/ref=sr_1_3?crid=2BGO7QDU5350A&keywords=Nike%2BSphere%2B360%2BRunning%2Bbreathing%2BGloves&qid=1666008792&sprefix=nike%2Bsphere%2B360%2Brunning%2Bbreathing%2Bgloves%2Caps%2C207&sr=8-3&th=1) @ $25 or [Flexilla Garden](https://www.amazon.com/Flexzilla-GC290M-Bamboo-Crinkle-ZillaGreen/dp/B08ZQ5PRHX/ref=sr_1_30_sspa?crid=2UUVPR9AAYQ8P&keywords=Nike+garden+gloves&qid=1666008908&qu=eyJxc2MiOiIxLjQ3IiwicXNhIjoiMS4wMCIsInFzcCI6IjAuMDAifQ%3D%3D&sprefix=nike+garden+glove%2Caps%2C220&sr=8-30-spons&psc=1) @ $5
* 2x [ESP32 starter kit](https://www.aliexpress.us/item/3256804153580081.html?spm=a2g0o.detail.1000014.5.674a59cd9uUO16&gps-id=pcDetailBottomMoreOtherSeller&scm=1007.40050.281175.0&scm_id=1007.40050.281175.0&scm-url=1007.40050.281175.0&pvid=97d7af3b-1a2f-4e17-b031-7951de28be50&_t=gps-id:pcDetailBottomMoreOtherSeller,scm-url:1007.40050.281175.0,pvid:97d7af3b-1a2f-4e17-b031-7951de28be50,tpp_buckets:668%232846%238113%231998&pdp_ext_f=%7B%22sku_id%22%3A%2212000028820378692%22%2C%22sceneId%22%3A%2230050%22%7D&pdp_npi=2%40dis%21USD%2112.9%2112.9%21%21%21%21%21%40210323a416657738103337417e1ac4%2112000028820378692%21rec) @$13 (each glove gets one)
* 2x IMU [MPU9265](https://www.amazon.com/MPU-9255-Three-axis-Gyroscope-Accelerometer-Magnetic/dp/B08D11HBZ7/ref=sr_1_17?crid=2JICHOUHCF8X3&keywords=MPU9265&qid=1666009715&qu=eyJxc2MiOiIwLjAwIiwicXNhIjoiMC4wMCIsInFzcCI6IjAuMDAifQ%3D%3D&sprefix=mpu9265%2Caps%2C293&sr=8-17) @ $8.42
* 1x mini [10pcs LiPo battery+ charger](https://www.amazon.com/Accessories-Battery-Charger-Quadcopter-Wholesale/dp/B08B5T3LFG/ref=sr_1_21?crid=8OGH822MFII8&keywords=mini+lipo+battery&qid=1666011343&qu=eyJxc2MiOiI0LjU0IiwicXNhIjoiMy43NiIsInFzcCI6IjIuODIifQ%3D%3D&sprefix=mini+lipo+battery%2Caps%2C250&sr=8-21) @35/set or 2x [Wrist Power Banks](https://www.aliexpress.us/item/3256804150563219.html?spm=a2g0o.productlist.main.5.43e64d12fIFIwR&algo_pvid=0059221a-58c2-42d9-bcbd-ed93004e9376&algo_exp_id=0059221a-58c2-42d9-bcbd-ed93004e9376-2&pdp_ext_f=%7B%22sku_id%22%3A%2212000028808470756%22%7D&pdp_npi=2%40dis%21USD%2135.0%2135.0%21%21%21%21%21%40211bd4cd16660102040202676d072e%2112000028808470756%21sea&curPageLogUid=asopnJ3RJZ3v) @$37
* 10 pcs [Flex Strain Gauges](https://www.amazon.com/Pressure-SF15-150-150mmx15mm-Resistance-type-Sensitive/dp/B08B41DF22/ref=sr_1_3?crid=1ZXQZ76BYEZAN&keywords=flex+strain+sensor&qid=1666009301&sprefix=flex+strain+sensor%2Caps%2C217&sr=8-3) 4.5" @ $15 (est)
* Resistors and maybe some power regulator IC's

This brings the total investments upper limit for stage 1 around USD300 (+approx $50 shipping costs)

### Glove 1.0

The first prototype was made digitally on tinkercad because it sounded to most people more difficult as it sounds but the first prototype was not. It consists of 5 flex sensors placed on the back on every finger and based  on the readings a letter is read

#### The assembly

The assembly consists of a pair of gloves, 5 flex sensors, a MPU9250, a ESP32

The flex sensors were stitched to the back of every finger and everything is brought together on a Perf-Board that is stitched on the back of the hand with a 3D base under it.

![](https://lh4.googleusercontent.com/TkwOgd1BehM4MLjbzgKPiqqXCfWdr0rY3E0IyipxJBkzYqzqdC26-simkx5TJq3nzxhewRuft9eqSospux9DEzWBBYD78vZkC2Q3jjZuUc48LMw5SD-wrKFyeFBN8WKRXHnTnTqAFyyv9lXQsAMNSZua3aacmQm22EDZcmU7iaGsKPSGgY44tVpaUg)

Here is a photo of the flex sensors stitched to the gloves

![](https://lh4.googleusercontent.com/CJR911E2Jcvm87OaodWWtquTWjEnQNS889MLMHAGHxImVak0uLgjqux78lQQeBH1qZJqzCbgt_lS4XGR6e5FiIeKQI8tcsHri6CHQEADB3p3LmsvurPrWdLdpk-2VeBWg9AYTzNND8Jx6LN3DOyfY2RiypsD5jMfz7VwMQ6ySQ9QaAGT3EFqq_BTWQ)

Here is the first working and fully made glove

##### Making the pref-board

When i was making the pref-board the first time it was the first time i actually soldered but i got the hang of it after messing up my first board lol.

I didn't really follow a schematic so i can't really add one put ill show a rough sketch of how i pulled the cables

After I pulled the cables on the board, I pulled some cables to connect the flex sensors to the board. While doing that I soldered on my friend's hand because the sensors were already stitched on the glove and or being stitched. Soldering cables on the flex sensor is not easy because you can very easily burn the connection of the sensor with heat for the iron.

#### The code

The version 1 code mostly consists of if statements that check if a flex sensor is in a set range and if all the flex sensors are in a certain range a letter comes out.

Most of the letters can be read with only the flex sensors but some of the letters have some motion so that's where the MPU comes in.

BUT i could get that to work yet so for the Hack0mation i only had letters that use the flex sensors only (15 letters or sum i lost count lol).

Ohh yeah almost forgot i made a separate library for the flex sensor to not make the main code not so bulky but it did not end as i wanted to because most of the code from the library was useless

Okay that's enough about the small introduction about the code let's get into it

This sets the pins of each flex sensor

| fl.intelise_output(Flex_pin1, Flex_pin2, Flex_pin3, Flex_pin4, Flex_pin5);`` |
| ---------------------------------------------------------------------------------------- |

This calibrates the highest and lowest flex sensor output for the constraints of the outputs

 while(millis()<privtime+times)

  {

    if(digitalRead(7)==HIGH)

    {

    floatflexADC1=analogRead(Flex_pin1);

    floatflexADC2=analogRead(Flex_pin2);

    floatflexADC3=analogRead(Flex_pin3);

    floatflexADC4=analogRead(Flex_pin4);

    floatflexADC5=analogRead(Flex_pin5);

    if(flexADC1<sensorMin1)

    {

    sensorMin1=flexADC1;

    }

    if(flexADC1>sensorMax1)

    {

    sensorMax1=flexADC1;

    }

    if(flexADC2<sensorMin2)

    {

    sensorMin2=flexADC2;

    }

    if(flexADC2>sensorMax2)

    {

    sensorMax2=flexADC2;

    }

    if(flexADC3<sensorMin3)

    {

    sensorMin3=flexADC3;

    }

    if(flexADC3>sensorMax3)

    {

    sensorMax4=flexADC4;

    }

    if(flexADC4<sensorMin4)

    {

    sensorMin4=flexADC4;

    }

    if(flexADC4>sensorMax4)

    {

    sensorMax4=flexADC4;

    }

    if(flexADC5<sensorMin5)

    {

    sensorMin5=flexADC5;

    }

    if(flexADC5>sensorMax5)

    {

    sensorMax5=flexADC5;

    }

    }

  }

This contains the flex sensors outputs to a smaller number from 0 to 90

floatFlexgrade1=readconstrainmapflex(Flex_pin1,flexADC1,sensorMin1,sensorMax1);

 floatFlexgrade2=readconstrainmapflex(Flex_pin2,flexADC2,sensorMin2,sensorMax2);

 floatFlexgrade3=readconstrainmapflex(Flex_pin3,flexADC3,sensorMin3,sensorMax3);

 floatFlexgrade4=readconstrainmapflex(Flex_pin4,flexADC4,sensorMin4,sensorMax4);

 floatFlexgrade5=readconstrainmapflex(Flex_pin5,flexADC5,sensorMin5,sensorMax5);

Here is a example of one of the if statements

if(((Flexgrade1>=25)&&(Flexgrade1<=42))&&((Flexgrade2>=40)&&(Flexgrade2<=60))&&((Flexgrade3>=40)&&(Flexgrade3<=60))&&((Flexgrade4>=35)&&(Flexgrade4<=60))&&(Flexgrade5==90))

  {

    Serial.print("A");

  }

Here is the first way i tried to minimise the spam of reading the letters but that would not work good if you need the same letter after each other

voidprintletter(charread_letter)

{

  if(read_letter!=priv_letter)

  {

    Serial.println(read_letter);

    priv_letter=read_letter;

  }

}

Okay i hope to have explained everything that i find hard to get but if anything still needs clarification feel free to ask me

#### References

Here is the [link](https://github.com/ddylvianen/Visionary-Hands) to all the code and 3d prints, etc of version1.0.

Here is the [link](https://drive.google.com/file/d/1rneLRhRRG5mcmAeZ7FQOerTq3z9IXqmr/view?usp=sharing) to the LIBS for version1.0.

### Glove 2.0

On the second version I wanted to add 2 AI models ,a vibrator to alarm the person if they are being called and make the arduino code better. it does not seem like much but the AI part is the most hardest to do

 The first AI model is for better reading accuracy of the words and letters and the second one is for the inverse kinematic modeling.

The vibrator is going to be controlled by sound loudness that is going to be changeable

![img](https://lh5.googleusercontent.com/3tu2-PkYyx71Dt67eqZljIpnCCMHtPrxi5QO_WpT9JfSNmysuzDkoi5U-lgwwOlEWLncNsVlg83hayivFHO4-IJFhZcVoAfdhbX7tWPCAtkmKtdoUWnxZJoiT1iFoKnp1qAElphYZXiLqfIcfZ3NJtN1s1X1BS29yTBaGopPhIimqHFs72HjJGFCjQ)

#### The assembly

For the second glove I bought a better fitting glove to make wearing the glove more comfy(the old ones fitted very tight on my hands) and made everything over to the new glove. While doing that I also made a new perfboard because the connections on the older ones were not that good.

To make the new perf board I also did not follow a schematic but I did make a power railing and a row for the resistors. For this build we solved the resistor problem that I had with the older versions where with the specified resistor value it gave a very low output. We found that the esp can read a higher analog value than other microcontrollers and that solved the problem.

We also 3dprinted a enclosure for the components

#### The code

The code was completely rebuilt to have separate classes for everything. For example the detecting of letters and words are in a separate class, the kinematics are in a separate class and the AI accuracy is in a separate class.

The detection of words was also completely rebuilt with the help of math and vector distance. This new code does not yet have the kinematic modeling added to it but I do have an older version where that is added.

Before I actually created the circuit I made a tinkercad circuit to simulate it:

[https://www.tinkercad.com/things/heWnATffkK6-visionaryhands-flex/editel?sharecode=i0oi51NOIAM4CiszyZ0qBML8q3vNrdxGz3yAD8fwVjs](https://www.tinkercad.com/things/heWnATffkK6-visionaryhands-flex/editel?sharecode=i0oi51NOIAM4CiszyZ0qBML8q3vNrdxGz3yAD8fwVjs)

### AI model 1: Accuracy

#### Training set

For this model a training set is very very important so i started with that

I made an INO sketch on top of what I already had and rebuilt it to have websockets. After I enter a letter in the serial plotter the ESP sends 50 examples back to back with the entered letter.

The examples are then sent to a python script with websockets and are then stored in a CSV file.

#### AI model

Not yet made

### AI model 1: movement

#### Training set

For the training set I made an arduino sketch which collected 218 rows of raw data in one sample then prints a space that means for the AI model that it is the end of the sample. For each movement I collect 100 samples and save them in a CSV that has the name of the movement, for example shake.csv.

#### AI model

The Ai model is made in google colab with tensorflow and python.
The Ai model has a list with the names of the movements and opens them one by one and saving all the samples to the tensorflow model after that the model is ran and the trained model is converted in a .h file that then can be used in arduino

#### The problems

* Vector problem

Because the code works with the distance of lines it sometimes mistakes some letters as others because mathematically they are the closest. To solve this I'm trying to work only with the raw values to calculate the distance of the letters and what's read.

#### References

Here is the [link](https://drive.google.com/drive/folders/1rLLe2F80Qpw9iofulS2PyZ5UTKx8yu-y?usp=sharing) to the CSV files

Here is the [link](https://colab.research.google.com/drive/1IdEBUskBIFNHAhzjUdafcr6tCeL3xEIE?usp=sharing) to the code for the movement

here is the [link ](https://drive.google.com/file/d/1jS7SFJwkADKS-4psZpCET0iqG2ULNxEd/view?usp=sharing)to the code for getting the training set (the mpu9250 library is needed check the version1 library zip for it)

here is the[ link](https://drive.google.com/file/d/1VqwMDg_-T8IcDYHy4aLBACfV5tuzAkGH/view?usp=sharing) to the new stack

here is the[ link](https://drive.google.com/file/d/1JuE0VxJngWtPTRND7jXJjFW9Mi7wlhzb/view?usp=sharing) to the sketch that has the kinematic modeling added(note: this version still works with if statements instead of the vector distance and is very messy)(i will if needed clarify the code a bit better)

**
