# Rhubarb Lip-Sync

[Rhubarb Lip-Sync](https://github.com/DanielSWolf/rhubarb-lip-sync) is a command-line tool that automatically creates mouth animation from voice recordings. You can use it for characters in computer games, in animated cartoons, or in any other project that requires animating mouths based on existing recordings.

Rhubarb Lip-Sync produces output files in various text formats (TSV/XML/JSON). If you're a programmer, this makes it easy for you to use the output in whatever way you like. If you're not a programmer, there is currently no direct way to import the result into your favorite animation tool. If this is what you need, feel free to [create an issue](https://github.com/DanielSWolf/rhubarb-lip-sync/issues) telling me what tool you're using. I might add support for a few popular animation tools in the future.

## Mouth shapes

Rhubarb Lip-Sync uses a fixed set of eight mouth shapes, named from A-H. These mouth shapes are based on the six mouth shapes (A-F) originally developed at the Hanna-Barbera animation studios for classic shows such as Scooby-Doo and The Flintstones.

| Name | Image | Description |
| ---- | ----- | ----------- |
| A | ![](img/ken-A.png) | Closed mouth for rest position and the *P*, *B*, and *M* sounds. |
| B | ![](img/ken-B.png) | Slightly open mouth with clenched teeth. Used for most consonants as well as the *EE* sound in b**ee** or sh**e**. |
| C | ![](img/ken-C.png) | Open mouth for the vowels *EH* as in r**e**d, m**e**n; *IH* as in b**i**g, w**i**n; *AH* as in b**u**t, s**u**n, **a**lone; and *EY* as in s**a**y, **e**ight. |
| D | ![](img/ken-D.png) | Wide open mouth for the vowels *AA* as in f**a**ther; *AE* as in **a**t, b**a**t; *AY* as in m**y**, wh**y**, r**i**de; and *AW* as in h**o**w, n**o**w. |
| E | ![](img/ken-E.png) | Slightly rounded mouth for the vowels *AO* as in **o**ff, f**a**ll; *UH* as in sh**ou**ld, c**ou**ld; *OW* as in sh**o**w, c**o**at; and *ER* as in h**er**, b**ir**d. |
| F | ![](img/ken-F.png) | Small rounded mouth for *UW* as in y**ou**, n**ew**; *OY* as in b**o**y, t**o**y; and *W* as in **w**ay. |
| G | ![](img/ken-G.png) | Biting the lower lip for the *F* and *V* sounds. |
| H | ![](img/ken-H.png) | The *L* sound with the tongue slightly visible. |

## How to run Rhubarb Lip-Sync

Rhubarb Lip-Sync is a command-line tool that is currently available for Windows and OS X.

* Download the [latest release](https://github.com/DanielSWolf/rhubarb-lip-sync/releases) and unzip the file anywhere on your computer.
* Call `rhubarb`, passing it a WAVE file as argument, and redirecting the output to a file. This might look like this: `rhubarb my-recording.wav > output.txt`.
* Rhubarb Lip-Sync will analyze the sound file and print the result to `stdout`. If you've redirected `stdout` to a file like above, you will now have an XML file containing the lip-sync data.

The following is a complete list of available command-line options.

| Option | Description |
| ------ | ----------- |
| `-f` *format*,<br/>`--exportFormat` *format* | The export format. Options: `tsv` (tab-separated values), `xml`, `json`. Default value: `tsv` |
| `-d` *text*,<br/>`--dialog` *text* | Allows you to explicitly specify the text of the dialog rather than relying on Rhubarb Lip-Sync's automatic recognition. This is an experimental feature. Currently, the main limitation is that each word must be contained in Rhubarb Lip-Sync's internal dictionary, or the program will fail. |
| `--logFile` *path* | Creates a log file with diagnostic information at the specified path. |
| `--logLevel` *level* | Sets the log level for the log file. Options: `trace`, `debug`, `info`, `warning`, `error`, `fatal`. Default value: `debug` |
| `--version` | Displays version information and exits. |
| `-h`,<br/>`--help` | Displays usage information and exits. |
| *input file* | The input file to be analyzed. Must be an sound file in WAVE format. ||

## How to use the output

The output of Rhubarb Lip-Sync is a file that tells you which mouth shape to display at what time within the recording. You can choose between three file formats -- TSV, XML, and JSON. The following paragraphs show you what each of these formats looks like.

### Tab-separated values (`tsv`)

TSV is the simplest and most compact export format supported by Rhubarb Lip-Sync. Each line starts with a timestamp (in seconds), followed by a tab, followed by the name of the mouth shape. The following is the output for a recording of a person saying 'Hi.'

```
0.00	A
0.09	C
0.17	D
0.38	A
0.47	A
```

You see that at the beginning of the recording, the mouth is closed (shape A). 0.09s into the recording, the mouth opens (shape C); a little later, it opens even wider (shape D). 0.38s into the recording, it closes again (shape A).

The last output line in TSV format is special: Its timestamp is always the very end of the recording (truncated to a multiple of 0.01s) and its value is always a closed mouth (shape A).

### XML format (`xml`)

XML format is rather verbose. The following is the output for a person saying 'Hi,' the same recording as above.

```xml
<?xml version="1.0" encoding="utf-8"?>
<rhubarbResult>
  <metadata>
    <soundFile>C:\Users\Daniel\Desktop\audio-test\hi.wav</soundFile>
    <duration>0.47</duration>
  </metadata>
  <mouthCues>
    <mouthCue start="0.00" end="0.09">A</mouthCue>
    <mouthCue start="0.09" end="0.17">C</mouthCue>
    <mouthCue start="0.17" end="0.38">D</mouthCue>
    <mouthCue start="0.38" end="0.47">A</mouthCue>
  </mouthCues>
</rhubarbResult>
```

The file starts with a `metadata` block containing the full path of the original recording and its duration (truncated to a multiple of 0.01s). After that, each `mouthCue` element indicates the start and end of a certain mouth shape, as explained for TSV format. Note that the end of each mouth cue is identical with the start of the following one. This is a bit redundant, but it means that we don't need a special final element like in TSV format.

### JSON format (`json`)

JSON format is very similar to XML format -- the choice mainly depends on which is better supported by your programming language.  The following is the output for a person saying 'Hi,' the same recording as above.

```json
{
  "metadata": {
    "soundFile": "C:\\Users\\Daniel\\Desktop\\audio-test\\hi.wav",
    "duration": 0.47
  },
  "mouthCues": [
    { "start": 0.00, "end": 0.09, "value": "A" },
    { "start": 0.09, "end": 0.17, "value": "C" },
    { "start": 0.17, "end": 0.38, "value": "D" },
    { "start": 0.38, "end": 0.47, "value": "A" }
  ]
}
```

There is nothing surprising here; everything said about XML format applies to JSON, too.

## Limitations

Rhubarb Lip-Sync has some limitations you should be aware of.

### English only

Rhubarb Lip-Sync only produces good results when you give it recordings in English. You'll get best results with American English.

### Fixed set of mouth shapes

Rhubarb Lip-Sync uses a fixed set of eight mouth shapes, as shown above. If you want to use fewer shapes, you can apply a custom mapping in your own code.

## Tell me what you think!

Right now, Rhubarb Lip-Sync is very much work in progress. If you need help or have any suggestions, feel free to [create an issue](https://github.com/DanielSWolf/rhubarb-lip-sync/issues).
