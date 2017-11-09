//A custom script header for advanced movement and effects

//Lweapons.zh Version 1.2.7

+-------+
| USAGE |
+-------+

Lweapons.zh is used to create lweapons independent of ffcs that can have a variety of behaviors.

A Lweapons.zh script should begin by calling one of the functions that launches the lweapons.
This sets up much of the lweapon's internal data. By calling UpdateLWZH1() before waitdraw and
UpdateLWZH2() in your global script, you activate the automatic handling of these lweapons. 

The global variables are used to set things such as flags for various behaviors,movement types,
the life of the lweapon, what it does on dying as well as important data for proper combo interaction.

Most lweapon attributes can be altered by these functions, to create a lweapon of the size and appearance
that you desire.

Most of the lweapons do not have to be of their intended type and when it comes to melee lweapons;
using the original type is impossible. The sprites for the lweapons should have at least three tiles,
in the order vertical, horizontal and diagonal.

There are a number of functions used to create and control lweapons. They
can be assigned a number of simple movements and can be made to vanish and
spawn additional lweapons. These behaviors require the weapons to be passed
into UpdateLWZH2() each frame.

To prevent conflicts with Lweapons.zh, scripts should avoid using the index
lweapon->Misc[LW_ZH_I_FLAGS]. This index is set to 15 by default, but they can be changed.

Functions, variables, and constants whose names start with __ are for internal
use and may be changed or removed in future versions without warning.

+--------+
| GLOBAL |
+--------+

These functions should be called in the active global script.

void UpdateLWZH1()
	*Call before Waitdraw()
	*Handles things like reactivating Link's collision detection.
	*Also, makes it where reflected lweapons don't hurt you.

void UpdateLWZH2()
	*Call after Waitdraw()
	*Handles all lweapon behaviors modified by the other scripts.
	
+-------+
| FLAGS |
+-------+

These are lweapon flags that can be set via script.

LW_ZH_I_FLAGS

 * LWF_ITEM_PICKUP
 * 	This lweapon picks up items when it contacts them.
 *
 * LWF_PIERCES_ENEMIES      
 *	This lweapon pierces enemies.
 *
 * LWF_STUNS_ENEMIES        
 *	This lweapon stuns enemies vulnerable to it's weapon type,
 *
 * LWF_ZERO_G      
 *	This lweapon ignore gravity.
 *	
 * LWF_NORMALIZE	
 *	The sprite for this lweapon is not rotated based on Link's direction.
 *
 * LWF_INSTA_DELIVER       
 *	Uses in conjunction with LWF_ITEM_PICKUP.
 *	If the lweapon contacts an item, Link instantly collects it.
 *
 * LWF_LINK_NO_COLL         
 *	While this lweapon is active, Link's collision detection is off.
 *
 * LWF_LINK_FLOATS
 *	While this lweapon is active, Link floats above the surface.
 *	Floats in midair if sideview gravity is active.
 *
//* LWF_CAN_REFLECT         
//*	This lweapon is reflected off of magic mirrors.
//*	Not yet fully supported.
//*
//* LWF_POISON
//*  Uses to make an Lweapon poison an enemy.
//*  Not yet fully supported.
 
 
 LW_ZH_I_FLAGS_2
 
 * LWF_LEVEL_1          	   
 *	Simulated level 1 of a particular weapon type.
 *
 * LWF_LEVEL_2		      
 *	Simulated level 2 of a particular weapon type.
 *
 * LWF_LEVEL_3
 *	Simulated level 3 of a particular weapon type.
 *
 * LWF_LEVEL_4
 *	Simulated level 4 of a particular weapon type.
 *
 * LWF_KNOCKBACK_OFF
 *	Link isn't affected by knockback while this lweapon is active.
 *
 * LWF_NO_COLLISION         
 *	Turns off this lweapon's collision detection.
 *
 * LWF_RETURN
 *	Signals that this lweapon is returning to Link.
 *	Used internally.
 *
 * LWF_TEMP_PIERCE
 *	Signals that this lweapon has hit an enemy which ignores it.
 *  Used internally to counter death effects.
 *
