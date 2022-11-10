# mpv-clipboard

Provides generic but powerful low-level clipboard commands for users and script writers.

Requires `powershell` on Windows,`pbcopy`/`pbpaste` on MacOS, `xclip` on X11, and `wl-copy`/`wl-paste` on Wayland.  

## Script Messages

### set-clipboard

`script-message set-clipboard <text>`

A script message that can be set in `input.conf` to copy the given text into the clipboard.
Can be combined with [property expansion](https://mpv.io/manual/master/#property-expansion)
to copy any mpv property.

For example to copy the path of the current file with Ctrl+c:

`Ctrl+c script-message set-clipboard ${path}`

### get-clipboard

`script-message get-clipboard <response-string>`

Sends the contents of the clipboard to the given script-message. This can be used by other
scripts to request the contents of the clipboard. The other script should register a script-message
with the same name as the `response-string` to receive the clipboard contents.

Additional arguments may be introduced in the future.

### clipboard-command

`script-message clipboard-command <command> <arg1> <arg2> ...`

Allows one to use the contents of the clipboard in a command. If the command
or arg strings contain the substring `%clip%` then it will be substituted for the
contents of the clipboard.

For example to tell mpv to play the contents of the clipboard:

    Ctrl+v script-message clipboard-command loadfile %clip%

The `%` character will act as an escape character;
`%%clip%` will evaluate to `%clip%`. Any `%%` will be substituted for `%`, so you may need
to use additional `%` characters if you actually want multiple in a row.

By default these commands behave asynchronously, meaning if you send multiple commands at the same time there
no guarantee that the first will be run before the second:

    Ctrl+t script-message clipboard-command print-text 1 ; script-message clipboard-command print-text 2

To handle this you can set the first [input command prefix](https://mpv.io/manual/master/#input-command-prefixes)
to `sync`, that will ensure that the commands are run in order. Note that all of the usual
[sync vs async rules](https://mpv.io/manual/master/#synchronous-vs-asynchronous) still apply. This behaviour may be moved to a separate script-message in te future.

## Credits

The code to get text from the clipboard comes from mpv's [console.lua](https://github.com/mpv-player/mpv/blob/master/player/lua/console.lua).

The code to set the clipboard text on Unix systems was based on [mpv-copyTime](https://github.com/Arieleg/mpv-copyTime).
