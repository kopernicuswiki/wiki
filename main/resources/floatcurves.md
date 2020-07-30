---
layout: default
title: KSP FloatCurves and you- the magic of tangents
subtitle: Or: Enter the magical land of unexpected behaviour
---

You thought it was doing this:

TitleExampleA.jpg

Instead, its doing this:

TitleExampleB.jpg

Part 1: What are floatCurves?

If you make mods you've probably used floatCurves and never realized.

Every time you see something like this:

velocityCurve
{
    key = 1000 0 0 0
    key = 850 0.2 0 0
    key = 0 1 0 0
}
Or like this:

    emission = 0.0 0.0
    emission = 0.05 0.0
    emission = 0.075 0.25
    emission = 1.0 1.25
Or even something like this:
```
atmosphereCurve
{
    key = 0 370
    key = 1 320
}
```
You are making a floatCurve.

What is a floatCurve? Its a wrapper around a Unity Animation Curve. Essentially, its a way to describe an arbitrary line on a 2d graph using a few points rather than a formula.

Unity Animation Curves are a type of spline (cubic Hermite splines, I'm informed) and you can think of it as unity threading something that behaves like a stuff, springy steel cable through the points you set.

This lets you do things like describe how the playback volume of a sound should change with the throttle, or how a wheel's steering should change with speed, in a nonlinear way using only a few lines in a text file, and without complex maths.

Great, right?

They are, but if you're not careful floatCurves will trip you up, and things will not behave as you expect.

For example, here's a random curve I made:
```
key = 0 1
key = 0.4 0.6
key = 0.41 0.4
key = 1 0
```
What do you expect that looks like graphed out?

It looks like this:

[image link]

In this tutorial, I will teach you how to make it look like this:

RandomExampleB.jpg

Part 2: So what are these tangents you mentioned?

In its most basic form, each point on a floatCurve will operate in 'Free' mode, where Unity fits a spline to the entire curve making it a single smooth, unbroken line.

However, as you saw above, that can cause your curve to behave differently than you expect it. If that curve is driving the thrust of an engine, or the steering of a wheel, or the length of a rocket's flame, it could look very very wrong, and you'll be scratching your head trying to fix it and adding tons of points to the curve trying to disciple it.

Thankfully, you don't need to do that.

A little known fact about points on a floatCurve is that they can take 2 optional parameters, in-tangent and out-tangent, that describe the angle of the curve as it enters and leaves the point.

Since we're talking trigonometric functions to describe angles between points on a plane of totally arbitrary width and height, they're rather complicate to visualize unless you're a mathematical genius.

Thankfully, r4m0n made (because I badgered him about it) a little plugin for the unity editor that lets you edit them graphically, interactively.

Since KSP floatCurves are unity animation curves, you can also be sure that the function used to draw it out for you is the same that will be used by KSP when the curve is run on a part.

Part 3: Let's have some examples

First, you will need a floatCurve editor. The two main ones are the in-game editor provided by Amazing Curve Editor and r4mon's unity editor plugin. 

Install one so you can follow along.

Example 1: Torque curve for a racing wheel

The Tesla Roadster is a pretty neat car. It looks cool, its electric, and it goes pretty fast! Also, its made by a guy who makes rockets!

It would be pretty neat if we could make a rover wheel behave like the engine in of them, no? Well, we can, if we understand floatCurves.

First, lets try and find the torque curve for the vehicle. It so happens there's tons of them on the web, and a simple google image search for 'tesla roadster torque curve' got me this:

Example1A.jpg

We'll use Wolfram Alpha to convert the key points of the curve from Miles per Hour to Meters per Second.

That wavy bit at the start is probably the traction control keeping the torque in check.

If we do that in KSP we won't be able to start moving from a stop if we're on a hill, so lets start with 50% torque at 0m/s.

I have no idea how to convert lb-ft into whatever unity wheels for torque, so I'll start with the same value the 'RoveMax Model M1' starts with, 250 whatevers:
```
torqueCurve
{
    key = 0 250
}
```
The next point on the line - roughly 7.5mph - is, Wolfram tells me, at 3.35m/s, and we go full torque there, a full 500 whatevers!:
```
torqueCurve
{
    key = 0 250
    key = 3.35 500
}
```
Next, at about 35mph (15.65m/s) torque has dropped a little, lets say to 485:
```
torqueCurve
{
    key = 0 250
    key = 3.35 500
    key = 15.65 485
}
```
At 117mph(52.3m/s) its dropped to only 20%, or 100:
```
torqueCurve
{
    key = 0 250
    key = 3.35 500
    key = 15.65 485
    key = 52.3 100
}
```
By 120mph(53.64m/s) it seems to have pretty much dropped to 0, so lets do that:
```
torqueCurve
{
    key = 0 250
    key = 3.35 500
    key = 15.65 485
    key = 52.3 100
    key = 53.64 0
}
```
Great! Now we should have a wheel that performs, roughly like a Tesla Roadster's engine, right?

... ehm ... not so much.

Lets look at it in Float Curve Editor, and you'll see what I mean.

First, start the unity client, and under the KSP menu created by the plugin, select 'Curve Editor':

Example1B.jpg

You'll get a window like this:

Example1C.jpg

Click on the text box in the middle of it, and paste your key lines into it, like so:

Example1D.jpg

If you use tabs rather than spaces to indent your code, you'll have to remove them or the plugin won't be able to read the curve.

See the little graph up top? That's the line you defined with your floatCurve, graphed out and editable for you ... and already you can see it looks nothing at all like what we based it on.

A reminder. The curve we based our values on:

Example1A.jpg

Now click on the little graph, and it'll expand to show your curve in all its wobbly, misbehaving glory:

Example1E.jpg

Don't worry! We can fix it, we have the technology!

To start, right-click on the second point from the left, hover over 'Right Tangent' and select 'Linear':

Example1F.jpg

You'll get this:

Example1G.jpg

Move over to the next point, right-click and select 'Broken':

Example1H.jpg

You'll see 2 small lines appear to each side of the point, these are the handles you use to tweak the curve. Grab the one on the left of the point and move it down till it looks like this:

Example1I.jpg

Over to the next point, right-click and select 'Broken' again:

Example1J.jpg

Lower the left handle of that point till its lying flat:

Example1K.jpg

Go back to the middle point, click it, and then move its right handle down till it looks like this:

Example1L.jpg

Lets tweak those two handles a bit more, till it looks something like this:

Example1M.jpg

That's close enough to this for me:

Example1A.jpg

Copy the tangents from the Curve Editor into your .cfg:
```
torqueCurve
{
    key = 0 250
    key = 3.35 500 36.70368 -1.219512
    key = 15.65 485 -5.862143 -29.66346
    key = 52.3 100 -10.50477 -0.4904045
    key = 53.64 0
}
```
Example 2: Emission curve for an an FX group

Lets say I'm making a jet engine. Since I do the .cfg-side for B9 Aerospace, I do this kind of thing a lot.

This hypothetical engine is some kind of afterburner-equipped part, and I want to have a nice FX starting at 66% throttle to look cool :)

