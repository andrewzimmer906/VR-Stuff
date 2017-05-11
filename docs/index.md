# Quickstart
The demo scenes both use external libraries to enable basic movement controls.

### FPS Demo
The *FPSDemo* uses a FPS Controller from Unity standard assets.  To run this scene you'll need to download this asset through the unity menus:

    Assets -> Import Package -> Characters

### SteamVR Demo
The *SteamVRDemo* uses the Steam VR Camera rig from the Steam VR Asset package which you can download for free here:
[Steam VR Link](https://www.assetstore.unity3d.com/en/#!/content/32647).


### GearVR Demo
The *GearVRDemo* uses the Open VR Camera rig from the Open VR API package which you can download for free here by selecting "Oculus Utilities for Unity 5":
[Open VR Link](https://developer.oculus.com/downloads/unity/).


*Important*
If you demo a VR scene, you'll also need to enable support for that particular library by opening

    Window -> Portal State Manager

and selecting your platform.


# How to set up a multi-dimensional scene

The best way is to use one of the demo scenes and copy assets around, but if you'd like to set up your own, the steps are below.

## Create your dimensions.

The 'Dimension' script is added to objects to define many worlds coexisting in the same space, but only accesible through portals. The easiest way to create one is to use a demo scene, but if you'd like to make one from scratch.

1. Create an empty game object.
2. Add the 'Dimension' component to it.
3. Set 'Initial Dimension' to true if you'd like your player to start in this dimension.
4. Add your main camera as a child of the Initial Dimension object.

NOTE: The dimensions use the last layers in your layer mask, so if you are using all 32 layers they will not work.

## Create your portals

The 'Portal' script is responsible for rendering to another dimension and allowing you to transition between dimensions.

1. Grab a portal from the Portal-Prefabs folder and add it to your scene.
2. Inside the prefab, there is an object called 'portal' that contains the portal script components.
3. Make sure the 'main camera' variable is set to your main camera.
4. Set 'Dimension 1' and 'Dimension 2' to the dimensions you'd like to connect with the portal.

NOTE: Make sure to add a skybox component to your main camera if you want to define custom skyboxes for different dimensions.

## Getting ready for Steam VR

The portal code works in both normal and stereo (VR) camera modes. If you are using Steam VR setup is identical to above with one caveat.

1. Make sure to use the 'eyes' camera as your main camera in the portal "Main Camera' variable.
2. Add a skybox to your eyes camera.
3. Tag your controllers and set that tag in the "Ignore Rigidbody Tag" if you'd like to manipulate your portal with touch controllers. This will keep the portal from deforming away from your controllers.

NOTE: Make sure to add a skybox component to your main camera if you want to define custom skyboxes for different dimensions.

## Getting ready for Gear VR

The portal code works in both normal and stereo (VR) camera modes. If you are using Gear VR setup is identical to above with a couple of additional steps.

1. Make sure to use the 'CenterEyeAnchor' camera as your main camera in the portal "Main Camera' variable.
2. Add the "GearCameraRenderInfo" to your 'CenterEyeAnchor' camera.
3. Add the "GearGrabObject" component to your 'CenterEyeAnchor' camera.
4. Add a skybox to your 'CenterEyeAnchor' camera.

NOTE: Make sure to add a skybox component to your main camera if you want to define custom skyboxes for different dimensions.

## Support

If you have any additional questions or comments, please contact me directly at andrewzimmer906@gmail.com and I'll do my best to help you out.

Cheers!

# Class Structure
## Portal.cs

The Portal class handles most of the portal interactions and rendering.
One thing to note is that when in VR the portal renders in full stereo which can degrade performance if many portals are all visible at the same time.

*Dimension 1* - This sets the starting dimension for the portal. The user will transition
from dimension1 to dimension2 and back again.

*Dimension 2* - This sets the end dimension for the portal.

*Main Camera* - This should be set to the main camera in the scene.
For FPSController this is:

    FPSController -> FirstPersonCharacter

For Steam VR this is:

    [CameraRig] -> Camera (head) -> Camera (eye)

And for Open VR this is

    OVRCameraRig -> TrackingSpace -> CenterEyeAnchor

*Render Quality* - This is a multiplier for the size of the textures used on the portal. Increase this value to increase quality. Lower this value to increase performance. Default is four.

To ensure smooth transition between worlds, the portal mesh deforms away from the main camera as it passes through. The values below allow you to adjust the transformation distance and amount.

*Maximum Deform Range* - The maximum distance at which the portal begins to deform away from the main camera to avoid clipping.

*Deform Power* - The power with which the portal deforms away from the camera. If you see a flicker as you pass through, increasing this variable is usually a good fix.

*Ignore Rigidbody Tag* - The portal deforms away from rigidbodies, but if you give them a tag and set it here, it will ignore them. This is highly useful for items like your Vive Controllers.

*Always Visible Mask* - This mask defines the parts of the scene that are visible in all dimensions. You'll usually want to make sure at least "Default" is set. Always set the layers individually, don't use Everything.

*Enable Oblique Projection* - Oblique Projection clips the camera exactly to the portal rectangle. This is really good if you've got nearby objects. If you aren't using Oblique Projection, the portal clips to start rendering in a circle around itself.

The altered projection also messes with procedural skyboxes, so enable at your own risk.

## Dimension.cs

This class is used to mark a "Dimension". On start this component creates a custom layer and sets itself and all of its children onto that layer. This allows the program to hide dimensions using layer culling on the main camera.
