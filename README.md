Download Link: https://assignmentchef.com/product/solved-part-5-modify-rivergui
<br>
<p style="font-weight: 400;">We’re going to completely redesign RiverGUI so that the following are true:

<ul>

 <li style="font-weight: 400;">There are no signs of beans, goose, wolf, or farmer anywhere!</li>

 <li style="font-weight: 400;">In the View portion of the class, we’re going to map items to their new rectangles, and then we’ll draw the rectangles.</li>

 <li style="font-weight: 400;">In the Controller portion of the class, we’re going to check if a click occurred in an items rectangle, and handle it if it did.</li>

 <li style="font-weight: 400;">We’re going to use offsets and maps so that we can handle different items using the same method.</li>

</ul>

<p style="font-weight: 400;">If you have a different idea for refactoring and updating the RiverGUI *and* you’re very comfortable with Java, let me know on Piazza and I will let you try it. But consider working with the outline I give here since I tested it and I know it works (and I know *how* it works). First, let’s look at some of the fields you are going to need.

<p style="font-weight: 400;">        private final Rectangle leftBoatRectangle = …private final Rectangle rightBoatRectangle = …private final Rectangle baseLeftRectangle = …private final Rectangle baseRightRectangle = …private final int[] dx = { .., .., .., .. };private final int[] dy = { .., .., .., .. };

private GameEngine engine; // Modelprivate Map&lt;Item, Rectangle&gt; rectMap;private Rectangle boatRectangle;

<p style="font-weight: 400;">The left boat rectangle and right boat rectangle have not changed. The base left rectangle is the old left goose rectangle and the base right rectangle is the old right goose rectangle. Note that in the old GUI, the goose was always at the bottom left of the item group, while in this GUI, the beans will always be at the bottom left (we can change this depending on how we implement the game engine). We do not need the other rectangles because we’re going to use offsets. The dx and dy array represents the offsets of the four items. ITEM_0 is the bottom-left item, so it’s offset is 0, 0. ITEM_3 is the top-right item so it’s offset is 60, -60 (that’s 60 pixels right along the x-axis and 60 pixel up along the y-axis). We’re also going to change the way items are loaded onto the boat. We will only allow at most two items, but those items will be in their offset positions. This doesn’t look good, but we’ll do it this way for now and try to get things looking better later. The image below shows how offsets are used.

<p style="font-weight: 400;">We also introduce a map field that maps items to rectangles. This is going to help us decide if a click event takes place inside an item or boat. The boatRectangle field will hold what you expect – the rectangle for the boat.

<p style="font-weight: 400;">The code below indicates how the view will change.

<p style="font-weight: 400;">@Overridepublic void paintComponent(Graphics g) {g.setColor(Color.GRAY);g.fillRect(0, 0, this.getWidth(), this.getHeight());paintItem(g, Item.ITEM_0);paintItem(g, Item.ITEM_1);paintItem(g, Item.ITEM_2);paintItem(g, Item.ITEM_3);paintBoat(g);}

private void paintItem(Graphics g, Item item) { … }private void paintBoat(Graphics g) { … }private Rectangle getItemRectangle(Item item) {// associate the item with its new value (rectangle) in the mapreturn rectMap.get(item);}private Rectangle getBoatRectangle() {// set boatRectangle to its new valuereturn boatRectangle;}

private Rectangle getRectangleOffsetBy(Rectangle rect, int dx, int dy) { … }

private void paintRectangle(Graphics g, Color color, String label, Rectangle rect) {// similar to paintStringInRectangle but now it creates// the rectangle with the specified color first// use rect.x, rect.y, rect.width, rect.height to get those values if needed}

<p style="font-weight: 400;">The main method simply paints 4 items and the boat. To paint a rectangular object, you will need (1) the color, (2) the label, and (3) the rectangle. Color and label are easy to get from the game engine. For the rectangle of an item, you have to change where the item is (start, finish, or boat) and then determine what the rectangle is. Once you have the rectangle, use it to update the map. The rectangle in the map is what you should return. For the item rectangles, you will need to work with offsets, that’s why we included the method getRectangleOffsetBy(…). It is very important that when you implement this method the first thing you do is make a *copy* of the rectangle that is passed in using the copy constructor. Unfortunately (in my opinion), Java rectangles are *mutable*, which means its easy to inadvertently modify their values even if you have declared them to be *final*. The body of the paint-rectangle method can be fairly easily deduced from the paint-string-in-rectangle method from the original code.

<p style="font-weight: 400;">Finally, because we mapped items to rectangles in the View, the Controller is not that difficult:

<p style="font-weight: 400;">@Overridepublic void mouseClicked(MouseEvent e) {if (rectMap.get(Item.ITEM_0).contains(e.getPoint())) {respondToItemClick(Item.ITEM_0);} else if (rectMap.get(Item.ITEM_1).contains(e.getPoint())) {respondToItemClick(Item.ITEM_1);} else if (rectMap.get(Item.ITEM_2).contains(e.getPoint())) {respondToItemClick(Item.ITEM_2);} else if (rectMap.get(Item.ITEM_3).contains(e.getPoint())) {respondToItemClick(Item.ITEM_3);} else if (boatRectangle.contains(e.getPoint())) {engine.rowBoat();}repaint();}

<p style="font-weight: 400;">The respond to item click method is fairly simple.