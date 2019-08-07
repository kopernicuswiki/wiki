---
title: Comet Tails
subtitle: Rosettas for us all
---


The `CometTails { }` subnode is a part of Kopernicus Expansion and allows you to add Dust and Ion trails to bodies.

**Example**
This is the default configuration.
```
Body
{
	CometTails
	{
		CometTail
		{	
			type = CometTailType // Wheter the tail is made of Ions ar Dust. Required.
      color = Color // The color of the tail in RGBA. Default is white.
      rimPower = Single // How close the atmosphere rim stays to the edge of the body. The higher the number, the closer it is. Default is 
      distortion = Single //
      alphaDistortion = Single
      zDistortion = Single
      lacunarity = Single
      gain = Single
      radius = Single
      length = Single
      opacityCurve
      {
        // FloatCurve
      }
      brightnessCurve
      {
        // FloatCurve
      }
      
		}
	}
}
```