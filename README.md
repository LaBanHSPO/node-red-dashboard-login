# Node-RED-Dashboard : Log-In, Session Management

Node-RED Dashboard Example with Sign In (Log In) Form, Session Management (15 secs for this example)

#### Accounts 

```
 [{ username : 'admin', password : 'admin' },
  { username : 'guest', password : 'guest' }]
```    

#### Sensor Flow 

The Sensor Flow (and modified for this demo) is from https://diyprojects.io/node-red-dashboard-gauges-charts-notifications-html

#### `ui-control`

  
- Transition from `Signin` to `SensorData` & `History` Group.  ( `TabName`_`GroupName` )

 > `{group:{hide:["Dashboard_Signin"], show:["Dashboard_SensorData", "Dashboard_History"], focus:true}} `

- Transition from `SensorData` & `History` Group to `Signin` Group.

 > `{group:{hide:["Dashboard_SensorData","Dashboard_history"], show:["Dashboard_Signin"], focus:true}}`

<p align="center">
<img src="https://github.com/phyunsj/node-red-dashboard-login/blob/master/node-red-dashboard-session-layout.png" width="300px"/>
</p>

## In Action 


<p align="center">
<img src="https://github.com/phyunsj/node-red-dashboard-login/blob/master/node-red-login-session.gif" width="700px"/>
</p>

## Flow

<p align="center">
<img src="https://github.com/phyunsj/node-red-dashboard-login/blob/master/node-red-login-session.png" width="700px"/>
</p>

