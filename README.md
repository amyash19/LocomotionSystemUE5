# LocomotionSystemUE5 - (WIP)

## Animation Architecture Breakdown

This animation architecture was set up for weapon focused gameplay with the intent for the character to use a wide variety of weapons and items. As such the primary focus is for weapons to drive the character animations. The resulting architecture is a modular, scalable and dynamic setup. 

![Alt text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Animation%20architecture%20breakdown.jpg)

### ItemAnimLayers Interface

The ‘ItemAnimLayers’ interface is an animation layer interface that contains all the animation layers for the main states of the player character such as Idle, movement cycles, jump, etc. This is accessed by both ‘ABP_CharacterBase’ and ‘ABP_itemAnimLayersBase’.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/Screenshot%202023-12-12%20195302.png)

### ABP_CharacterBase

This animation blueprint acts as the base animation blueprint that is assigned to the player character in the character blueprint class. As the player’s inventory changes (e.g unarmed to rifle), the appropriate anim class layer is linked to the current anim instance. The character base animation blueprint contains the main character state logic and does not directly reference any animation assets. 
Instead it implements the ‘ItemAnimLayers’ interface to get access to animation logic for each state. For example, this is how the ‘Idle’ state is set up in the character base animation blueprint:

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/Idle.png)

Noticeably, the Event graph is also empty, since all updates are done solely through the ‘BlueprintThreadSafeUpdate’ function. This is great for memory management especially when dealing with multiple animation blueprints as this structure does.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/threadsfae%20update.png)

### ABP_ItemAnimLayersBase

This animation blueprint acts as the base animation blueprint to handle the animation logic for common weapon types. In this case, the common weapon type is a gun and the specific weapons are a rifle and a pistol. 

Here the main character states are further broken down into more specific animation states. For example the ‘Idle’ state contains ‘Idle’ and ‘Idle stance’ states and deals with transitioning between standing and crouching idle states and animations. This will also contain other related states such as ‘Turn in Place’ and ‘Idle Break’ in the future.  

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/idle2.png)

This is also where animation assets are set up dynamically. Since this system is weapon driven, each weapon has its own set of locomotion animations. This allows us to have a wide variety of weapons and widely different stances and animations for each weapon. It also solves a number of animation issues we would otherwise face if we were blending between multiple overlay states. 

Rather than referencing animation assets directly, this animation blueprint has a set of variables that can be overridden by Child Animation Blueprints. These variables can be found in the "Anim Set - X" categories and will show up in the class defaults of any child animation blueprint.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/anim%20setup.png)

### ABP_Unarmed/Rifle/Pistol

Adding new weapons is as straightforward as creating a child animation blueprint of ‘ABP_ItemAnimLayersBase’ and slotting in animation assets. Examples of this are ‘ABP_Rifle’ and ‘ABP_Pistol’. The unarmed child animation blueprint is the default anim class layer.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/Unarmed.png)

https://github.com/amyash19/LocomotionSystemUE5/assets/123622195/e3519b71-8905-4175-8755-2bdb99a42a6e

## Foot IK Control RIg

Control rig can be used in a multitude of ways. From creating a full body control rig for your character for animations to setting up foot IK for accurate foot placement on terrain. I have created a simple Foot IK control rig that essentially sets a trace from the character's foot to the ground and adjusts the feet and pelvis joints accordingly.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/CR_FootIk.gif)

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/FootIk.gif)
 

## Motion Warping 

Motion warping is a feature that allows us to dynamically adjust a character's root motion to target locations. It's a feature that allows animations to play more naturally and without compromise. For example, adjusting a player's position to vault to different heights or in the case of a dodge roll facing an obstacle, the animation will be scaled down to come to a natural end against the obstacle instead of clipping through it. 

I have implemented vaulting with motion warping. For vaulting, we can break down the target locations needed into three parts: The start of the vault (around the time the character has placed their hand on the platform), the distance the character has to traverse over the platform and where the character lands. These are added as motion warping notifys in an animation montage.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/Vault%20montage.png)

This image shows how vault is adjusted with motion warping. The red debug line indicates where the animation would normally go and it shows that it would go through the platform and this wouldn't work for different heights. The blue debug shows where motion warping is adjusting the character's root motion as dictated by our set target locations.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/vaultmw.png)

https://github.com/amyash19/LocomotionSystemUE5/assets/123622195/9c7b411e-84b3-4336-b7f9-eae46e0c7a75

Motion warping is a powerful and useful tool that can be used for vaulting, mantling, hit reacts, dodging, opening a door, pushing a button, etc. I plan to implemnet mantling next.


## Motion Matching

Motion matching is a relatively new and experimental feature in Unreal Engine. Motion matching is a query based pose selection system. Motion matching can make informed animation pose selctions from a set of animation data without the need to set up transition logic between animation sequences.

A motion matching database asset will work well with a wide variety of locomotion animations covering a range of directional movement.


![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/mm%20database.png)

The Motion Matching Animation Blueprint node is a dynamic alternative to State Machines, or Blendspaces.

![Alt Text](https://github.com/amyash19/LocomotionSystemUE5/blob/main/Images/MM%20abp.png)


## Upcoming work

* Mantling with motion warping.
* Climbing with motion warping and Hand and Foot IK control rig.
* Physical animation (physics-driven animation/physics based animation blending) For example, during a hit react, a character's limbs shouldn't go through the geometry. 
* Locomotion improvements (Turn in place, idle break, sprinting, height based landing, etc).
* Continue work on motion matching.
* Adding anim link layers for melee weapons.