So, lets make the curve for the FX. Emission should start at 0.66, so we start off like this:

MODEL_MULTI_PARTICLE
{
    modelName = Squad/FX/shockExhaust_red_small
    transformName = thrustTransform
    emission = 0.66 0
}
Afterburner flames should ramp up with flames pretty quickly, so lets make another point at, say, 68% throttle, and put emission at 75% there.

MODEL_MULTI_PARTICLE
{
    modelName = Squad/FX/shockExhaust_red_small
    transformName = thrustTransform
    emission = 0.66 0
    emission = 0.68 0.75
}
Finally, full throttle should be full emission, so lets add a final point at 100% throttle, 100% emission.

MODEL_MULTI_PARTICLE
{
    modelName = Squad/FX/shockExhaust_red_small
    transformName = thrustTransform
    emission = 0.66 0
    emission = 0.68 0.75
    emission = 1 1
}
Cool, right?

Now we should have the emission property of the FX ramp start at 66% throttle, ramp up quickly to 75% at 68% throttle, and then gradually increase to full at 100% throttle. Right? Right?

WRONG.

Lets copy-paste that curve into the FloatCurve Editor.

You'll need to change 'emission' to 'key', and delete any tabs in front of them:

key = 0.66 0
key = 0.68 0.75
key = 1 1
What its actually doing is this:

Example2A.jpg

Starts at 66% throttle, ramps waaaaaaaay up to peak at about 170% emission at 79% throttle, and then dips back down to 100% emission at 100% throttle.

Lets fix that.

First, right-click on the last point on the curve, over on the right, and select 'Flat':

Example2B.jpg

Next, right-click on the middle point, and select 'Free Smooth':

Example2C.jpg

Now grab the right handle of the point, and drag it down until the curve stops rising above 1 before it reaches the end:

Example2D.jpg

Now copy the tangents the FloatCurve Editor wrote out in its main window. We end up with:

MODEL_MULTI_PARTICLE
{
    modelName = Squad/FX/shockExhaust_red_small
    transformName = thrustTransform
    emission = 0.66 0
    emission = 0.68 0.75 1.668269 1.668269
    emission = 1 1 0 0
}
And we're done - hopefully this helps you do better with floatCurves!