//* LWF_WAS_REFLECTED
//*  Signals that the lweapon is currently being reflected.
//*  Used to keep it from happening every frame.
//*  Not yet fully supported.
//*
//* LWF_IS_REFLECTED
//*	This lweapon has been reflected off a magic mirror at some point.
//*  Not yet fully supported.
//*
//* LWF_IS_MELEE             
//*	This lweapon is a melee weapon.
//*	Not yet supported.
//*
//*
//* LWF_LINK_FROZEN         
//*	While this lweapon is active, Link can't move.
//*	Mostly used with hookshot lweapons.
//*	Not yet supported.

+----------+
| MOVEMENT |
+----------+

Handles lweapon movement.

void SetLWeaponMovement(lweapon wpn, int type, int arg, int arg2)
 * LWM_SINE_WAVE         
 *    Move in a sine wave.
 *    arg: Amplitude
 *    arg2: Angular frequency in degrees per frame
 * 
 * LWM_THROW
 *    Arc through the air as if thrown. The weapon stops moving when it hits
 *    the ground.
 *    arg: Initial upward velocity; use -1 to make the weapon travel the
 *         distance to Link
 *    arg2: What happens to the lweapon when it hits the ground.
 *			LWMF_DIE: The weapon will die on impact rather than simply stopping.
 *			LWMF_BOUNCE: This weapons bounces for a bit before coming to a rest.
 *			LWMF_REST: This lweapon rests where it lands.
 * 
 * LWM_FALL
 *    Fall straight down. The weapon stops moving when it hits the ground.
 *    arg: Initial height
 *	  arg2: What happens to the lweapon when it hits the ground.
 *			LWMF_DIE: The weapon will die on impact rather than simply stopping.
 *			LWMF_BOUNCE: This weapons bounces for a bit before coming to a rest.
 *			LWMF_REST: This lweapon rests where it lands.
 *
 * LWM_HOMING
 *    Turn toward an enemy each frame, if any exist..
 *    arg: Maximum rotation per frame in radians
 *    arg2: Homing time in frames - after this many frames, the weapon
 *          will die. Use -1 to home indefinitely.
 * 
 * LWM_CIRCLE          
 *		This lweapon circles Link.
 *		arg: Radius of the circle.
 *		arg2: Speed of rotation. Use negative for counterclockwise.
 * 
 * LWM_BRANG             
 *		This lweapon moves like a boomerang.
 *		arg: Distance the boomerang can travel, in pixels.
 *		arg2: Degrees this boomerang turns every frame when returning to Link.
 *
 * LWM_DUAL_FX
 * 		This lweapon sets off more than one type of flag.
 *		arg: First type of Lweapon flag.
 *		arg2: Second type of lweapon flag.
 *		Not yet fully bug-free.
 * 
 * LMM_CARRY
 *		This lweapon is carried above Link's head.
 *		Not yet fully supported.
 *
 //*	LWM_FOLLOW
 //*		Follows the movement of another Lweapon.
 //*		arg: Lweapon type to follow.
// *
 //*	LWM_REST
 //*		Stays in one location or doesn't move relative to Link.
 //*		arg:Types of resting lweapon
 //*			LW_R_GRENADE
 //*				A grenade held above Link's head.
 //*			
 //*			LW_R_SOMARIA
 //*				A block produced by the Cane of Somaria.
 //*
 //*			LW_R_MAGNET
 //*				Acts like magnetic gloves.
 //*
 //* LWM_HOOKSHOT         
 //*		This lweapon moves like a hookshot.
 //*		Not yet supported.
 //*		arg; Length of the chain, in tiles.
 //*		arg2: Sprite of the chain.
 //*
 //* LWM_MELEE             
 //*		This weapon acts like a melee weapon.
 //*		Most of these are not yet supported.
 //*		arg:Type of melee weapon
 //*			LW_IS_SWORD
 //*				Acts like a sword
 //*			
 //*			LW_IS_HAMMER
 //*				Acts like a hammer.
 //*
 //*			LW_IS_WAND
 //*				Acts like a wand.
 //*		arg2:Misc weapon changes. OR these together or use zero for none.
 //*			LW_HAS_SLASH
 //*				Used to make sword slash.
 //*
 //*			LW_HAS_PERIL
 //*				Used to shoot peril beasm
 //*
 //*			LW_HAS_WHIM_RING
 //*				You have the whimsical ring
 //*
 //*			LW_HAS_BOOK
 //*				You have the magic book.
  
