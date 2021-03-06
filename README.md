# blob-opera-midi

Quick and dirty tool to convert 4-part MIDI arrangments to [Blob Opera](https://artsandculture.google.com/experiment/blob-opera/AAHWrq360NcGbw) format JSON.

## Background

https://twitter.com/Overlapping/status/1338979256903208960

## Installation

`$ npm install -g blob-opera-midi`

## Usage

### Converting the MIDI file

If your MIDI file is already exactly 4 tracks in SATB order:
- `$ blob-opera-midi song.mid`
- JSON file will be outputted to `<filename>.mid.json`

If your MIDI is not in track order or you want to preview the track assignments:
- `$ blob-opera-midi song.mid -i` or `$ blob-opera-midi song.mid --interactive`
- Arrow keys to select parts to map, enter to bring up a dialog with a number input corresponding to MIDI tracks from track graph on right, click (yes, with your mouse) the export button, then `esc` or `q` to exit.
- JSON file will be exported as `<filename>.mid.json`.

Add the `--christmas` flag to put Santa hats on the blobs.

### Sideloading the JSON file

1. Open the [Blob Opera][1] web page, preferably with Google Chrome.
2. [Open the developer tools][2]; usually by right-clicking anywhere and choosing **Inspect...**
3. Browse to the application source file (`app.js`) and [add a breakpoint][3] on the first line of this function:
   ```javascript
   t.prototype.finishRecording = function() {
              if (!this.isRecording) // ADD THE BREAKPOINT HERE
                  return null;
              var t = this.currentRecording;
              return function(t, e) {
                  for (var n = 1 / 0, i = 0, r = t.parts; i < r.length; i++) {
                      (l = r[i]).notes.length > 0 && (n = Math.min(n, l.notes[0].timeSeconds))
                  }
                  for (var o = e - n, a = 0, s = t.parts; a < s.length; a++)
                      for (var l, u = 0, c = (l = s[a]).notes; u < c.length; u++) {
                          c[u].timeSeconds += o
                      }
              }(t, .2),
              this.isRecording = !1,
              this.currentRecording = null,
              t
          }
   ```
4. Start a new recording by pressing the red button on the bottom-left corner and stop it again.
5. Now you've hit the breakpoint; type `this.currentRecording = <contents>` where `<contents>` should be replaced by the contents of the `libretto.json` file generated previously.
6. Resume the execution by clicking the play button on the debugger.
7. Wait a few seconds for the speed to stabilize and stop it.

You can also save your sideloaded song to a [shareable URL](https://artsandculture.google.com/experiment/blob-opera/AAHWrq360NcGbw?cp=eyJyIjoiS0JPbTd4amd4eTRkIn0) by encoding it using `te.RecordingMessage.encode(<JSON>).finish()` at line 9428 of the main app file:

![Screenshot of Chrome dev tools with a breakpoint set on line 9428 of prettified app source](https://i.imgur.com/Vyp9Pdv.png)

## Objections

### But isn't this really hacky?

Yes.

### Can't you just control the Blob Opera directly with MIDI signals?

Yes, but that's not interesting to me and this has advantages over that like temporally consistent vocalizations without having to do any manual programming.

### There's something wrong with the timing!

Probably. Submit a github issue!

## Related links

- [blob-opera-toolkit](https://github.com/0x2b3bfa0/blob-opera-toolkit) (python, supports MusicXML with real lyrics)
- [The Silvis Woodshed](http://gasilvis.net/) (MIDI scores for choral music, mostly in parts in SATB order)

[1]: https://artsandculture.google.com/experiment/blob-opera/AAHWrq360NcGbw
[2]: https://developers.google.com/web/tools/chrome-devtools/open
[3]: https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints#loc
