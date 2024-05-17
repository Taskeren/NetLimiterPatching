# NetLimiter Patching

This is a repository collecting the patched DLLs for NetLimiter.

## NOTICE

**My currently version of NetLimiter is 5.2.6.0**, and all of them are based on this.
Don't report any problem if you are using other versions.

You can do the same thing on different versions on your own, using dnSpy (or dnSpyEx) to change the codes in the methods.

## Usage

### [API-Enabler](https://github.com/Taskeren/API-Enabler)

With the API-Enabler, we can install and patch it in one go.

Run `Install.ps1` with administrator privileges, and wait for installation.

If it fails to install and patch, you can report the problem to the issues with detailed information.

### Manual

1. Make sure your NetLimiter version is correct.
2. You can download these files and drag into your NetLimiter folder, **when the service is stopped**.

You can start/stop the service by `net start nlsvc` and `net stop nlsvc`, in cmd/powershell as administrator.

## Changes

### NetLimiter.dll

Bypassed the expiration, where you can use the NL, but every time you open the gui, an expiration message is showing.

This is achieved by modifying `get_IsExpired` method, which I set it always return `false`.

### NLClientApp.Core.dll

Bypassed the BattlEye detection (for Destiny 2). I don't know how it works exactly, but I knew it from [this video](https://www.youtube.com/watch?v=avkBlOQ_kUI).

### NLClientApp.Modules.dll

Enhanced the hotkey features, to support <kbd>Shift + N</kbd> and <kbd>Alt + N</kbd>.

In `HotKeySelector`, change the method `StackPanel_KeyDown`:

To add shift combination feature, just simply modify this line:

```diff
- if ((Keyboard.Modifiers != (ModifierKeys.Alt | ModifierKeys.Control) && Keyboard.Modifiers != ModifierKeys.Control) || e.Key == Key.LeftAlt || e.Key == Key.LeftCtrl || e.Key == Key.RightAlt || e.Key == Key.LeftCtrl || e.Key == Key.LWin || e.Key == Key.RWin || e.Key == Key.LeftShift || e.Key == Key.RightShift)
+ if ((Keyboard.Modifiers != (ModifierKeys.Alt | ModifierKeys.Control) && Keyboard.Modifiers != ModifierKeys.Control && Keyboard.Modifiers != ModifierKeys.Shift) || e.Key == Key.LeftAlt || e.Key == Key.LeftCtrl || e.Key == Key.RightAlt || e.Key == Key.LeftCtrl || e.Key == Key.LWin || e.Key == Key.RWin || e.Key == Key.LeftShift || e.Key == Key.RightShift)
```

And if you need to add alt combination, you need some workaround. Because if I press any key comb with alt, the key of event will be `Key.System`, which I'm really confused.
So the alt combinations will be set when key is pressed without the modifiers (Alt, Shift, Ctrl).

```diff
- if ((Keyboard.Modifiers != (ModifierKeys.Alt | ModifierKeys.Control) && Keyboard.Modifiers != ModifierKeys.Control) || e.Key == Key.LeftAlt || e.Key == Key.LeftCtrl || e.Key == Key.RightAlt || e.Key == Key.LeftCtrl || e.Key == Key.LWin || e.Key == Key.RWin || e.Key == Key.LeftShift || e.Key == Key.RightShift)
+ if ((Keyboard.Modifiers != (ModifierKeys.Alt | ModifierKeys.Control) && Keyboard.Modifiers != ModifierKeys.Control && Keyboard.Modifiers != ModifierKeys.Shift) || e.Key == Key.LeftAlt || e.Key == Key.LeftCtrl || e.Key == Key.RightAlt || e.Key == Key.LeftCtrl || e.Key == Key.LWin || e.Key == Key.RWin || e.Key == Key.LeftShift || e.Key == Key.RightShift)
{
+	if (e.Key == Key.LeftAlt || e.Key == Key.LeftCtrl || e.Key == Key.RightAlt || e.Key == Key.LeftCtrl || e.Key == Key.LWin || e.Key == Key.RWin || e.Key == Key.LeftShift || e.Key == Key.RightShift || e.Key == Key.System)
+	{
+		// to skip the keys of modifiers like Ctrl, Shift and Alt. These are not valid!
		this.Key = Key.None;
		this.ModifierKeys = ModifierKeys.None;
		this.BtnSet.IsEnabled = false;
+	}
+	else
+	{
+		this.Key = e.Key;
+		this.ModifierKeys = ModifierKeys.Alt;
+		this.BtnSet.IsEnabled = true;
+	}
}
else
{
	this.Key = e.Key;
	this.ModifierKeys = Keyboard.Modifiers;
	this.BtnSet.IsEnabled = true;
}
```
