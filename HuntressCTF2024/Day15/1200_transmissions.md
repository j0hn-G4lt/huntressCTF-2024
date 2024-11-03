## 1200 Transmissions
Given a .wav file, it is clearly some sort of encoding when listening to playback. It sounds like dialup for the phreaks out there. 1200 is a clue to the baud rate which is how many symbols per second are recieved. Using a tool to make sense of this we can simply use this command: `minimodem -r 1200 -f transmissions.wav`
Output:
```python
### CARRIER 1200 @ 1200.0 Hz ###
Greetings, Professor Falken. 

Would you like to play a game? 

flag{f28d133e7174c412c1e39b4a84158fa3}

Thanks for playing the Huntress CTF!

        @
       @@
       @@@@
  @@@@  @@@@@@  @@ @@@@@@@@
   @@@@@@ @@@@@@@   @  @  @@@@
     @@@@@@@@@@@@@@ @@@ @@   @@@
 @@@@@@# @@@@@@@@@@@ @@@@@@@@  @@@
  @@@@@@@@@@@@@@@@@@@@@@@@@@@@@ @@@
  @@         @@@@@@@@@@@@@@@     @@
  @@@@@@@@@@@@/@@@@@@@@@     @(~ @@
  @@    @@@@@@@@@@@ @@@@ @@@@  @@@
  @@@ @@@@.@@@@@@@@ @@@@@ @@@@  @@
   @@@  @@@*%@@@@ @@@ @@@@ @@@@@
     @@@    @   @@@@@@@@@ @@@@@
       @@@@.     @@@@@@@@ @@@@
           @@@@@@@@@ @@@@@ @@
                        @@
                         @ -dk

### NOCARRIER ndata=663 confidence=4.773 ampl=1.001 bps=1200.00 (rate perfect) ###
```
