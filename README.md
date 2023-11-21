This is a documentation on the chart files used in the game from maimai DX FESTiVAL onwards.

# Before you start
- The information in this documentation was obtained from assumptions and testing. No reverse-engineering of binary files took place in that process.
- Chart files are tab-separated value files.
- Numberings in the chart files start from 0.
- A chart is broken down into measures, each of which constitutes four beats.
- Each measure is also broken down into a number of ticks, which serves as a unit of time when charting.
- The chart engine automatically skips meaningless or erroneous definitions.
    - However, if for example, a note was defined with more arguments than required, the chart engine will still take the first few arguments for processing, and render the note normally if these arguments are correct. If an argument, for example, the note position, is given an impossible value, the game will crash.
- Charts are reloaded each time a song is played. This means that if you are testing a custom chart, you can make whatever changes you need, then these changes will be reflected on your next play. You can also use the "Track Skip (Buttons)" function to perform more tests rapidly.

# Headers
This part contains general data for the chart.
## VERSION
This line is always defined as follows:
```
VERSION	0.00.00	1.04.00
```
1.04.00 is the current version of the charting engine.
## FES_MODE
Defines if a chart is a Utage chart or not.
Currently, it is unknown how maimai DX handles special modifiers for Utage charts (e.g. extreme scroll speed/invisible notes in *全世界共通リズム感テスト*).
```
FES_MODE	[0/1]
```
## BPM_DEF
Defines four main BPM values in the chart, in this order: Starting BPM, Mode BPM (the BPM that the chart spends most time in), Max BPM, and Min BPM.
```
BPM_DEF	257.000	290.000	290.000	257.000
```
It's also worth noting that BPM values use three decimal digits, and follow how computer understands tempo. To get the BPM value to fill in:
- First, use 60000000 to divide by the BPM value you intend to use.
- Round that number down to the unit digit, then use 60000000 to divide by that number.
- Round the number you just obtained down to the third decimal to get the value to fill in.

This change is more noticeable in higher BPM values. For example, to define 275.000 BPM, you would have to fill in 275.001.
## MET_DEF
Defines the main time signature of the song.
Since maimai has no real way to visually indicate a measure, this value is purely cosmetic.
Also, note that this value is written in reverse: The lower part goes first, then the upper part.
```
MET_DEF	4	4
```
## RESOLUTION
Defines the number of ticks in a measure. A higher number means you can place notes with more exact timings.
Defaults to 384. Generally, a multiple of 3 and some power of 2 is recommended. If you like to work with whole numbers, then also make it a multiple of 5.
```
RESOLUTION	384
```
## CLK_DEF
Affects the number of ticks played at the beginning of a song. `CLK_DEF / (RESOLUTION / 4)` ticks will play following the starting BPM of the chart, and this often corresponds to the starting time signature.
It is possible to have these ticks played at a different BPM than intended, by setting a different starting BPM, then setting the next BPM definition to the actual BPM once the ticks end. (Examples include *Last Samurai*).
```
CLK_DEF	384
```
## COMPATIBLE_CODE
Always set to the following:
```
COMPATIBLE_CODE	MA2
```
# BPM
This part defines the BPM changes in the chart.
Structure:
```
BPM [measure]   [tick]  [BPM]
```
`[measure]` and `[tick]` denotes the timing of the BPM change, and `[BPM]` its value.
This part should always start with a BPM definition at measure 0 and tick 0.
# MET
This part defines the time signature changes in the chart.
Structure:
```
MET [measure]   [tick]  [lower] [upper]
```
`[measure]` and `[tick]` denotes the timing of the BPM change, and `[lower]` and `[upper]` respectively, its lower and upper part.
Again, as there are no real ways to visually indicate a measure in maimai, and each (in-chart) measure will always constitute four beats no matter what time signature it is in, these values are purely cosmetic. However, it can help charters understand the rhythm of a song.
This part should always start with a BPM definition at measure 0 and tick 0.
# Notes
ma2 1.04.00 uses almost all note definitions used in ma2 1.03.00, with some new additions due to the new features from maimai DX FESTiVAL.
## Basic
To define a note, you have to state various arguments for it.
Consistent across all note types:
- Its timing, in terms of measure and tick
- Its starting position
- Its modifier (except for Touch/Touch Hold, which has no modifiers)

Specific to Holds:
- Its duration

Specific to Slides:
- Its starting and ending position
- Its delay
- Its duration
- Its shape

## Modifiers
A note can have one of these modifiers, prefixed right before its name:
- `NM`: Normal note
- `EX`: EX note, giving the player a Critical Perfect as long as it is hit
- `BR`: Break note, changing its color to orange and awards five times as much points
- `BX`: Break EX note, essentially a free score boost
- `CN`: Exclusive to slides, denoting additional slide segments. More in Slides.

