# Objective
Keep a record of all drawing commands and use them to generate height/width of a dynamically drawn Shape object.

## Idea

The idea is to create a new child class of createjs.Shape() class and before calling the drawing commands, we keep a record of the dimentions.

What (apparently) works:
* Get width/height of a dynamically drawn Shape() object.
* Set width/height of any Shape() object.
* Get width even after rotation of a Shape.
* Get unrotated bounds of a Shape. i-e get a rectangle of the object by assuming it is at 0 rotataion.
* A formula for getting bounds even when it is rotated is also included, but is commented because its not accurate. 
 Available via rubygems

## Reason for making this git
For now the code is just an 'idea' of what I think can be done. And for now I have only implimented it for Shape. This can very easily be extended to work for containers and other display objects.

### Example

  			var g = new Shape();
				g.graphics.beginFill("#FCF").rect(0,0,180,100);
				g.graphics.beginFill("#CFC").drawCircle(50,50,100);
				g.graphics.beginFill("#F00").drawRoundRect(100,100,100,100,20);
				g.graphics.beginFill("#0F0").drawEllipse(0,0,250,50);
				g.graphics.setStrokeStyle(9).beginStroke("#FFF").moveTo(300,50).lineTo(500,200);
				g.graphics.setStrokeStyle(9).beginStroke("#00F").moveTo(30,30).arcTo(120,120,70,70,5);

				g.x = canvas.width/2 - g.width/2;
				g.y = canvas.height/2 - g.height/2;
        
        console.log(g.width);
        console.log(g.height);

        g.width = 50; //etc etc.
        
        //also, for example:
        var outline = new Shape();
  			var bounds = g.getUnrotatedBounds();
				outline.graphics.setStrokeStyle(3).beginStroke("rgb(62,255,45)").drawRect(bounds.x,bounds.y,bounds.width,bounds.height);
        //Should draw a rect around the Shape();


## logger.rb
An example using logger as a watcher. Pretty straighforward.


## httpclient.rb/httpclient-server.rb
This is an example of how the Webhook system would work

### Running

To get the maximum effect, start with a clean Redis database

* Start the webhook reciever

	noah/examples$ ruby httpclient-server.rb 
	== Sinatra/1.1.2 has taken the stage on 4567 for development with backup from Thin
	>> Thin web server (v1.2.7 codename No Hup)
	>> Maximum connections set to 1024
	>> Listening on 0.0.0.0:4567, CTRL+C to stop

* Start the webhook publisher

	noah/examples$ ruby httpclient.rb

* Run the rake sample script

In the publisher window, you should see some messages like so:

	Got message for noah.Host[localhost].create
	Got message for noah.Host[localhost].save
	Got message for noah.Host[localhost].save
	Got message for noah.Host[localhost].update

In the server window, you should see the following:

	"{\"id\":\"1\",\"name\":\"localhost\",\"status\":\"up\",\"created_at\":\"2011-02-15 05:19:05 UTC\",\"updated_at\":\"2011-02-15 05:19:05 UTC\",\"services\":[]}"
	127.0.0.1 - - [15/Feb/2011 00:19:05] "POST /webhook HTTP/1.1" 200 135 0.0024
	"{\"id\":\"1\",\"name\":\"localhost\",\"status\":\"up\",\"created_at\":\"2011-02-15 05:19:05 UTC\",\"updated_at\":\"2011-02-15 05:19:05 UTC\",\"services\":[]}"
	127.0.0.1 - - [15/Feb/2011 00:19:05] "POST /webhook HTTP/1.1" 200 135 0.0004
	"{\"id\":\"1\",\"name\":\"localhost\",\"status\":\"up\",\"created_at\":\"2011-02-15 05:19:05 UTC\",\"updated_at\":\"2011-02-15 05:19:05 UTC\",\"services\":[]}"


## websocket.rb
This is an example of using Websockets, EventMachine and Redis PubSub to provide a "status" console of sorts.

### Running

To get the maximum effect, start with a clean Redis database.

* Start the server:

	~/development/noah/examples$ ./websocket.rb 
	>> Thin web server (v1.2.7 codename No Hup)
	>> Maximum connections set to 1024
	>> Listening on 0.0.0.0:3000, CTRL+C to stop

You should be able to load up the "normal" Noah sample page on [http://localhost:3000].

* Load the "websocket" file

In another browser window, open the `websocket.html` file.

* Send a message

From another terminal window send the following:

	curl -X PUT -d '{"name":"testhost2","status":"down"}' http://localhost:3000/h/testhost2

You should see the message come across in the browser window like so:

	connected...

	2 connected and waiting....

	(noah.Host[testhost2].create) {"id":"1","name":"testhost2","status":"down","created_at":"2011-02-14 20:58:04 UTC","updated_at":"2011-02-14 20:58:04 UTC","services":[]}

	(noah.Host[testhost2].save) {"id":"1","name":"testhost2","status":"down","created_at":"2011-02-14 20:58:04 UTC","updated_at":"2011-02-14 20:58:04 UTC","services":[]}

	(noah.Host[testhost2].save) {"id":"1","name":"testhost2","status":"down","created_at":"2011-02-14 20:58:04 UTC","updated_at":"2011-02-14 20:58:04 UTC","services":[]}

	(noah.Host[testhost2].update) {"id":"1","name":"testhost2","status":"down","created_at":"2011-02-14 20:58:04 UTC","updated_at":"2011-02-14 20:58:04 UTC","services":[]}

You can see the Watcher pattern in the parenthesis and then the JSON message body.

For fun, refresh the page to clear it and then run the sample data population rake task.

### Known issues
When I started working on the Watcher stuff, I realized that I'm sending A LOT of extranous messages. These are mostly the result of the way I'm creating new objects with Ohm (i.e. via `.create`).
I'll be cleaning that up and trying to get down to a single message per operation.