```
[{"id":"bd456e4e.359d8","type":"ui_form","z":"2f4a7c2e.00e544","name":"Dashboard Sign-In","label":"","group":"b82da486.9fc8d8","order":0,"width":0,"height":0,"options":[{"label":"username","value":"username","type":"text","required":true},{"label":"password","value":"password","type":"password","required":true}],"formValue":{"username":"","password":""},"payload":"","submit":"submit","cancel":"cancel","topic":"","x":109.01734924316406,"y":104.00000381469727,"wires":[["ea809461.f36198","a926c1ff.ede37","6cabb33.95cd54c"]]},{"id":"6603f3ab.d89abc","type":"debug","z":"2f4a7c2e.00e544","name":"Debug Account Log ","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","x":677.0174407958984,"y":65.0000114440918,"wires":[]},{"id":"bc6dfbc9.d9d138","type":"comment","z":"2f4a7c2e.00e544","name":"Sign-In Section","info":"","x":109.0173568725586,"y":47,"wires":[]},{"id":"ea809461.f36198","type":"function","z":"2f4a7c2e.00e544","name":"Account Log","func":"var accountlog =  flow.get(\"accountlog\") || [] ; \n\naccountlog.push({ accessAt : new Date(), username : msg.payload.username})\n\nflow.set(\"accountlog\", accountlog);\n\nmsg.payload = accountlog;\nreturn msg;","outputs":1,"noerr":0,"x":308.0173568725586,"y":74,"wires":[["6603f3ab.d89abc"]]},{"id":"a926c1ff.ede37","type":"function","z":"2f4a7c2e.00e544","name":"Account Verification","func":"var accounts =  flow.get(\"accounts\") || [ { username : \"admin\", password : \"admin\"},{ username : \"guest\", password : \"guest\"}] ; \n\nvar username = msg.payload.username ;\nvar password = msg.payload.password ; \n\nmsg.payload = 1;\n\naccounts.forEach(function ( account ){\n    if ( account.username == username ) {\n       msg.payload = 2;\n       if ( account.password == password ) {\n           msg.payload = 0;\n       } \n    }\n});\n\nif ( msg.payload == 0 ) {\n  var currentsocketid = flow.get(\"clientid\") || undefined;\n  if ( currentsocketid !== undefined && currentsocketid !== msg.socketid ) msg.payload = 3;\n}\n// keep the original socketid from msg.socketid;\nreturn msg;","outputs":1,"noerr":0,"x":321.01739501953125,"y":119.00000953674316,"wires":[["88afc196.8abc2"]]},{"id":"88afc196.8abc2","type":"switch","z":"2f4a7c2e.00e544","name":"","property":"payload","propertyType":"msg","rules":[{"t":"eq","v":"0","vt":"str"},{"t":"eq","v":"1","vt":"str"},{"t":"eq","v":"2","vt":"str"},{"t":"eq","v":"3","vt":"str"}],"checkall":"true","repair":false,"outputs":4,"x":490.017391204834,"y":149.99999237060547,"wires":[["eefb7b9e.f5daa8"],["2f57eca.40fa214"],["cc34d440.a91948"],["126fd334.ed13ed"]]},{"id":"eefb7b9e.f5daa8","type":"function","z":"2f4a7c2e.00e544","name":"= 0 : Success","func":"/* activate session timer */\nvar sessionTimer =  flow.get(\"sessionTimer\") || 0; \nvar currTime = Date.now();\nflow.set(\"sessionTimer\", currTime);\nflow.set(\"clientid\", msg.socketid);\n/* ui-control payload */\nmsg.payload = { group: {\n    show : [\"Dashboard_SensorData\", \"Another_History\"],\n    hide : [\"Dashboard_Signin\"]\n    }\n};\n\nreturn msg;","outputs":1,"noerr":0,"x":660.0172348022461,"y":106.00000190734863,"wires":[["5c0b7126.396e8"]]},{"id":"2f57eca.40fa214","type":"function","z":"2f4a7c2e.00e544","name":"= 1 : Unknown User","func":"msg.payload = \"Username Doesn't exist!!!\"\nreturn msg;","outputs":1,"noerr":0,"x":681.0172424316406,"y":143.00000381469727,"wires":[["61e843e4.4d74cc"]]},{"id":"cc34d440.a91948","type":"function","z":"2f4a7c2e.00e544","name":"= 2 : Wrong Password","func":"msg.payload = \"Invalid Password\";\nreturn msg;","outputs":1,"noerr":0,"x":682.0173110961914,"y":180.00001621246338,"wires":[["61e843e4.4d74cc"]]},{"id":"5c0b7126.396e8","type":"ui_ui_control","z":"2f4a7c2e.00e544","name":"Go to Sensor/History Group","x":947.0174179077148,"y":105.99999809265137,"wires":[[]]},{"id":"61e843e4.4d74cc","type":"ui_toast","z":"2f4a7c2e.00e544","position":"dialog","displayTime":"3","highlight":"","outputs":1,"ok":"OK","cancel":"","topic":"Authentication Failed","name":"Authentication Failure Dialog","x":947.0173225402832,"y":163.00000381469727,"wires":[[]]},{"id":"d60f3dd8.0214b","type":"comment","z":"2f4a7c2e.00e544","name":"Session Management - 15 secs for this demo","info":"","x":188.51734924316406,"y":263.0000114440918,"wires":[]},{"id":"643a01a8.560ea","type":"inject","z":"2f4a7c2e.00e544","name":"Timer","topic":"","payload":"","payloadType":"date","repeat":"3","crontab":"","once":false,"onceDelay":0.1,"x":97.0173568725586,"y":351.99999618530273,"wires":[["bc828c1d.b3504","f6900b2.d3e9ef8"]]},{"id":"bc828c1d.b3504","type":"function","z":"2f4a7c2e.00e544","name":"Detect Session Timer","func":"var sessionTimer =  flow.get(\"sessionTimer\") || 0; \nvar currTime = Date.now();\nvar SESSION_TIMEOUT = 15000; //15 secs\n\nif ( sessionTimer === 0 /* Inactive, No user signed in */ ) {\n    msg.payload = 2;\n} else {\n    if ( currTime - sessionTimer > SESSION_TIMEOUT ) {\n      msg.payload = 0;  \n    } else {\n      msg.payload = 1;        \n    }\n}\nreturn msg;","outputs":1,"noerr":0,"x":285.0173873901367,"y":318.0000114440918,"wires":[["50a18f10.e23c2"]]},{"id":"50a18f10.e23c2","type":"switch","z":"2f4a7c2e.00e544","name":"","property":"payload","propertyType":"msg","rules":[{"t":"eq","v":"0","vt":"str"},{"t":"eq","v":"1","vt":"str"},{"t":"eq","v":"2","vt":"str"}],"checkall":"true","repair":false,"outputs":3,"x":455.0174217224121,"y":316.0000114440918,"wires":[["5b39c16e.da2f"],["91c7a0ab.ec163"],["71d9aa2e.665fd4"]]},{"id":"5b39c16e.da2f","type":"function","z":"2f4a7c2e.00e544","name":"Time-Out","func":"/* activate session timer */\nvar sessionTimer =  flow.get(\"sessionTimer\") || 0; \nflow.set(\"sessionTimer\", 0);\n\nmsg.socketid = flow.get(\"clientid\") || undefined;\nmsg.payload = \"Session Expired! Please singin again!\";\nreturn msg;","outputs":1,"noerr":0,"x":611.017333984375,"y":289.00001525878906,"wires":[["d29000e.556e5","a3d5d44d.a71ce8"]]},{"id":"d29000e.556e5","type":"ui_toast","z":"2f4a7c2e.00e544","position":"dialog","displayTime":"3","highlight":"","outputs":1,"ok":"OK","cancel":"","topic":"Session Timeout","name":"Time-Out Dialog","x":798.517463684082,"y":255.00001525878906,"wires":[[]]},{"id":"8859efbd.09b1b","type":"ui_ui_control","z":"2f4a7c2e.00e544","name":"Go to Signin Group","x":1005.5173816680908,"y":291,"wires":[[]]},{"id":"a3d5d44d.a71ce8","type":"function","z":"2f4a7c2e.00e544","name":"Sign-In Redirection","func":"var msg = {};\n\nmsg.socketid = flow.get(\"clientid\") || undefined;\nmsg.payload = { group: {\n    hide : [\"Dashboard_SensorData\", \n            \"Dashboard_History\"],\n    show : [\"Dashboard_Signin\"]\n}\n};\n\nflow.set(\"clientid\", undefined);\nreturn msg;","outputs":1,"noerr":0,"x":809.0173721313477,"y":292.0000114440918,"wires":[["8859efbd.09b1b"]]},{"id":"de95afea.546e3","type":"ui_toast","z":"2f4a7c2e.00e544","position":"bottom right","displayTime":"1","highlight":"","outputs":0,"ok":"OK","cancel":"","topic":"Timer","name":"Display Remaining Time","x":618.7395515441895,"y":405.99999618530273,"wires":[]},{"id":"4da58462.7ddd1c","type":"comment","z":"2f4a7c2e.00e544","name":"Sensor Data Collection","info":"","x":128.0173568725586,"y":448.0000057220459,"wires":[]},{"id":"937e9201.0a617","type":"inject","z":"2f4a7c2e.00e544","name":"Timer","topic":"","payload":"","payloadType":"date","repeat":"2","crontab":"","once":false,"onceDelay":0.1,"x":100.51734924316406,"y":525.0000133514404,"wires":[["ab530393.261bd"]]},{"id":"c4e61034.b5ec2","type":"comment","z":"2f4a7c2e.00e544","name":"MQTT ? CoAP?","info":"Expecting data from the actaul sensors.","x":275.5173568725586,"y":484.99999809265137,"wires":[]},{"id":"f6900b2.d3e9ef8","type":"function","z":"2f4a7c2e.00e544","name":"Calculating Remaining Time (Debug)","func":"var sessionTimer =  flow.get(\"sessionTimer\") || 0; \nvar currTime = Date.now();\nvar remainingSecs = Math.floor((15000 - (currTime - sessionTimer))/1000) + 1;\n    \nif ( sessionTimer == 0 /* Inactive, No user signed in */ ) {\n    return null;\n} else {\n    \n    if ( remainingSecs <= 7 ) msg.payload = \"Remaining : \" + remainingSecs + \" secs\";  \n    else return null;\n}\n\nmsg.socketid = flow.get(\"clientid\") || \"B1234\";\nreturn msg;","outputs":1,"noerr":0,"x":335.0173797607422,"y":406.0000238418579,"wires":[["de95afea.546e3"]]},{"id":"6d52854e.4e8d8c","type":"ui_text","z":"2f4a7c2e.00e544","group":"c35e46a8.b04ce8","order":0,"width":0,"height":0,"name":"Label Display","label":"","format":"{{msg.payload}}","layout":"row-spread","x":999.5173416137695,"y":611.0001316070557,"wires":[]},{"id":"ab530393.261bd","type":"function","z":"2f4a7c2e.00e544","name":"Decode MySensor Message","func":"\nvar mySensorsMessage = {}\nvar newPayload = {};\nvar timestamp = new Date();\n\nnewPayload.nodeId  =  3;\nnewPayload.type =  Math.floor( Math.random() * Math.floor(2));  // 0 or 1\nif ( newPayload.type === 0 /* temperature */ ) newPayload.value   =  Math.floor( Math.random() * Math.floor(30)) + 10; // 10 - 39\nelse newPayload.value   =  Math.floor( Math.random() * Math.floor(40)) + 40; // 40 - 79\n\nmsg.payload = newPayload; \n\nreturn msg;","outputs":1,"noerr":0,"x":296.5173568725586,"y":526.000020980835,"wires":[["1e3effb1.5ff58","d320c244.f24af"]]},{"id":"1e3effb1.5ff58","type":"function","z":"2f4a7c2e.00e544","name":"Filter : temperature","func":"if (msg.payload.nodeId == 3 && msg.payload.type === 0) {\n    var msg;\n    msg.payload = msg.payload.value;\n    msg.topic = \"Temperature\"\n    return msg;\n}    ","outputs":1,"noerr":0,"x":513.5174026489258,"y":482.0000238418579,"wires":[["ef7cf0b4.332f5","38045480.64ec1c","328b09bd.9901c6","4381200b.380d3"]]},{"id":"ef7cf0b4.332f5","type":"ui_gauge","z":"2f4a7c2e.00e544","name":"Gauge : Temperature","group":"c35e46a8.b04ce8","order":0,"width":0,"height":0,"gtype":"gage","title":"Temperature DHT22","label":"MySensors v2","format":"{{value}}°C","min":0,"max":"50","colors":["#66ccff","#ff8000","#ca3838"],"seg1":"","seg2":"","x":763.5173568725586,"y":467.99998664855957,"wires":[]},{"id":"d320c244.f24af","type":"function","z":"2f4a7c2e.00e544","name":"Filter : humidity","func":"if (msg.payload.nodeId == 3 && msg.payload.type === 1) {\n    var msg;\n    msg.payload = msg.payload.value;\n    msg.topic = \"Humidity\"\n    return msg;\n}    ","outputs":1,"noerr":0,"x":499.5174560546875,"y":589.0000629425049,"wires":[["d4eaedd1.98978","38045480.64ec1c","328b09bd.9901c6"]]},{"id":"d4eaedd1.98978","type":"ui_gauge","z":"2f4a7c2e.00e544","name":"Gauge : Humidity","group":"c35e46a8.b04ce8","order":0,"width":"0","height":"0","gtype":"gage","title":"Humidity DHT22","label":"MySensors v2","format":"{{value}}%","min":0,"max":"100","colors":["#1a25ab","#e6e600","#ca3838"],"seg1":"","seg2":"","x":755.5174217224121,"y":505.00003242492676,"wires":[]},{"id":"38045480.64ec1c","type":"function","z":"2f4a7c2e.00e544","name":"Attention : temperature & humidity","func":"context.data = context.data || new Object();\n\nswitch (msg.topic) {\n    case \"Temperature\":\n        context.data.temperature = msg.payload;\n        msg = null;\n        break;\n    case \"Humidity\":\n        context.data.humidity = msg.payload;\n        msg = null;\n        break;\n    default:\n        msg = null;\n    \tbreak;\n\n}\n\nif(context.data.temperature !== null && context.data.humidity !== null && context.data.temperature !== undefined && context.data.humidity !== undefined) {\n\tmsgOut = \"\"; //new Object();\n    msgOut = \"Temerature : \" \n    msgOut += context.data.temperature.toString(); \n    msgOut+= \"°C - Humidity : \"; \n    msgOut+= context.data.humidity.toString();\n    msgOut+= \"%\";\n    context.data=null;\n    var msg = {};\n    msg.payload = msgOut;\n\treturn msg;\n} \n//else return msg;","outputs":1,"noerr":0,"x":759.5173416137695,"y":612.0000915527344,"wires":[["6d52854e.4e8d8c"]]},{"id":"328b09bd.9901c6","type":"ui_chart","z":"2f4a7c2e.00e544","name":"History : Temperature & Humidity","group":"4de9506d.e7364","order":1,"width":"6","height":"9","label":"Temperature & Humidity","chartType":"line","legend":"true","xformat":"%H:%M","interpolate":"linear","nodata":"No Data Yet","dot":false,"ymin":"0","ymax":"80","removeOlder":"12","removeOlderPoints":"","removeOlderUnit":"3600","cutout":"","useOneColor":false,"colors":["#1f77b4","#aec7e8","#ff7f0e","#2ca02c","#98df8a","#d62728","#ff9896","#9467bd","#c5b0d5"],"useOldStyle":true,"x":806.5173797607422,"y":543.0001087188721,"wires":[[],[]]},{"id":"f6f225f9.0408b8","type":"ui_ui_control","z":"2f4a7c2e.00e544","name":"Stay in Sensor Group","x":955.517448425293,"y":330.00000953674316,"wires":[[]]},{"id":"91c7a0ab.ec163","type":"function","z":"2f4a7c2e.00e544","name":"Sensor/History Goup","func":"var msg = {};\nmsg.socketid = flow.get(\"clientid\") || undefined;\nmsg.payload = { group: {\n    show : [\"Dashboard_SensorData\", \"Dashboard_History\"],\n    hide : [\"Dashboard_Signin\"]\n}\n};\nreturn msg;","outputs":1,"noerr":0,"x":652.5173797607422,"y":330.00000762939453,"wires":[["f6f225f9.0408b8"]]},{"id":"71d9aa2e.665fd4","type":"function","z":"2f4a7c2e.00e544","name":"Signin Goup","func":"var msg = {};\nmsg.payload = { group: {\n    hide : [\"Dashboard_SensorData\", \"Dashboard_History\"],\n    show : [\"Dashboard_Signin\"]\n}\n};\nreturn msg;","outputs":1,"noerr":0,"x":623.5174407958984,"y":371.0000457763672,"wires":[["a2bd5885.a386c8"]]},{"id":"a2bd5885.a386c8","type":"ui_ui_control","z":"2f4a7c2e.00e544","name":"Go to Signin Group","x":943.5175971984863,"y":368.0000457763672,"wires":[[]]},{"id":"4381200b.380d3","type":"debug","z":"2f4a7c2e.00e544","name":"","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","x":723.0764236450195,"y":578.6424503326416,"wires":[]},{"id":"6cabb33.95cd54c","type":"debug","z":"2f4a7c2e.00e544","name":"","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","x":290.517391204834,"y":184.53475952148438,"wires":[]},{"id":"b960fa16.e7e618","type":"ui_toast","z":"2f4a7c2e.00e544","position":"dialog","displayTime":"3","highlight":"","outputs":1,"ok":"OK","cancel":"","topic":"System In Use.","name":"System In-Use Dialog","x":930.1285285949707,"y":216.12153911590576,"wires":[[]]},{"id":"126fd334.ed13ed","type":"function","z":"2f4a7c2e.00e544","name":"= 4 : System In Use","func":"msg.payload = \"System is used by other user.\";\nreturn msg;","outputs":1,"noerr":0,"x":671.4616775512695,"y":217.12153244018555,"wires":[["b960fa16.e7e618"]]},{"id":"b82da486.9fc8d8","type":"ui_group","z":"","name":"Signin","tab":"d65502b1.ee517","order":1,"disp":true,"width":"6","collapse":false},{"id":"c35e46a8.b04ce8","type":"ui_group","z":"","name":"SensorData","tab":"d65502b1.ee517","order":2,"disp":true,"width":"6","collapse":false},{"id":"4de9506d.e7364","type":"ui_group","z":"","name":"History","tab":"d65502b1.ee517","order":3,"disp":true,"width":"6","collapse":false},{"id":"d65502b1.ee517","type":"ui_tab","z":"","name":"Dashboard","icon":"dashboard","order":1}]
```

:pencil: Unfortunately, some changes are required to make it work. 

Under `node-red-dashboard`,

nodes/ui-toast.js

```
        node.on('input', function(msg) {
            //if (node.position !== "dialog") { delete msg.socketid; } // SJ
            ...
        });
```
nodes/ui-ui-control.js

```
        this.on('input', function(msg) {
            
            ...
            
            if ( msg.socketid !== undefined) ui.singleUserOnly = true; // SJ
            else ui.singleUserOnly = false;                            // SJ

        });
```
ui.js
```
var singleUserOnly = false;  // SJ
...
function emit(event, data) {
    if ( singleUserOnly &&  data.socketid !== undefined ) io.emit(event, data); // SJ
    else io.emit(event, data); // Display SingIn Form.  
}

function emitSocket(event, data) {

    if (data.hasOwnProperty("socketid") && (data.socketid !== undefined)) {
        io.to(data.socketid).emit(event,data);
    }
    //else {                        // SJ
    //    io.emit(event, data);     // SJ
    //}                             // SJ

}
...
```


#### Additional enhancements to be considered

- HTTPS Setup (https://www.hardill.me.uk/wordpress/2015/05/11/securing-node-red/) 
- New User Registration
- Database : account, log, etc

