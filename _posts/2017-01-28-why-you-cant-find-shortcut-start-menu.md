---
layout: post
title:  Why You Can't Find That Shortcut in the Windows 10 Start Menu
author: Michael Giuffrida
---

> Why some app shortcuts never appear in Windows 10 search results and how to fix it

Remember when dragging a program into the Start Menu would add a shortcut to the Start Menu? And the shortcut would
actually show up when you search for it?

In Windows 10, you can't drag a .exe (or a shortcut) into the Start Menu anymore. So if you've installed a program and
it didn't create an entry in Start, or if you've downloaded a standalone .exe you want to add to Start, the easiest way
would be to right-click your program and choose "Pin to Start". **Don't do that.**

Pinning a program to Start will add a tile to the right side of Start, but it will *never* show up as a search result
when you type the program's name. Typical workarounds won't work (like adding the shortcut manually in Explorer,
rebuilding your index, or removing and re-adding the shortcut). Yep, Pin to Start breaks Windows, and it's
[infuriating](https://superuser.com/questions/949886/how-to-add-new-item-to-the-windows-10-all-apps-section) and
[consternating](https://superuser.com/questions/954372/how-do-i-add-a-new-program-to-the-start-menu) users.

This is only a problem for an app that isn't *already* in the Start Menu. You can pin existing shortcuts without a
problem.

## Reset a program's entry in the Start Menu

If you just want to add a new program to Start, skip to the next section. But if you're here, chances are your Start
menu is already screwed up because you tried pinning something.

> Pinning a new app will add that tile to Start but won't make it appear in search results. Worse, Windows will forever
> remember what apps you've pinned. If you unpin the app and try to add a shortcut in the Start folder manually, Windows
> will happily re-pin that app for you, and Start will never show it in searches.

So first, we have to **trick Windows into forgetting our pinned entry by overwriting it**.

1. **Unpin** the app
2. **Rename** the app's directory, e.g. from `AwesomeApp` to `AwesomeApp2`
3. Create a **new shortcut** for the app's new location in your Start Menu directory:  
   `%AppData%\Microsoft\Windows\Start Menu\Programs`
4. Rename the app's directory **back to** its original name, e.g. `AwesomeApp`

The app should appear in Start searches now, as well as the program list. You can also safely pin the app now without
dropping it from search results.

## Add a program to Start, the right way

Here's a bug-free way to add a shortcut to Start that will show up in search results and the program list. If you've
already tried pinning the app, you need the steps in the previous section. Otherwise, it's easy:

1. Open your Start Menu directory in Explorer:  
   `%AppData%\Microsoft\Windows\Start Menu\Programs`
2. Open the app's directory in another Explorer window
3. Alt-drag the app into the Start Menu folder to create a shortcut

It's pretty simple; you just have to add the shortcut manually instead of using "Pin to Start". Again, once you've done
this, feel free to pin and unpin the app or rename the shortcut.

> You could add the shortcut to `%ProgramData%\Microsoft\Windows\Start Menu\Programs` instead if you prefer.