+----------+
| LIFESPAN |
+----------+
 
 void SetLWeaponLifespan(lweapon wpn, int type, int arg)
 * This controls the conditions under which a weapon dies. "Dying" does not mean
 * the weapon is removed, but that its scripted movement is no longer handled,
 * and, optionally, a death effect is activated. Use one of the LWL_ constants
 * for the type argument. The effect of arg depends on the type.
 * 
 * LWL_TIMER
 *    Die after a certain amount of time.
 *    arg: Time, in frames
 *
 * LWL_SOLID
 *	  Dies after running into a solid combo.
 *	  srg: Combo Type which can be passed through
 *
 * LWL_MP_COST
 *	 Dies if Link->MP falls below a certain amount.
 *	 Automatically adjusts for half magic.
 *	 arg: How often to drain one MP.
 *
 * LWL_EDGE
 *	 Dies if weapon passes the edge of the screen.
 *	 arg: With boomerangs, set the sprite for sparkles.
 *
 *
  
+-------+
| DEATH |
+-------+
 
 void SetLWeaponDeathEffect(lweapon wpn, int type, int arg)
 * This determines what happens when the weapon dies. The type argument should
 * be one of the LWD_ constants. The effect of arg depends on the type.
 *
 * LWD_VANISH
 *    The weapon is removed.
 *    arg: No effect
 * 
 * LWD_AIM_AT_NPC
 *    The weapon pauses for a moment, then aims at a random enemy is any exist.
 *    arg: Delay
 * 
 * LWD_EXPLODE
 *    The weapon explodes.
 *    arg: Explosion damage
 * 
 * LWD_SBOMB_EXPLODE
 *    The weapon explodes like a super bomb.
 *    arg: Explosion damage
 * 
 * LWD_4_FIREBALLS_HV
 *    Shoots fireballs horizontally and vertically.
 *    arg: Fireball sprite
 * 
 * LWD_4_FIREBALLS_DIAG
 *    Shoots fireballs at 45-degree angles.
 *    arg: Fireball sprite
 * 
 * LWD_4_FIREBALLS_RANDOM
 *    Randomly shoots fireballs either vertically and horizontally or at
 *    45-degree angles.
 *    arg: Fireball sprite
 * 
 * LWD_8_FIREBALLS
 *    Shoots fireballs horizontally, vertically, and at 45-degree angles
 *    arg: Fireball sprite
 * 
 * LWD_FIRE
 *    Leaves a single, immobile fire
 *    arg: Fire sprite
 * 
 * LWD_4_FIRES_HV
 *    Shoots fires horizontally and vertically.
 *    arg: Fire sprite
 * 
 * LWD_4_FIRES_DIAG
 *    Shoots fires at 45-degree angles.
 *    arg: Fire sprite
 * 
 * LWD_4_FIRES_RANDOM
 *    Randomly shoots fires either vertically and horizontally or at
 *    45-degree angles.
 *    arg: Fire sprite
 * 
 * LWD_8_FIRES
 *    Shoots fires horizontally, vertically, and at 45-degree angles
 *    arg: Fire sprite
 * 
 * LWD_SHAKES_SCREEN
 *	  Shakes the screen
 *	  arg: How much to shake the screen.
 *
//* LWD_FREEZE
//*    Creates an ice block that freezes the enemy.
//*    arg: Sprite of ice block.
//*

+---------+
| EFFECTS |
+---------+

This is to create other lweapon effects, such as continous sounds and launching ffc scripts..

