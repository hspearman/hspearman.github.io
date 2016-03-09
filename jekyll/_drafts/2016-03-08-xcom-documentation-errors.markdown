---
layout: post
title: "XCOM 2 Modding: How to Fix Some Mistakes in the UI Doc"
date: 2016-03-08 19:03:35 -0600
categories: xcom2 modding
---
XCOM 2, the sequal to XCOM: Enemy Unknown, boasts day-one modding support that shipped on release day.

This modding support features an SDK on Steam Workshop, along with PDFs to guide modders through the process. Unfortunately, the documentation can be terse-- and sometimes, incorrect.

For my XCOM 2 UI mod, I turned to Firaxis' PDF (XCOM 2 SDK > Documentation > Tech folder > XCOM2Mods_UserInterface.pdf) that provides some details about UI modding.

Naturally, I tried to run some sample code before digging deeper. **Manipulating current UI elements** contains this sample code:

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
		// Let’s spawn a new rectangle graphic inside of the research progress bar.
		BGBox = Spawn(class'UIPanel', PowerCoreScreen);
		BGBox.InitPanel('BGBoxSimple', class'UIUtilities_Controls'.const.MC_X2BackgroundSimple);
		BGBox.AnchorMiddleLeft();
		BGBox.SetPosition(100, 100); // This is relative to the anchor we just set.
		BGBox.SetSize(400, 200);
		
		// Now we can put a new text field over that rectangle, also inside the research bar.
		DisplayText = Spawn(class'UIText', PowerCoreScreen).InitText();
		DisplayText.SetY(BGBox.Y + 10);
		RefreshDisplayText();
	}

	Event OnReceiveFocus()
	{
		// If the data to display may change on other screens, you need to refresh
		// your text field when you return to this screen.
		RefreshDisplaytext();
	}

	function RefreshDisplayText()
	{
		DisplayText.SetText( "My new value" /*update the value to what you want to show*/ );
	}
	
	defaultproperties
	{
		ScreenClass = class'UIFacility_PowerCore';
	}
	
If you build this code, some errors occur. Here are the changes you need to make:

1. **Remove `local UIText DisplayText`**

   On build, the warning `Variable declaration: 'DisplayText' conflicts with previously defined field in 'UIAddAHudWidgetToPowerCore'` appears.

   UnrealScript has two kinds of variables: **Instance** and **Local**. Instance variables exist object-wide, while local variables only exist inside the function that declares them.

   We want object-wide access to set`DisplayText` in `OnInit(UIScreen Screen)` and reference it in `RefreshDisplayText()`.

   `local UIText DisplayText` confuses the compiler by overshadowing the instance variable of the same name. So we remove it.
   
2. **Change `Event OnReceiveFocus()` to `event OnReceiveFocus(UIScreen Screen)`**

   On build, the error `Redefinition of 'event OnReceiveFocus' differs from original; different number of parameters` appears.

   The method signature in `UIScreenListener.uc` is actually `event OnReceiveFocus(UIScreen Screen)`. Since our class inherits from  `UIScreenListener`, our method signature should match to override it.
   
3. **Replace `Spawn` with `Screen.Movie.Pres.Spawn`**

   On build, the error `Bad or missing expression for token: Spawn, in '='` appears.

   As mentioned [here](http://forums.2k.com/showthread.php?4212176-Problems-with-the-example-mod-code), `Spawn()` exists outside of `UIScreenListener`. We declare the scope (i.e. `Screen.Movie.Pres`) so the compiler knows where to find the method.

4. **Change `BGBox.AnchorMiddleLeft()` to `BGBox.SetAnchor(class'UIUtilities'.const.ANCHOR_TOP_LEFT)`**

   In `UIPanel.uc`, similar methods like `AnchorBottomRight()` are defined. Internally, they call `SetAnchor()` with constants from `UIUtilities.uc`. Notice `ANCHOR_MIDDLE_LEFT` is defined inside `UIUtilites`, but `AnchorMiddleLeft()` does not exist.
   
   Since `AnchorMiddleLeft()` doesn't exist, we call `SetAnchor(class'UIUtilities'.const.ANCHOR_MIDDLE_LEFT)` on `BGBox` manually.

After these fixes, the sample code should work. It looks like this:

	class UIAddAHudWidgetToPowerCore extends UIScreenListener;

	var UIText DisplayText;

	event OnInit(UIScreen Screen)
	{
		local UIFacility_PowerCore PowerCoreScreen;
		local UIPanel BGBox;
		PowerCoreScreen = UIFacility_PowerCore(Screen);
		
		// Example accessing a child.
		PowerCoreScreen.m_kResearchProgressBar.SetAlpha(50);
		// Let’s spawn a new rectangle graphic inside of the research progress bar.
		BGBox = Screen.Movie.Pres.Spawn(class'UIPanel', PowerCoreScreen);
		BGBox.InitPanel('BGBoxSimple', class'UIUtilities_Controls'.const.MC_X2BackgroundSimple);
		BGBox.SetAnchor(class'UIUtilities'.const.ANCHOR_MIDDLE_LEFT);
		BGBox.SetPosition(100, 100); // This is relative to the anchor we just set.
		BGBox.SetSize(400, 200);
		
		// Now we can put a new text field over that rectangle, also inside the research bar.
		DisplayText = Screen.Movie.Pres.Spawn(class'UIText', PowerCoreScreen).InitText();
		DisplayText.SetY(BGBox.Y + 10);
		RefreshDisplayText();
	}

	event OnReceiveFocus(UIScreen Screen)
	{
		// If the data to display may change on other screens, you need to refresh
		// your text field when you return to this screen.
		RefreshDisplaytext();
	}

	function RefreshDisplayText()
	{
		DisplayText.SetText( "My new value" /*update the value to what you want to show*/ );
	}

	defaultproperties
	{
		ScreenClass = class'UIFacility_PowerCore';
	}
	
Build, launch XCOM 2 in debug-mode, and select your mod. Navigate to the Research tab via Strategy. This should display:


Not very pretty ... but hopefully it gives you a working start for UI mods.
	
Happy modding!
