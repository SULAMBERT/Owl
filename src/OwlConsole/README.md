# OwlConsole

## What is OwlConsole?
OwlConsole is a terminal application that allows users to browse message boards through the command line. OwlConsole uses the same backend as Owl and therefore supports the same variety of message board software.

## Reference

## `login`

#### Synopsis

`login <url> [<username>] [<password>] [--parser=<parser-name>]'

#### Description

Logs the user into a message board. Any existing connections are immediately terminated even if the call to `login` ultimately fails. 

#### Options

`<url>` - The URL of the message board. If the protocol is ommitted then `http` will be tried first and then `https`. 

`<username>` (optional) - If no username is passed in, the user will be prompted. If the username contains spaces then this option should be omitted and the name should be entered in the prompt, otherwise the username will be invalid.

`<password>` (optional) - If no password is specified, then the user will be prompted. 

`--parser=<parser-name>` - If no `<parser-name>` is specified, Owl will attempt each of the currently loaded parsers and will use the first one that works. If the `<parser-name>` is provided but fails to connect then Owl will not attempt any other parsers.

## `lf`

#### Synopsis

`lf`

#### Description 

List the subforums of the current forum.

## `lt`

#### Synopsis

`lt [<page-number>] [<perpage>] [--showids]`

#### Description
List the threads in the current forum.

#### Options
`<page-number>` - The page number of threads to list. This is calcuated from the `<perpage>` option. The default is 1.

`<perpage>` - The number of threads to list. This number is used to calcuate the starting post in cunjunction with the `<page-number>` option. The default value is 10.

`--showids` - By defaul the threads IDs are not shown in the thread list. This option will show them.

## `lp`

#### Synopsis
`lp [<page-number>] [<perpage>] [--showids]`

#### Description
#### Options