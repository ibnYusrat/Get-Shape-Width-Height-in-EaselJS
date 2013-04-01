# Objective
Keep an eye on the coordinates used in the drawing commands and use them to generate height/width of a dynamically drawn Shape object.

## Idea

The idea is to create a new child class of createjs.Shape() class and before calling the drawing commands, we keep a record of the coordinates.

What (apparently) works:
* Get width/height of a dynamically drawn Shape() object.
* Set width/height of any Shape() object.
* Get width even after rotation of a Shape.
* Get unrotated bounds of a Shape. i-e get a rectangle of the object by assuming it is at 0 rotataion.
* A formula for getting bounds even when it is rotated is also included, but is commented because its not accurate. Check out the source-code.

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


### A Request
It is just an idea of how dimentions of a dynamically drawn shape objects can be retrived. If you make any improvments in the code, PLEASE do let me know so that I can update this git. Your credit will obviously remain intact. 
Thanks!