void SetLWeaponSFX(lweapon wpn, int type, int arg)
*	This lweapon continously makes a sound while active.
*	type: Sound to make.
*   arg: How often to make sound.


void SetLWeaponScript(lweapon wpn, int script_num, int Misc_Num){
*	This lweapon has an attached ffc script.
*	script_num: What ffc script to run.
*	Misc_Num: A unique number to identify this lweapon.
 
+-------+
| OTHER |
+-------+

These functions are mostly used internally and probably shouln't be called in your script.

lweapon FindMiscLWeapon(int Weapon_ID)
* 	Finds an lweapon with a Miscellaneous value set to Weapon_ID.
 
 void SetLWeaponDir(lweapon wpn)
 * Set the direction of an angled eweapon so that it interacts correctly
 * with shields. This is normally handled automatically, but you may need
 * to use it if you script a weapon's movement yourself.

void SetLWeaponRotation(eweapon wpn)
 * Rotate the weapon's sprite.

void KillLWeapon(lweapon wpn)
 * Kill the lweapon, stopping scripted movement and activating any
 * death effects.
 
void RemoveLWeaponType(int type)
*	Any lweapon of wpn->ID==type is removed.

void SetLWeaponFlag(lweapon wpn, int flag)
*	Sets the desired flag on any lweapon to true.

void UnSetLWeaponFlag(lweapon wpn, int flag)
*	Sets the desired flag on any lweapon to false.

void SetLWeaponFlag2(lweapon wpn, int flag)
*	Sets the desired flag on any lweapon to true.

void UnSetLWeaponFlag2(lweapon wpn, int flag)
*	Sets the desired flag on any lweapon to false.

int LWDefense(int type)
*	Determines what defense index of npc->Defense this lweapon is associated with.
*	Not fully supported.

int Calc(int x1, int x2, int numParts)
*	Determines absolute value distance between coordinates x1 and x2, then divides by numParts.

int NumMiscLWeapons(int index,int flag){
*	Counts how many Lweapons have the flag of a certain index set.

bool ComboFIAtWpn(lweapon wpn,int flag)
*	Determines if combo at location of lweapon has a certain placed or internal flag.

bool ComboFITAtWpn(lweapon wpn,int flag,int type)
*	Determines if combo at location of lweapon has a certain placed or internal flag.
*	Also, checks for the right combo type.

bool ComboCollision (int loc, int x1, int y1, int x2, int y2)
* 	Determines is a combo has intersected with an lweapon.
*	Uses wpn->X, wpn->Y, wpn->X+wpn->HitWidth and wpn->Y+wpn->HitHeight as four last args.

bool GetLWeaponFlag(lweapon wpn, int flag)
*	Determines if a lweapon has a flag set or not.

bool GetLWeaponFlag2(lweapon wpn, int flag)
*	Determines if a lweapon has a flag set or not.

+------------+
| DEPRECATED |
+------------+

These functions may be used by other scripts and are preserved in Lweapons.zh purely for compatibility.

bool Between(int loc,int greaterthan, int lessthan)
*	Determines is a value is greater than one value and less than another.
*	arg: Value
*	arg2: Greater than this.
*	arg3: Less than this.

bool ScreenFlagTest(int category,int flag)
*	Checks if a screen flag in a certain category is checked.
*	Used to test for whether secrets are permanent.

bool OnScreenEdge(lweapon wpn)
* 	Checks to see if a weapon is on the edge of the screen.

void ItemSetAt(int itemset,int loc)
* 	Creates an item from itemset and the coordinates of loc.

int LWBlockType(lweapon wpn)
*	Determines what combo types block this lweapon.

int LWBlockType(int wpn_id,lweapon wpn)
*	Determines what combo types block an lweapon with multiple types.

//int GetComboSprite(int type)
//*	Determines what sprite is created if a combo type is destroyed.
//*	Not fully supported.

//int GetComboSound(int type)
//*	Determines what sound is made if a combo type is destroyed.
//*	Not fully supported.

//int CreateGraphicAt(int sprite, int x, int y)
//*	Creates indicated sprite and x and y coordinates.