For example, to make a normal Tap, the correct name will be `NMTAP`. For a Break Hold, `BRHLD`, and so on.
## Tap
Structure:
```
[mod]TAP    [measure]   [tick]  [position]
```
`[position]` refers to the buttons on the machine. Let the button on 1-o'clock button 0, then count from that button clockwise.
```
NMTAP   2   0   4  // Normal tap at button 4
EXTAP   2   192 6  // EX tap at button 6
BRTAP   3   0   8  // WARNING: There is no button 8, so this will crash the game
```
## Hold
Structure:
```
[mod]HLD    [measure]   [tick]  [position]  [duration]
```
`[duration]` refers to the amount of time the button must be held, in ticks. The value can be set to 0, in which case it behaves like a Tap note, but with double scoring.
- Setting the duration to a very small integer, for example, `1`, also achieves the same result.
```
NMHLD   3   0   4   192  // Normal hold at button 4 with duration of two beats
BRHLD   3   192 4   0    // Pseudo-tap-break at button 4
```
## Slides
### Basic
To make a slide, first denote a Star note. It signifies the appearance of a Slide following it, and is considered a Tap.
```
NMSTR   4   0   3  // Normal star at button 3
```
Then, define a slide following this structure:
```
[mod][shape]    [measure]   [tick]  [startPos]  [delay] [duration]  [endPos]
```
- `[mod]` can be `NM` for a normal slide, or `BR` for a break slide
- `[measure]` and `[tick]` should be equal to that of the star note
- `[startPos]` and `[endPos]` define the starting and ending position of the slide
- `[delay]` states the amount of time that the slide-star fully forms before moving. Most of the slides delay for one beat.
- `[duration]` states the duration of the slide
	- Unchecked: Can a slide has a duration of `0`?
- `[shape]` is one of the following

| Symbol | Shape  | startPos & endPos notes  | Corrseponding simai shape |
|---|---|---|---|
| SI_  | A line connecting two buttons on the screen  | Distance between startPos and endPos must be greater than 1  | -|
| SV_  | A line goes from the starting button to the center of the screen, then to the ending button  | Distance between startPos and endPos cannot be 0  | v |
| SCR  | An arc going along the judgment ring, clockwise  | None  | < or > depending on position|
| SCL  | An arc going along the judgment ring, counter-clockwise  | None  | < or > depending on position|
| SUR  | An arc going along the center of the screen, clockwise  | None  | q |
| SUL  | An arc going along the center of the screen, counterclockwise  | None  | p |
| SXR  | An arc going along the side of the screen, clockwise  | None  | qq |
| SXL  | An arc going along the side of the screen, counterclockwise  | None  | pp |
| SLR  | A line connecting the starting button and the button two spaces away, clockwise, then the ending button  | endPos must be greater than startPos + 2  | V |
| SLL  | A line connecting the starting button and the button two spaces away, counterclockwise, then the ending button  | endPos must be greater than startPos - 2  | V |
| SSL  | Zigzag slide to the left  | Distance between startPos and endPos must be 4  | s |
| SSR  | Zigzag slide to the right  | Distance between startPos and endPos must be 4  | z |
| SF_  | Fan-like shape connecting two opposing buttons | Distance between startPos and endPos must be 4  | w |

For example, to make a straight-line slide from button 2 to button 5 with duration of half a beat:
```
NMSTR   5   0   2               // Star note
NMSI_   5   0   2   96  48  5   // Slide definition
```
Note that you can define a Star note without defining a Slide afterward. Likewise, you can define a Slide without a Star before that. Both of these enable Utage functions.
### Define multiple slides from a single Star note
Sometimes, there can be two or more slides going from a Star note. To achieve this, simply define multiple slides with the same timing as the Star note.
```
NMSTR   5   0   2               // Star note
NMSI_   5   0   2   96  48  5   // First slide
NMSI_   5   0   2   96  48  7   // Second slide
```
If multiple slides are going from a star, the star will turn into a 10-sided star, and the slides will turn yellow.
### Connecting multiple segments of a slide
New to maimai DX FESTiVAL is the ability to connect multiple slide segments into one. They are defined with the `CN` modifier.
There are many things to note about this type of slide:
- They inherit the modifier of the very first slide segment,
- Their timing is the sum of the first slide segment, plus all delay and duration values,
- Their delay is always 0,
- Their startPos should be equal to the previous slide segment's endPos,
- and their star's moving speed is directly proportional to the total duration of the whole slide, not the duration of individual segments within it.

```
NMSTR   5   0   2               // Star note
NMSI_   5   0   2   96  48  5   // Line connecting button 2 to 5
CNSUR   5   144 5   0   48  0   // Arc connecting button 5 to 0, clockwise
```

## Touch
Structure:
```
NMTTP   [measure]   [tick]  [numPos]    [chrPos]    [hanabi]    [size]
```
- `[numPos]` is any integer in the range 0-7.
- `[chrPos]` is any one of `[A, B, C, D, E, F.]`
    - When combined, `[chrPos]` and `[numPos]` form the name of a touch sensor on the screen.
    - Note that while there are technically two C touch sensors, C1 and C2, only C1 (C0 in-chart) is used when charting.
- `[hanabi]` triggers a firework effect if set to `1`.
- `[size]` defines the size of the note.
    - `M1` returns a regular-sized note.
    - `L1` results in a bigger note used in Basic/Advanced charts.
