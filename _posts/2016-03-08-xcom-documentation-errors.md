---
layout: post
title: "XCOM 2 Modding: How to Fix Some Mistakes in the UI Doc"
date: 2016-03-10 21:10:00 -0600
---
As a programmer and a huge XCOM fan, I was pretty excited that XCOM 2 was released with day-one modding support.

For my own mod, I ran some sample code as a starting point. Unfortunately, it didn't exactly compile-- in fact, the sample code is broken.

I finally got it working, but it took some trial-and-error and plenty of struggling. To save everyone else the trouble, I've documented the changes here.


## Finding the Sample Code

Under the **Manipulating current UI elements** section of the UI doc (*XCOM 2 SDK > Documentation > Tech folder > XCOM2Mods_UserInterface.pdf*), this sample code is found:

~~~ javascript
class UIAddAHudWidgetToPowerCOre extends UIScreenListener;

var UIText DisplayText;

event OnInit(UIScreen Screen)
{
	local UIFacility_PowerCore PowerCoreScreen;
	local UIText DisplayText;
	local UIPanel BGBox;
	PowerCoreScreen = UIFacility_PowerCore(Screen);
	
	// Example accessing a child.
	PowerCoreScreen.m_kResearchProgressBar.SetAlpha(50);
	// Let’s spawn a new rectangle graphic
	// inside of the research progress bar.
	BGBox = Spawn(class'UIPanel', PowerCoreScreen);
	BGBox.InitPanel(
		'BGBoxSimple',
		class'UIUtilities_Controls'.const.MC_X2BackgroundSimple);
	BGBox.AnchorMiddleLeft();
	// This is relative to the anchor we just set.
	BGBox.SetPosition(100, 100); 
	BGBox.SetSize(400, 200);
	
	// Now we can put a new text field over that rectangle,
	// also inside the research bar.
	DisplayText = Spawn(class'UIText', PowerCoreScreen).InitText();
	DisplayText.SetY(BGBox.Y + 10);
	RefreshDisplayText();
}

Event OnReceiveFocus()
{
	// If the data to display may change on other screens,
	// you need to refresh your text field
	// when you return to this screen.
	RefreshDisplaytext();
}

function RefreshDisplayText()
{
	/*update the value to what you want to show*/ 
	DisplayText.SetText( "My new value" );
}

defaultproperties
{
	ScreenClass = class'UIFacility_PowerCore';
}
~~~

## Making the Changes
	
If you build this code, some errors occur. Here are the changes you need to make:

1. **Remove `local UIText DisplayText`**

   On build, the warning `Variable declaration: 'DisplayText' conflicts with previously defined field in 'UIAddAHudWidgetToPowerCore'` appears.

   UnrealScript has two kinds of variables: **Instance** and **Local**. Instance variables exist object-wide, while local variables only exist inside the function that declares them.

   We want object-wide access to set`DisplayText` in `OnInit(UIScreen Screen)` and reference it in `RefreshDisplayText()`.

   `local UIText DisplayText` confuses the compiler by overshadowing the instance variable of the same name. So we remove it.
   
2. **Change `Event OnReceiveFocus()` to `event OnReceiveFocus(UIScreen Screen)`**

   On build, the error `Redefinition of 'event OnReceiveFocus' differs from original; different number of parameters` appears.

   The method signature in **UIScreenListener.uc** is actually `event OnReceiveFocus(UIScreen Screen)`. Since our class inherits from  `UIScreenListener`, our method signature needs to match to override it.
   
3. **Replace `Spawn` with `Screen.Movie.Pres.Spawn`**

   On build, the error `Bad or missing expression for token: Spawn, in '='` appears.

   As mentioned [here](http://forums.2k.com/showthread.php?4212176-Problems-with-the-example-mod-code), `Spawn()` exists outside of `UIScreenListener`. We declare the scope (i.e. `Screen.Movie.Pres`) so the compiler knows where to find the method.

4. **Change `BGBox.AnchorMiddleLeft()` to `BGBox.SetAnchor(class'UIUtilities'.const.ANCHOR_TOP_LEFT)`**

   In **UIPanel.uc**, similar methods like `AnchorBottomRight()` are defined. Internally, they call `SetAnchor()` with constants from **UIUtilities.uc**. Notice `ANCHOR_MIDDLE_LEFT` is defined inside `UIUtilites`, but `AnchorMiddleLeft()` does not exist.
   
   Since `AnchorMiddleLeft()` doesn't exist, we call `SetAnchor(class'UIUtilities'.const.ANCHOR_MIDDLE_LEFT)` on `BGBox` manually.
   
5. **Add `BGBox.Show` to `OnReceiveFocus()`**

	From my understanding, intializing a UIPanel isn't enough. We need to continually refresh via `BGBox.Show()` as the UI redraws itself.
	
	This logic is already applied to `DisplayText`.
	
## The Final Product

After these fixes, the sample code should work. Here's the final result:

~~~ javascript
class UIAddAHudWidgetToPowerCore extends UIScreenListener;

var UIText DisplayText;
var UIPanel BGBox;

event OnInit(UIScreen Screen)
{
	local UIFacility_PowerCore PowerCoreScreen;
	PowerCoreScreen = UIFacility_PowerCore(Screen);
	
	// Example accessing a child.
	PowerCoreScreen.m_kResearchProgressBar.SetAlpha(50);
	// Let’s spawn a new rectangle graphic
	// inside of the research progress bar.
	BGBox = Screen.Movie.Pres.Spawn(class'UIPanel', PowerCoreScreen);
	BGBox.InitPanel(
		'BGBoxSimple',
		class'UIUtilities_Controls'.const.MC_X2BackgroundSimple);
	BGBox.SetAnchor(class'UIUtilities'.const.ANCHOR_TOP_LEFT);
	// This is relative to the anchor we just set.
	BGBox.SetPosition(100, 100); 
	BGBox.SetSize(400, 200);
	
	// Now we can put a new text field over that rectangle,
	// also inside the research bar.
	DisplayText = Screen.Movie.Pres.Spawn(
		class'UIText',
		PowerCoreScreen).InitText();
	DisplayText.SetY(BGBox.Y + 10);
	RefreshDisplayText();
}

event OnReceiveFocus(UIScreen Screen)
{
	// If the data to display may change on other screens,
	// you need to refresh your text field
	// when you return to this screen.
	RefreshDisplaytext();
	BGBox.Show();
}

function RefreshDisplayText()
{
	/*update the value to what you want to show*/
	DisplayText.SetText( "My new value" );
}

defaultproperties
{
	ScreenClass = class'UIFacility_PowerCore';
}
~~~

## Testing the Changes
	
Click Debug > Start Debugging. Select your mod and start the game. From here, launch Strategy mode and navigate to the Research tab. This should display:


![Sample UI Mod]({{ site.baseurl }}/public/imgs/xcom2_research_bar_mod.png)


Not very pretty ... but it works.

Incase you're confused, that black box is indeed our UIPanel. It just looks weird because it's alpha is 100% (`BGBox.SetAlpha(50)` makes it look a little more normal).

Hopefully, this post saves you some time and provides a good starting point.
	
Until next time. Happy modding!
