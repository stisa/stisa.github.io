+++
title= "Unity3D & accelerometer"
date= "2014-07-08T18:49:26+02:00"
description= "Noob problems with Unity"
tags= ["Unity","accelerometer"]
+++
You know that feeling when you spend ours thinking about an issue, and you finally understand something **very** simple was missing the whole time? Well, I know that feeling very well.

```clike
  accel = Mathf.Round(Mathf.Sqrt(
    (Input.acceleration.x *Input.acceleration.x) +  
    (Input.acceleration.z * Input.acceleration.z) +  
    (Input.acceleration.y*Input.acceleration.y)
  ));  
```

Outputted 0. Zero. Always, even when shaking the phone. And then I realized `Input.acceleration` gives a value between 0 and 1, and then rounding down that number always ended up to zero.

Next time, I might read documentation a bit more carefully, although I didn't find it mentioned on the Unity docs.
