# The Secret of Mental Island - JS1K 2016 - "EleMental"

In previous JS1K contents people have entered great musical entries before like 
the [Game of Thrones soundtrack](http://js1k.com/2014-dragons/demo/1953) or the
[Synth Sphere](http://js1k.com/2013-spring/demo/1558). Those entries heavily 
relied on procedural generated note progression through code or through bytebeat
functions. This way, composing or recreating music is difficult. So, I am trying 
to find out, if it is possible to use a tracker like Renoise to 
produce music that can still be used in a tiny program like a 1k JavaScript
demo without the necessity to couple code and music too tightly.

## Making the Music

As the first step, I select a fairly complex piece of music. My decision goes
towards the highly sophisticated title soundtrack of Monkey Island that was 
composed by talented Michael Land. So, I sit down and recreate the music with
Renoise using only two monophonic tracks while also enjoying sweet childhood 
memories. My aim is to keep the spirit of the original. I also have to
reduce the number of notes. This reduces the entropy, which increases 
compressibility that becomes important later.

The result of this work is the Renoise tracker file [monkey.xrns](https://github.com/homecoded/demo/raw/master/HC04-secret-of-mental-island-by-homecoded/monkey.xrns).

## Converting the Music to Javascript Data
 
The Renoise module is basically just a ZIP-file. I extracted the ```Song.xml```
from the module and put its contents into a textarea inside a html-file. After
analyzing the XML file, I find that it can be easily parsed via jQuery. The file
consists of a list of patterns, which in turn consist of a list of tracks, which
again consist of a list of lines and lines contain a note node. Each pattern
also defines how many lines are in the pattern.

    <RenoiseSong>
        <PatternPool>
            <Patterns>
                <Pattern>
                    <NumberOfLines>64</NumberOfLines>
                    <Tracks>
                        <PatternTrack>
                            <Lines>
                                <Line>
                                    <NoteColumns>
                                        <NoteColumn>
                                            <Note>C-1</Note>
                                        </NoteColumn>
                                    </NoteColumns>
                                </Line>
                                <Line>
                                    <NoteColumns>
                                        <NoteColumn>
                                            <Note>D-1</Note>
                                        </NoteColumn>
                                    </NoteColumns>
                                </Line>
                                ...
                            </Lines>
                        </PatternTrack>
                    </Tracks>
                </Pattern>
                <Pattern>
                    ...
                </Pattern>
                ...
            </Patterns>
        </PatternPool>
    </RenoiseSong>
    
The result of the first parse run is an array, that contains both tracks with all 
their notes. Together with a mapping table that assigns a frequency to each note
name I create two arrays, one for each track, that contain lists of frequencies. 
While doing that, I create a list of used notes. The list of actually used notes
has a length of 30, an information that will be needed later.

After obtaining the song described as a list of frequencies, I convert the song
into a string where each character represents an index of the used-notes array.
The indexes are encoded as base35 encoded numbers. That means a number may 
be comprised of digits 0-9 and the letters a to y. This analogous to hexadecimal
numbers, which only contain numbers 0-9 and letters a-f. The encode the track
base31 would have sufficed but I decide to use a number that is also used as
a frequency in the note table. This duplication of the number 35 helps compress
the code just a tiny little bit.

    // converting a number to base35
    var iNumber = 20;
    iNumber.toString(35); // "k"

I store my data conversion script in the file [extract_music_data.html](http://htmlpreview.github.io/?https://github.com/homecoded/demo/blob/master/HC04-secret-of-mental-island-by-homecoded/extract_music_data.html).

## Building the Player


