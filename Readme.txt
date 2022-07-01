Blueprint Warehouse Carnage

Turn Physics on >> Select object.  >> Physics tab >> Simulate Physics (check it on)
There is also Disable Gravity (makes it zero-G)
There is also Mass checkbox you can check and give a mass to

So in starter scene, turn gravity on.  Place a large cube over the table.  Enable its gravity.  Hit play.  
Rejoice.

Objects -- something in the game.  A thing.
Nodes represent an object.

Reference - where the object resides in memory
If you have an obj in your scene you can in the blueprint right click and get an object reference to that obj!

Impulses -> receiving input
>> Bring in Space Bar node.
This will receive the input from pressed and released.

Type in Impulse to mesh.  "Add Impulse".  Hook that to space bar.  It will add impulse when space is pressed.
Hook that also to the Target.

Impulse is XYZ.  This is basically adding the force in that vector.
Force = Mass * Acceleration 
Impulse = Mass * Velocity Change
Unreal uses cm as its main unit.

Vel Change on Impulse box checked ignores the mass.  So you can put whatever, and it ignores the mass.
So if I put 400 in Z it will bunny hop on space key.
Alternatively don't check that box, then the Z needs to multiply 400 or whatever by the mass.

!! - no Input is registering - waiting on answer !!
!! Answer - click in the viewport it doesn't auto receive focus.... !~!
Can go into settings and check Game Gets Mouse Control to auto have mouse and keyboard focus when you start.


Blueprint classes and Instances (the UE version of prefab)
You can create a new object in scene.  In the property window there is a little blueprint button (its small and looks like a blueprint).  Clicking that creates a blueprint editor.

This creates the blueprint and puts it in your content drawer (ctl-space)

To delete a link - Alt + Click on it.

When pins are colored and can be expanded you  need to right click them and split pin.

=========================================================================================
DATA TYPES
the pins are colored by their data type so you can't just plug in incompatible data types.
Struct - same as object - quite small.  (same C++ / C# deal)

F8 - ejects from the game and you can move around the scene.\

Importing assets.  Go to unreal marketplace.  Get what you want.  Under library you can add to a project.
From there make sure you see it in content of the drawer (ctrl + shift) and if not, right click the folder of the content and choose to Migrate it.

Now to get projectiles to fire forwawrd, we use the Get Actor Forward Vector.  We then hook it to a multiplication node - right click that node and convert it to a float instead of a vector, and then plug that into the Impulse of the Add Impulse.
=========================================================================================
Geometry Brushes (BSP)
Tool bar (cube with plus sign and drop down)>> Place Actors Panel
That window - geometry button (stairs, spheres, cylinders, cones, boxes) (NOT THE SHAPES BUTTON!!)

Add Box
Brushes can build the geometry or carve it out by subtracting it.
Building an outer box that is the room then carve out the inner section that is the room.

Rename the Box to "Walls"
THE BRUSH SETTINGS get set to this (NOT THE SCALE!!!)
X = 34*100 (3400) - units are cm remember
Y = 2700
Z = 760

It seems when I make it that big its massive...?  I assume the cm by default is ... not the default.  

This creates the wire frame.
Take walls brush and duplicate n the outliner
Calls this one WallsSubtract

Expand walls brush (not walls subtract)
Add 200 to each one of those XYZs
Brush settings - BrushType Drop down = Subtractive (this is n property windows of WallsSubtract)
This makes a little room!  Because the walls are 2 meters bigger (so the walls are 2m)

Save level.  Named Main Map
Right side of screen >> Settings >> Project Settings >> Project Sectoin Maps and Modes, change the editor and game default map to that level.

Brings in Industry Props
Drags in rack to compare the size

Windows - add new box brushes that are subtractive and then position them in the walls.

=========================================================================================
MATERIALS AND LIGHTING
>> bring back minimal default level to get to your blueprint
>> ctl A select all, ctl C copy.
>> in new level open new level blueprint
>> paste everything in.

Asset filter >> materials >> check material filter.  Shows all materials in project
Outliner >> search for light (skylight and light source)
Double click light source (sun with arrows) and use rotate tool to change directionality of sun

=========================================================================================
Actor Components
Rack mesh has no shelves inside it.
You could add a rack but if you enable physics on both... they aren't cemented together.
Need to make a single item out of multiples.

Can drag rack or whatever up to the static mesh component of the main rack (in the details property panel)
This childs the item up kind of like unity.

Child objects position are relative to the parent position (same as in unity)
Enable physics on top most mesh.
They all now interact as one unit.

=========================================================================================
Collision Meshes
Hard to stack like barrels together especially if physics is going.  Incredibly unstable.

The way collisons are calculated is the problem.
Distilling down to player collision you can see it.

Select Item and go to detail (double click the mesh in the content drawer - this is the asset editor)
>> Collision button >> Remove Collision -> removes them and ... the barrel just goes through ground.
>> Collision >> Add 10DOP-Z Simplified Collision.  Adding it in, its wrapped around zed axis.  Seems to be much better.

Save the mesh.  Player Collision view seems less deformed.  If you click the Lit item in the main window (next to perspective in the viewport) you can see the various views (wireframe, collision, lit, etc)

When playing the barrels stay together.

Create a BP_Barrel class that has this simplified collision.  This will be what we use.
Increase mass to 25

=========================================================================================
Variables
In Blueprints
Window >> My Blueprint 

Adds the pane you need and one has a Variables section.
Add ammo and change it to an int
Compile (up at the top compile button says compile).  
Set a value in the default value of 20.

As we go through we want to decrement here.
Drag Ammo in and it brings a get and a set in.

Ctl Drag is Get  Alt Drag is Set as well
Get has output.  Set has input.

After print string, creates a new Print String and in there Ctl Clicks variable and connects that up so it shows the ammo count.

After you spawn projectile and impulse it, get the ammo.  Then subtract one.  Then set it.
R. Click search for Subtract
Get ammo, subtract one, store it in variable, print result

=========================================================================================
Booleans and IF 
Greater nodes, Equals node, Less nodes, etc
Branch node - true/false pin (your if statement)

=========================================================================================
Functions to organize
I was using comments

Select all of your nodes.  Right click, collapse to function.  Creates a new block that is the function.
>> Name it
Double clicking on the node opens all of those back up in its own tab.

You can add inputs and outputs as well that can be wired up.

Pure Functions -> you can remove pins from your functions.
Pure functions have no side effects and only return values.

If you check "pure" on the property node (Graph propery) it removes all of the pins.  This can be done without any side ffects (ie Has Ammo)

By clicking the pure check box, the white execution arrow goes away and you just plug the value straight into the bool input of the Branch.

=========================================================================================
Member Functions
Functions that live in a blueprint class.  

All of the nodes that deal with the BP_Projectile... we want to move them into that blueprint!
Open your blueprint.
All blueprints have a self.  This is the target reference that you will see in a lot of nodes.

Go to the functions.  Add one.
So in this the launch of the projectile will live specifically with the projectile class (blueprint)

=========================================================================================
Open Levels and Delay
Open Level by Name - pass in the name and it will load it
Delay - pauses for a moment
