<img src="https://raw.githubusercontent.com/alejandroliu/pmimporter/master/ImportMap-icon.png" style="width:64px;height:64px" width="64" height="64"/>

# pmimporter

* Summary: Import world maps into PocketMine-MP
* WebSite: [github](https://github.com/alejandroliu/pmimporter)

## Overview

* nbtdump - Dump the contents of NBT files
* level - manipulate some level.dat settings
* check - read world maps and analyze the block, object composition
* convert - main conversion tool

## Description

A collection of tools used for importing world maps for use with
PocketMine-MP and Minecraft PE.

It supports the following formats:

- McRegion: Minecraft PC Edition pre1.2
- Anvil: Minecraft PC Edition v1.2 and better
- PMF: PocketMine v1.3.  **READ-ONLY** format.
- mcpe0.2.0: Minecraft PE finite worlds.   **READ-ONLY** format.

**TODO:**
- LevelDB: Minecraft PE v0.9.0 (or better) infinite worlds.









* * *

When importing Minecraft PC Edition world maps (Anvil and McRegion formats) it
will analyze the used blocks to make sure that only blocks supported
by Minecraft PE are generated.  It does this by either mapping these
blocks or removing them.  This conversion/fitering can be tweaked with
an user provided `rules` file.

**TODO**
Similarly, Tiles and Entities that are not supported by Minecraft PE
are eliminated.

This is done because using these unsupported features on a Minecraft
PE client would cause the game to crash.

## Command Usage

In general, the command usage is:

* _path-to-php-executable_ _path-to-pmimporter.phar_ _sub-command_ [options]

### Sub-commands

#### CONVERT

	convert [-c rules.txt ] [-t count] [-f format] srcpath dstpath

Converts maps.

* `-c rules` : Specify a rules conversion file.
* `-t count` : Specifies the number of threads to run.
* `-f format` : Specifies the output format.  Defaults to `mcregion`.
* `-o offset` : Specifies the y-offset for the import.  A value for
  the number of blocks you want to move the world *down*.  A negative
  value will move the world *up*.  Be careful when moving the world
  *down* that you do not remove the ground.
* `srcpath` : Directory path to the source world.
* `dstpath` : Directory path to create the new world.

Also, `convert` allows you to specify special settings to tweak the
format reader/writer code.  These settings are specifc to each
`format`. To configure you must pass the option:

* `--in.setting=value`  
  for the input format settings.
* `--out.setting-value`  
  for the output format settings.

#### PMCHECK

	check worldpath [--all|[rX,rZ[:cX,cZ[+cX,cZ]]] ...]

Analyze the number of chunks, blocks, etc in a world map.

* `worldpath` : Directory path to world to analyze.
* `--all` : If specified it will analyze all regions.
* `rX,rZ` : Region X,Z coordinates.
* `:cX,cZ` : Specify individual chunks (in Chunk offsets, from 0 to
  31) to analyze.
* `+cX,cZ` : Additional chunks to add to totals.

#### LEVEL

	level worldpath [attr=value]

Displays and modifies certain level attributes.

* `worldpath` : Directory path to world to display/modify.
* `attr=value` : Modify the `attr` setting to `value`.

The following attributes are supported:

* `spawn=x,y,z` : World spawn point.
* `name=text` : Level name.
* `seed=integer` : Random Seed.
* `generator=name[,version]` : Terrain generator.  PocketMine by
  default only supports `flat` and `normal`.
* `preset=preset` : Alias for `generatorOptions`.
* `generatorOptions=preset` : Terrain generator `preset` string.
  Ignored by the `normal` generator.  Used by `flat`.

#### NBTDUMP

	nbtdump nbt_file

Dumps the contents of an `NBT` formatted file.


## Installation

Requirements:

* This software has only been tested on Linux
* PHP v5.6.0, version used by PocketMine-MP v1.4.1.  This one contains
  all dependancies.
* PHP CLI API

Download `pmimporter.phar` and use.  It does *not* need to be
installed.


## Configure translation


You can configure the translation by providing a `rules` file and
passing it to `pmcovert` with the `-c` option. The format of `rules.txt`
is as follows:

* comments, start with `;` or `#`.
* `BLOCKS` - indicates the start of a blocks translation rules section.
* `source-block = target-block` is a translation rule.  Any block of
  type `source-block` is converted to `target-block`.

There is a default set of conversion rules, but you can tweak it by
using `rules`.


## FAQ


* Q: Why it takes so long?
* A: Because my programming skills suck.  I usally start a conversion
  and go to bed.  When I wake up in the morning the map is ready to
  play.
* Q: Does it support LevelDB files (Pocket Edition v0.9.0 infinite
  worlds)?
  A: No, it does *NOT*.  You need to convert those to _Anvil_ format
  first using something like
  [LevelDb2Avnil](https://github.com/ljyloo/LevelDb2Avnil).
* Q: Why tall builds seem to be chopped off at te top?
* A: That is a limitation of Pocket Edition.  It only supports chunks
  that are up to 128 blocks high, while the PC edition Anvil worlds
  can support up to 256 blocks high.  You can shift worlds down by
  using the `-o` option.  So if you use `-o 40` that will move the
  build down 40 blocks.  *BE CAREFUL NOT TO REMOVE THE GROUND*
* Q: Why my Anvil format file is not recognized?
* A: That happens with Anvil files that were converted from an
  McRegion file.  These files contain both `.mcr` and .`mca` files.
  These confuses the File format recognizer.  You need to delete the
  `.mcr` files so the world is recognized as in Anvil format.
* Q: Why I experience glitches when I enter a new world?
* A: This is a Minecraft Pocket Edition limitation.  This is made
  worse by spawning into a very large chunk (usually very detailed
  builds). My recommendation is to change the spawn point to a very
  flat (boring) area.  Sometimes exting and re-entering the game
  helps.
* Q: Why I see some blocks that are not in the original map?
* A: These have to do with how the translation happens.  There are
  blocks that are not supported by Minecraft Pocket Edition.  These
  need to be map to a block supported by MCPE.  You can tweak this by
  modifying the conversion rules.
* Q: Why do converted maps overload my server?
* A: Detailed maps need to be uncompressed by the server.  These take
  an additional load on the server.

## References

* [PocketMine-MP](http://www.pocketmine.net/)
* [Block defintions](https://raw.githubusercontent.com/alejandroliu/pmimporter/master/classlib/pmimporter/blocks.txt)
* [Minecraft PC data values](http://minecraft.gamepedia.com/Data_values)
* [Minecraft PE data values](http://minecraft.gamepedia.com/Data_values_%28Pocket_Edition%29)

## Issues and Bugs


* Performance is quite poor.  It takes me 5 minutes to process a small
  map on Linux.  Large maps can easily take days.
* The only target format implemented is McRegion.
* Anvil maps are silently truncated to be less than 128 blocks high.  
  The PocketMine-MP core API only support Y dimensions for 0 to 127.
* PMF v1.3 maps do not provide valid Entity data so it is ignored.
* Entity data is a bit dodgy:
  * Dropped items are currently *not* copied.  They sometimes crash the
    client.
  * PocketMine itself has incomplete Entity support, so things are
    copied but are usually ignored by PocketMine.
* Conversion table could be better.  I am open to suggestions.

# Todo

* Finish LevelDB
  - reading
	- writing
* Copy Entities
  - reading: anvil, mcregion, mcpe020, leveldb
	- writing: anvil, mcregion, level
* Check Entities
* Copy Tiles
	- reading: anvil, mcregion, mcpe020, pmf, leveldb
	- writing: anvil, mcregion, leveld
* Check Tiles
* Re-write documentation
* Testing
  - conversion
	- features

| reading\writing | anvil | mcregion | LevelDB |
|-----------------|-------|----------|---------|
| mcregion        |       |          |         |
| anvil           |       |          |         |
| LevelDB         |       |          |         |
| McPe020         |       |          |         |
| PMF1.3          |       |          |         |

## Changes

* 2.0:
  - removed commands: entities, dumpchunk
  - syntax of sub-commands changed
  - PocketMine-MP plugin has been discontinued
  - Major speed improvements
  - Merge maps at a chunk level.
* 1.5upd2: Update
  - Added new blocks since 0.10
* 1.5upd1: Bugfix
  * Minor fix in plugin code
* 1.5: Bugfix
  * BugFixes in MCPE0.2.0 format.
  * BugFixes with region offsests on negative values
  * Tweaked builds and subcommand names
* 1.4: Maintenance release
  * pmentities fix typos
  * minor text info tweaks
  * Added pmentity to dump entity data
  * Added region settings to MCPE0.2.0 and PMF1.3 formats.
  * Fixed offset functionality.
  * Filter out Dropped Item entities.
* 1.3: OldSkool fixes
  * Added support for Tiles to PMF maps.
  * Added support for Tiles and Entities fo MCPE 0.2.0 maps.
  * Fixed HeightMap calculations in PMF and MCPE 0.2.0 formats
  * Added `settings` capability to tweak conversion.
  * Merged ImportMap and pmimporter into a single Phar file.
* 1.2: Fixes
  * pmcheck: show height map statistics.
  * pmconvert: offset y coordinates
* 1.1: OldSkool release
  * Added support for maps from Minecraft Pocket Edition 0.2.0 - 0.8.1
  * Added support for PMF maps from PocketMine v1.3.
* 1.0 : First release

## Copyright


Some of the code used in this program come from PocketMine-MP,
licensed under GPL.

    pmimporter  
    Copyright (C) 2015 Alejandro Liu  
    All Rights Reserved.

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 2 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
