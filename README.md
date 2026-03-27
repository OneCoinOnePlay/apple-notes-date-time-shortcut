# Apple Notes Date and Time Shortcuts

This documents a working way to add keyboard shortcuts for Apple Notes that append either the current date or the current time to the selected note.

Quit and reopen Notes once, then test the shortcuts.

## What It Does

- App: Apple Notes on macOS
- Shortcut: `Cmd+Shift+D`
- Service name: `Insert Date`
- Behavior: appends the current date to the selected note

- Shortcut: `Cmd+Shift+S`
- Service name: `Insert Time`
- Scope: Notes only
- Behavior: appends the current time in 12-hour format to the selected note

`Cmd+Shift+T` cannot be used for the time shortcut because it is already bound in Apple Notes.

## Where the Shortcut Appears in macOS

After the workflow is installed, the shortcut can appear in:

`System Settings > Keyboard > Keyboard Shortcuts > Services > General > Insert Date`

`System Settings > Keyboard > Keyboard Shortcuts > Services > General > Insert Time`

Depending on macOS version, the grouping may vary slightly. If the workflow is accidentally configured to accept text input, it may appear under `Text` instead of `General`, and the keyboard shortcut can just beep in Notes when no text input is available.

## Important Limitation

This does **not** insert at the text cursor position.

Apple Notes does not expose reliable cursor-position editing through AppleScript for this use case. The working solution updates the selected note body directly instead of typing at the insertion point.

## Workflow Location

The date workflow bundle lives at:

`~/Library/Services/Insert Date Time.workflow`

The main date workflow file is:

`~/Library/Services/Insert Date Time.workflow/Contents/Resources/document.wflow`

The time workflow bundle lives at:

`~/Library/Services/Insert Time.workflow`

The main time workflow file is:

`~/Library/Services/Insert Time.workflow/Contents/Resources/document.wflow`

## Important Workflow Setting

This Quick Action must be configured to receive **no input** in **Notes**.

If it is configured to receive text, macOS can refuse to run it from the keyboard shortcut and only play the alert sound.

The workflow metadata should effectively correspond to:

- Workflow receives: `no input`
- In: `Notes`

## AppleScript Used

The date workflow uses this script:

```applescript
on run {input, parameters}
	set ts to do shell script "date '+%A, %d %B %Y'"
	set noteID to do shell script "osascript -e 'tell application \"Notes\" to get selection' | perl -ne 'print $1 if /^note id (.+)$/'"
	if noteID is "" then return input
	tell application "Notes"
		set n to note id noteID
		set body of n to (body of n) & "<div>" & ts & "</div>"
	end tell
	return input
end run
```

The time workflow uses this script:

```applescript
on run {input, parameters}
	set ts to do shell script "date '+%I:%M %p'"
	set noteID to do shell script "osascript -e 'tell application \"Notes\" to get selection' | perl -ne 'print $1 if /^note id (.+)$/'"
	if noteID is "" then return input
	tell application "Notes"
		set n to note id noteID
		set body of n to (body of n) & "<div>" & ts & "</div>"
	end tell
	return input
end run
```

## Current Formats

Date:

`Friday, 27 March 2026`

It comes from:

```sh
date '+%A, %d %B %Y'
```

Time:

`06:52 PM`

It comes from:

```sh
date '+%I:%M %p'
```

If you want a different format, edit the relevant `set ts` line in the AppleScript:

```applescript
set ts to do shell script "date '+%A, %d %B %Y'"
```

For example:

```applescript
set ts to do shell script "date '+%Y-%m-%d'"
```
