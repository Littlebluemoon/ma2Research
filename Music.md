This is a documentation on Music.xml, maimai DX's way of organizing chart metadata.

# Before you start
- This document assumes that you have basic knowledge about XML, and how data within it is organized.
- Only the tags' meanings are described in this document.

# Overview
`Music.xml` is an XML file that is put on the same folder as the charts for a specific song. It contains essentially all the metadata for a given song, as well as its charts (but NOT their actual content).

There are a few things applied across all `Music.xml` files in general:
- They make heavy use of this array structure:
```xml
<id></id>
<str></str>
```
where `<id>` is the identifier for some given value `<str>`. If there is no given value for a tag, the structure `<str />` is used.
    - The term "array" from here onwards refer to this structure.
- Statistical information in the metadata is for reference purposes only. If you supply an incorrect value into said field(s), the charts will load normally without problems.
- The general location for these files are `[game data directory]/StreamingAssets/A000/music/musicXXXXXX` with `XXXXXX` being some ID value.

# Tags

## dataName
The tag `<dataName>` has a value equals to the file's containing folder.
## netOpenName
The tag `<netOpenName>` is an array:
- `<id>` refers to the date on which the song was added to the game, in the format `yymmdd`.
    - The only exceptions are pre-DX charts, where `<id>` equals 0.
- `<str>` refers to the update in which the song was added to the game.
    - The update's name will have this format: `Netyymmdd`, with `yymmdd` being the day of the update.
	- The only exceptions are pre-DX charts, where `<str>` equals `Net190711`.
## releaseTagName
The tag `<releaseTagName>` is an array:
- `<id>` refers to the version's minor number, with some formatting:
    - Pre-DX versions are considered 1.00.00;
	- Post-DX versions are kept unchanged.
	- `<id>` will have a value equal to the last four digits of the version number, with the last one changed into a 1. Leading zeroes are omitted.
- `<str>` has a value equals to the version's formal name. The first two rules above still apply.
    - It is formatted as `VerX.XX.XX` with `X.XX.XX` being the version number.
## disable
`<disable>` receives an integer boolean value, deciding on whether to show this song in song select or not.
- If true (1), the song will not appear in song select, and vice versa.
## name
`<name>` is an array that refers to a chart's ID (and consequently its chart type), and its name.
- `<id>` is the song's ID. Equals to `XXXXXX` (the number on its parent folder), omitting any leading zeroes. The last four digits determine the song's ID, and the first two determines its chart type:
    - 0: Standard chart
	- 1: DX chart
	- 1X, X=(0-9): (X+1)th Utage chart
- `<str>` is the song's name in its original form.
## rightsInfoName
An array showing the song's copyright information, displayed under the song's jacket in song select.
- `<id>` is the song's copyright text ID. Set to 0 by modern charters.
- `<str>` is the copyright text's content.
    - This field must be set to `<str />` (empty value) to prevent the copyright text box from appearing.
## sortName
`<sortName>` has a value equal to the song's name, in a form that allows the game to easily sort the songs using their name.
- Spaces and special symbols are removed
- Alphanumeric characters are converted to their uppercase counterparts
- Japanese characters (hiragana/kanji) are converted to kana, without dakutens and handakutens. For example, ガ (ga) would become カ (ka).
## artistName
An array showing the song's artist.
- `<id>` is the song artist's ID. Set to 0 by modern charters.
- `<str>` is the artist's name in its original form.
## genreName
An array showing the song's genre in-game.
- `<id>` is the genre's ID as shown in `[game data directory/musicGenre]`.
    - Warning: Filling in a non-existent ID while the sorting option is set to Genre will crash the game
	- Warning: Some values in that directory is not available for use
- `<str>` is the corresponding genre as shown in `[game data directory/musicGenre]`.
	- Warning: Filling in a non-matching genre name while the sorting option is set to Genre will crash the game
## bpm
`<bpm>` is a single integer showing the song's BPM.

This can be the song's main BPM, or starting BPM.
## version
`<version>` is a single number, denoting the version number in which the song was added, formatted.
- The first digit is 1 if the version was pre-DX, otherwise 2.
- The next three digits is the minor version number, padded with a 0 afterwards.
- The last one is the version's letter, starting from 0 as the letter counts from A.
## AddVersion
An array denotes the version that the song was added.
- `<id>` is the version's ID, starting from 0 as the version name progress from maimai (original).
    - Warning: Supplying a non-existent version ID while the sorting option is set to Version will crash the game