```
NMTHO   10  0   4   D   0   M1      // Touch at sensor D4
```
## Touch Hold
```
NMTTP   [measure]   [tick]  [duration]  0    C    [hanabi]    [size]
```
`[numPos]` and `[chrPos]` is replaced by `0` and `C` respectively, since that is the only combination for the position of a Touch Hold.
# Note ordering
Just like ma2 1.03.00, notes are sorted in order of timing. Within each timing group, the order of notes doesn't matter: so long as the expressions are well-formed, the note will be generated in the correct order.
# Footer
This part contains overall stats for the chart. This is purely for reference purposes since incorrect values here will still allow the chart to run.
## T_REC
Records the amount of specific note types, broken down into note types and modifiers.
| Message | Meaning |
|---|---|
| T_REC_TAP | Amount of Normal Tap Notes  |
| T_REC_BRK | Amount of Break Tap Notes  |
| T_REC_XTP | Amount of EX Tap Notes |
| T_REC_BXX | Amount of EX Break Tap Notes |
| T_REC_HLD | Amount of Normal Hold Notes |
| T_REC_XHO | Amount of EX Hold Notes |
| T_REC_BHO | Amount of Break Hold Notes |
| T_REC_BXH | Amount of EX Break Hold Notes |
| T_REC_STR | Amount of Normal Star Notes |
| T_REC_BST | Amount of Break Star Notes |
| T_REC_XST | Amount of EX Star Notes |
| T_REC_XBS | Amount of EX Break Star Notes |
| T_REC_TTP | Amount of Touch Notes |
| T_REC_THO | Amount of Touch Hold Notes |
| T_REC_SLD | Amount of Normal Slide Notes |
| T_REC_BSL | Amount of Break Slide Notes |
| T_REC_ALL | The total note count of the chart. This should also be equal to the sum of all the values above. |
## T_NUM
Records the number of note types, broken down into four main categories as shown in the result screen.
- Note that the note types here is categorized using maimai FiNALE gameplay.

| Message | Meaning |
|---|---|
| T_NUM_TAP | Amount of Tap and Touch notes (excluding Breaks) |
| T_NUM_BRK | Amount of Break Tap, Break Hold and Break Slide notes |
| T_NUM_HLD | Amount of Hold and Touch Hold notes (excluding Breaks) |
| T_NUM_SLD | Amount of Slide notes (excluding Breaks) |
| T_NUM_ALL | The total note count of the chart. This should also be equal to the sum of all the values above. |
## T_JUDGE
Records the amount of judge markers for various note types.
| Message | Meaning |
|---|---|
| T_JUDGE_TAP | The amount of notes judged as a Tap. Equals to the amount of Tap and Touch notes (including Break Tap Notes). |
| T_JUDGE_HLD | The total amount of hold ticks in the chart. Each `RESOLUTION / 8` of hold duration constitutes a hold tick. Take each Hold and Touch Hold (including Breaks)'s duration and divide by `RESOLUTION / 8`, rounded up, then add up all the results to get this value. Note that if a hold has a duration of `0`, it is considered to have 1 hold tick.|
| T_JUDGE_SLD | The amount of notes judged as a Slide. Equals to the number of Slide notes (including Breaks). |
| T_JUDGE_ALL | Equals to the sum of all values above. |
## TTM
This part contains miscellaneous information about scoring.
| Message | Meaning |
|---|---|
| TTM_EACHPARIS | The number of note groups being hit at the same time (i.e. yellow notes). |
| TTM_SCR_TAP | Total amount of score given by hitting all Tap and Touch notes perfectly (excluding Breaks). Each Tap/Touch awards 500 points, so this is equal to `T_NUM_TAP * 500`. |
| TTM_SCR_BRK | Total amount of score given by hitting all Breaks critically perfect. Critically perfecting a Break gives 2600 points, so this is equal to `T_NUM_BRK * 2600`. |
| TTM_SCR_HLD | Total amount of score given by hitting all Hold and Touch Hold notes perfectly (excluding Breaks). Each Hold/Touch Hold awards 1000 points, so this is equal to `T_NUM_HLD * 1000`. |
| TTM_SCR_SLD | Total amount of score given by hitting all Slide notes perfectly (excluding Breaks). Each Slide awards 1500 points, so this is equal to `T_NUM_SLD * 1500`. |
| TTM_SCR_ALL | Sum of `TTM_SCR_TAP`, `TTM_SCR_BRK`, `TTM_SCR_SLD` and `TTM_SCR_HLD`. This is the score required to get SSS+ rank. |
| TTM_SCR_S | Minimum score required to get S rank (97%). Equals to `(TTM_SCR_ALL - 100 * T_NUM_BRK) * 0.97`, rounded up to the nearest multiple of 50. |
| TTM_SCR_S | Minimum score required to get SS rank (99%). Equals to `(TTM_SCR_ALL - 100 * T_NUM_BRK) * 0.99`, rounded up to the nearest multiple of 50. |
| TTM_RAT_ACV | The FiNALE rating required to get SSS+ rank (hitting all notes perfectly, and all Breaks critically perfect). Equals to `TTM_SCR_ALL / (TTM_SCR_ALL - 100 * T_NUM_BRK) * 10000`, rounded down. |