- `<str>` is the version's name, without the word "maimai" starting from "maimai GreeN".
    - Warning: Supplying a non-corresponding version name while the sorting option is set to Version will crash the game
## movieName, cueName
Both these tags is an array denoting the song's ID (and thus the corresponding movie and cue file to play in the game), and the song's name.
- `<id>` is the song's ID. Equals to four last digits of `XXXXXX` (the number on its parent folder), omitting any leading zeroes.
- `<str>` is the song's name in its original form.
## dresscode
Unknown effect. Currently, always set to `false`.
## eventName, eventName2, subEventName
All these tags determines the event in which the song is released, but each one's effects was less clear. Seemingly, one of them can lock out the song until the player progressed through said events.
Each of these receives an array as its value:
- `<id>` is the event's ID, as listed in `[game data directory/event]`.
    - Two special cases are 0 and 1, seemingly assigned to songs which doesn't belong to any given events, or those *used to* be in an event that is inaccessible now.
- `<str>` is the corresponding event's name.

## lockType
`<lockType>` is an integer boolean that determines a song's unlock status through stamp cards.
- If set to 1, then it determines that the song is locked behind a stamp card, and vice versa.
    - Note that a song must first be marked as a login bonus for this to take effect.
## subLockType
`<lockType>` is an integer boolean that determines a song's Re:Master chart availability.
- If set to 1, then the song's Re:Master chart will not appear at all, and vice versa.

## dotNetListView
Always set to `true`.

## notesData
This section describe the charts' metadata. There are six `Notes` sub-tags there, corresponding to the six (formerly, now five) charts for a chart ID.
## Notes
Each of the `<Notes>` tag describe the charts' metadata through a multitude of sub-tags.

Counting from the first `<Notes>` tag, this defines metadata for a chart's difficulty in this order: Basic, Advanced, Expert, Master, Re:Master, and (formerly) Utage.
### file
The chart's filename. Equals to `[chartID]_[0X].ma2`, X=(0-5).
- X corresponds to difficulty levels in-game:
    - 0: Basic
	- 1: Advanced
	- 2: Expert
	- 3: Master
	- 4: Re:Master
	- 5: Formerly Utage, now has its own section, detailed information in another document
### level
The chart level's whole part.

Note that this value could be set as high as one wants, even though the game doesn't have a formal definition for levels higher than 15.
### levelDecimal
A single digit denoting the chart level's decimal part, hidden from the player, and used for rating calculations as well as difficulty estimation within a tier of difficulty.

`<level>` and `<levelDecimal>` together forms a *chart constant*. For example, a level of 9 and levelDecimal of 8 forms a chart constant of 9.8. Decimals of .7 and up is classified as +.
- **[NEW]** Starting from BUDDiES PLUS, .6 or up are classified as +. I don't know why, but now all three main SEGA arcade rhythm games has different ways to determine if a level is + or not.

Enter anything larger than 9 will probably crash the game.

### notesDesigner
An array showing the charter.
- `<id>` is the charter's ID. Set to 0 by modern charters.
- `<str>` is the charter's name. If filled as an empty value, it appears as - in song select.

### notesType
Unknown effect, but set to 0.

### musicLevelID
The chart's level tier, converted into an ID, starting from 0 as the level counts from 1, 2,... 7, 7+,... 15+.
### maxNotes
The chart's note count. This value is purely statistical.
### isEnable
Allow a chart to be playable.
- If set to false, the chart will be greyed out in song select, its level displayed as --, and cannot be selected.

## utageKanjiName
Utage specific function. A single kanji letter that will be displayed in song select, as well as a brief notion of what to expect in the chart.

## comment
Utage specific function. A short sentence describing the chart's theme, running jokes included.
- Fun fact: This is set to **LET'S PARTY!!!** for all charts in International release. Sucks a butt ton.
    - **Update**: Utage charts in international version now regained their corresponding comment(s).

## utagePlayStyle
Utage specific function. An integer that determines if an Utage chart is only playable in 2-Player Mode.
- If set to 1, the chart will only be playable in 2-Player Mode, and locked out in 1-Player Mode.
    - As such, the maximum achievable score will be doubled from 101.0000% to 202.0000%.
- If set to 0, the chart is open for play regardless of the number of players.

## fixedOptions
Consists of four `<fixedOption>` sub-tags, each describing a modifier forcefully applied to an Utage chart.

## fixedOption
Each of these is an array, denoting the option type, and option value.
- `<_fixedOptionName>` determines the option type;
- and `<_fixedOptionValue>` determines the option value.
