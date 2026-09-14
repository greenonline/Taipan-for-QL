# Taipan for QL

***Work in progress***

Playable, just about...

## Preamble

What a horrific port! I do not like entering programs into the QL at all. 

Funnily enough, I was tempted to purchase a QL for retro purposes, after having briefly owned one in the 80s (and clearly having very rose-tinted glasses), but after having ported this, I have no desire, whatsoever, to ever touch or see a QL ever again. No wonder the platform failed. Shockingly bad.

I am disappointed to say this, but I think that this QL port was even more horrific than the god-awful MMBASIC port.  It took hours to get the splash screen right and days to get the `READ` statements to work.

The emulator, Q-EmuLator, has some issues too. The "try me" dialog tends to lock up. Don't try to attach a drive before dismissing the "try me" dialog, it locks up. There doesn't seem to be a Github repo where to report issues.

Note: <kbd>CTRL</kbd>+<kbd>SPACE</kbd> to `BREAK`

### Main issues

 - Have to paste lines one or two at a time (no easy copy paste, nor text file input)
   - You can modify the saved files (emulator microdrive mapped to local directory) directly, as they are in text format
 - Problem getting line entered, whilst avoiding "bad line" errors
 - Problem then getting it to run
 - Have to slowly delete a line, one character at a time, for a "bad line" error. Can not just discard the line with a single keystroke
 - Microdrives are slow! Taipan takes almost as long as the Hobbit (8 minutes), to load! Saving is relatively quick, however.
 - No ordering on `dir` output
 - No indication of where, in a multi-statement line, an error exists
 - Undeclared/undefined variables contain '*' instead of'0', causes many "error in expression" errors
 - Annoying F1 selection required *every time* the QL boots
 - Substrings are not handled consistently

## Links

 - [QL vs Spectrum](https://misterspectrum.com/QLSuperBASIC.html)

## Longstanding issues with the original Apple II code

 - Unassigned variables
 - `K=1` in line 791 is superfluous

 
    ```none
    785 REM EVENTS SUBROUTINE (790-851)
    790 IF K = 1 THEN RETURN
    791 K=1:X = 50 + INT ( RND (1) * 100) + 1: GN = INT ( RND (1) * 3) +1:XP = (X + (GN * 50)) * 100:IF C < XP OR RND (1) < .75 THEN GOTO 805
    792 GOSUB 1340: VTAB= 12:PRINT " A BROKER OFFERS TO TAKE YOUR": PRINT "VESSEL IN TRADE FOR ONE WITH":PRINT GN + G;" GUNS & ";X + MW;" CAPACITY"
    ```

    should be

    ```none
    785 REM EVENTS SUBROUTINE (790-851)
    790 IF K = 1 THEN RETURN
    791 X = 50 + INT ( RND (1) * 100) + 1: GN = INT ( RND (1) * 3) +1:XP = (X + (GN * 50)) * 100:IF C < XP OR RND (1) < .75 THEN GOTO 805
    792 GOSUB 1340: VTAB= 12:PRINT " A BROKER OFFERS TO TAKE YOUR": PRINT "VESSEL IN TRADE FOR ONE WITH":PRINT GN + G;" GUNS & ";X + MW;" CAPACITY"
    ```

 - The destinations should be in two columns. However, Apple nor CP/M do. Nor BBC? 
   - Nope, destination is only two column for the records. Maybe two column should also be used for embarking?
 - Should change temple donation prompt to "Y = Yes", as any other key is a no.


#### Reserved variable names?

```none
ET
LOC
LOCA
```

## Notes 

### Entering code bugs

#### No dual NEXT statements

Line 40 bad

40 FOR I = 0 TO 9: FOR J = 0 TO 5:READ AP(I,J):AP(I,J) = AP(I,J) * 6 ^ (5 - J): NEXT J ,I: GOSUB 180

Two `NEXT` statements:

40 FOR I = 0 TO 9: FOR J = 0 TO 5:READ AP(I,J):AP(I,J) = AP(I,J) * 6 ^ (5 - J): NEXT J: NEXT I: GOSUB 180

#### No `THEN <line number>`

61 IF PEEK ( -16384) < 128 THEN 61
61 IF PEEK ( -16384) < 128 THEN GOTO 61

#### More spaces

161 IFJD<1THENJD=1

161 IF JD<1THEN JD=1

#### No space between < and = in <=

341 IF X = 1 AND NUM * GP(X1) < = C THEN SG(X1) = SG(X1) + NUM: SH=SH- NUM:C = C - GP(X1) * NUM:GOSUB 130: GOTO 230

341 IF X = 1 AND NUM * GP(X1) <= C THEN SG(X1) = SG(X1) + NUM: SH=SH- NUM:C = C - GP(X1) * NUM:GOSUB 130: GOTO 230

Also line 431, 461, 561, 1140

#### 400 bad line???

In quotes, need semicolon

```none
400 VTAB= 13:PRINT "MOVE WHAT ("TC$")? ": GOSUB 260:RETURN
```


#### More spaces everywhere!

720 FORI=0TO9STEP2: VTAB= (I / 2) + 4:PRINT A$:VTAB= (I / 2) + 4: PRINT I;" ";L$(I);: HTAB= 20:PRINT I + 1;" ";L$(I+1):NEXT I:PRINT

720 FOR I=0 TO 9 STEP 2: VTAB= (I / 2) + 4:PRINT A$:VTAB= (I / 2) + 4: PRINT I;" ";L$(I);: HTAB= 20:PRINT I + 1;" ";L$(I+1):NEXT I:PRINT

#### Must have space after IF, as well as around AND

850 IF(TR=0ANDL=0) OR X$ = "Y" AND L = 0 THEN PRINT: PRINT A$;:GOSUB 780:VTAB= 13:PRINT A$:PRINT:PRINT

850 IF (TR=0 AND L=0) OR X$ = "Y" AND L = 0 THEN PRINT: PRINT A$;:GOSUB 780:VTAB= 13:PRINT A$:PRINT:PRINT


#### `LOC$` is reserved?

Line 30 hacks

`loc$="hong kong"` gives an error

`location$="hong kong"` does not, nor does `lo$="hong kong"`


#### L=0 is "not implemented"

Similarly, trying to enter `l=0` gives a "not implemented" error, where as `k=0` works as one would expect.















### Runtime bugs

#### Set `HOME`, `INVERSE` and `NORMAL` to be variables

Search and replace with a `=1` suffix.

`FLASH` is an accepted keyword and need to be either `FLASH 1` or `FLASH 0`.

Yes, they *could* be deleted, as superfluous to requirements, *and* they increase memory requirements ever so slightly, but they are useful to keep around as "original formatting markers" for future formatting improvements, that should be made in order for the output to match that of the Apple II original.

#### Set `VTAB` and `HTAB` to be variables

Search and replace with a `=1` suffix.

Likewise, they *could* be deleted, as superfluous to requirements, *and* they increase memory requirements ever so slightly, but they are useful to keep around as "original formatting markers" for future formatting improvements, that should be made in order for the output to match that of the Apple II original.

#### No `PRINT TAB()`

Use `PRINT TO`

Even ZXSpectrum had `TAB` and `AT`!

####  FOR in line 30

Firstly, there was a missing `DIM L$(9)` (known issue with Apple II code):

```none
30 DIM M$(11),G$(5),AP(9,5),GG(5),L(9,5),GP(5),V(9),L$(9):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I
```

Problem reading (multi-dim) array of strings

```none
30 DIM M$(11),G$(5),AP(9,5),GG(5),L(9,5),GP(5),V(9),L$(9)
31 FOR I = 0 TO 9:READ L$(I):NEXT I
```

Aside from the weird issues where `LOC$` not being allowed to entered (see above), even this use of an intermediary string, fails at runtime, with "line 32 error in expression"

```none
30 DIM L$(9)
31 FOR I = 0 TO 9
32 READ LO$ 
33 L$(I)=LO$
34 NEXT I
70 DATA HONGKONG, FOOCHOU, SHANGHAI, NAGASAKI, MANILA, SINGAPORE, BATAVIA, SAIGON, CALCUTTA, LIVERPOOL
```

This was due to a lack of quote around the DATA items

```none
30 DIM L$(9)
31 FOR I = 0 TO 9
32 READ LO$ 
33 L$(I)=LO$
34 NEXT I
70 DATA 'HONGKONG', 'FOOCHOU', 'SHANGHAI', 'NAGASAKI', 'MANILA', 'SINGAPORE', 'BATAVIA', 'SAIGON', 'CALCUTTA', 'LIVERPOOL'
```

The above now fails with with "line 33 error in expression", 

Removing line 33 and the assignment, but then it fails on multiple runs.

```none
30 DIM L$(9)
31 FOR I = 0 TO 9
32 READ LO$ 
34 NEXT I
70 DATA 'HONGKONG', 'FOOCHOU', 'SHANGHAI', 'NAGASAKI', 'MANILA', 'SINGAPORE', 'BATAVIA', 'SAIGON', 'CALCUTTA', 'LIVERPOOL'
```

A `RESTORE` is needed.

This works well, repeatedly

```none
25 RESTORE
30 DIM L$(9)
31 FOR I = 0 TO 9
32 READ LO$ 
34 NEXT I
70 DATA 'HONGKONG', 'FOOCHOU', 'SHANGHAI', 'NAGASAKI', 'MANILA', 'SINGAPORE', 'BATAVIA', 'SAIGON', 'CALCUTTA', 'LIVERPOOL'
```


However, even with an added `RESTORE`, this still fails (even with quotes added to all data strings):

```none
30 RESTORE:DIM M$(11),G$(5),AP(9,5),GG(5),L(9,5),GP(5),V(9),L$(9):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I

```

Adding second dimension to string arrays fixed it:

```none
30 RESTORE:DIM M$(11,10),G$(5,10),AP(9,5),GG(5),L(9,5),GP(5),V(9),L$(9,10):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I
```

Needed to fix line 30:

 - quotes around the string data items (that wasn't required for Apple II, nor many other ports.
 - `RESTORE`
 - Multi-dimension string array (second dimension = 10)


#### Line 180: `RND(1)`


Needs to be just `RND`

Side note: when the program crashes at this point, `PRINT I` correctly shows `0`, but `PRINT L` scrolls a series of `0` up the screen!!!

Separating out:

```none
180 FOR I = 0 TO 5
181 GP(I) = INT (AP(L,I) + ( RND * AP(L,I)))
182 NEXT I: RETURN
```

gives `At line 181 error in expression`

The problem is *probably* due to the fact that `L` is never set (a known Apple II issue, which relies on undeclared variables defaulting to 0)

However, adding the line 
```none
12 L = 0
```

gives a "not implemented" error (see **Entering code bugs - L=0 is "not implemented"** above).

Maybe can not have `L(9,5)` and `L`? No:

Fails, "At line 10 bad name"

10 L=0
20 DIM L(9,5)
30 PRINT L

Likewise,
 
10 K=0
20 DIM K(9,5)
30 PRINT K

This is OK:

10 LO=0
20 DIM L(9,5)
30 PRINT LO

But this gives "At line 10 not implemented":

10 L=0
20 DIM LO(9,5)
30 PRINT L


However, line 50 in TAIPAN.BAS already has a LO() array, that is missing a DIM

50 FOR I = 0 TO 9:READ LO(I):NEXT I:D = 1000: Y=1860: GT = 1: C=400:MW = 50:SH = MW:SR = 1: G=1:V(0) = 1: GOSUB 5000: X$ ="":GOSUB 590: HOME=1:GOTO 120

becomes 

50 DIM LO(9): FOR I = 0 TO 9:READ LO(I):NEXT I:D = 1000: Y=1860: GT = 1: C=400:MW = 50:SH = MW:SR = 1: G=1:V(0) = 1: GOSUB 5000: X$ ="":GOSUB 590: HOME=1:GOTO 120


Note:

```none
l=0
lo=0
loc=0
bad line
loca=0
bad line
locat=0
```

Solution: Change `L` to `LOCAT`


This fixes it

```none
12 LOCAT = 0
50 DIM LO(9): FOR I = 0 TO 9:READ LO(I):NEXT I:D = 1000: Y=1860: GT = 1: C=400:MW = 50:SH = MW:SR = 1: G=1:V(0) = 1: GOSUB 5000: X$ ="":GOSUB 590: HOME=1:GOTO 120
180 FOR I = 0 TO 5:GP(I) = INT (AP(LOCAT,I) + ( RND * AP(LOCAT,I))):NEXT I: RETURN
```

#### Line 5010: Error in expression


```none
ch$(1)="ll"
v1=0
ch$(v1)="ll"
error in expression
v1=1
ch$(v1)="ll"
```

Very odd


Anyway, needed second dimension

```none
5000 DIM CH$(14),CN$(14 )
```

to

```none
5000 DIM CH$(14,24),CN$(14,24)
```

#### Line 590: Bad parameter

Flash

```none
590 VTAB= 15: HTAB= 8:PRINT "PRESS <";: FLASH:PRINT "SPACEBAR";:NORMAL=1:PRINT "> TO START"; : GOSUB 60:IF X$ = " " THEN RETURN
```

to

```none
590 VTAB= 15: HTAB= 8:PRINT "PRESS <";: FLASH 1:PRINT "SPACEBAR";: FLASH 0:NORMAL=1:PRINT "> TO START"; : GOSUB 60:IF X$ = " " THEN RETURN
```

#### GET KEY line 60

```none
55 REM GET$ SUBROUTINE (60-64)
60 REM POKE -16368, 0
61 REM IF PEEK ( -16384) < 128 THEN GOTO 61
62 REM X$ = CHR$ ( PEEK ( -16384) - 128)
63 REM POKE -16368, 0
63 X$ = INKEY$: IF X$="" THEN GOTO 63
64 RETURN
```

#### Line 130: error in expression

```none
130 VTAB= 1: HTAB= 1:PRINT "PORT ";L$(L);: HTAB= 28:PRINT M$(M);". ";DA+1;",";Y
```
Using `LOCAT` instead of `L`:

```none
130 VTAB= 1: HTAB= 1:PRINT "PORT ";L$(LOCAT);: HTAB= 28:PRINT M$(M);". ";DA+1;",";Y
```

But the continuing "error in expression" is due to also neither `DA` nor `M` has not been set, so 

```none
12 LOCAT = 0:DA=0:M=0
```

#### Line 150: error in expression

```none
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT G$(I):VTAB= 5 + I:HTAB= 11:PRINT CHR$ (133);: Q=SG(I):GOSUB 1330:VTAB= 5 + I:HTAB= 26:PRINT CHR$ (133);: Q = GG(I):GOSUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETURN
```

Had to add a `DIM SG(9)` to line 30

```none
30 RESTORE:DIM M$(11,10),G$(5,10),AP(9,5),GG(5),L(9,5),GP(5),V(9),L$(9,10),SG(9):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I
```
#### line 70, error in expression

`k` has not been declared

```none
12 LOCAT = 0:DA=0:M=0:K=0
```

#### 190 - error in expression

Using `LOCAT`
```none
190 FOR I = 0 TO 5:IF GP(I) > H(L,I) THEN H(L,I) = GP(I)
```

becomes

```none
190 FOR I = 0 TO 5:IF GP(I) > H(LOCAT,I) THEN H(LOCAT,I) = GP(I)
```

Also `H()` not declared:

```none
30 RESTORE:DIM M$(11,10),G$(5,10),AP(9,5),GG(5),H(9,5),L(9,5),GP(5),V(9),L$(9,10),SG(9):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I
```

#### 880 - error in expression

`RND` again
```none
880 GP(I) = INT (GP(I) * ( RND(1) * 4) + .5)
```

becomes

```none
880 GP(I) = INT (GP(I) * ( RND * 4) + .5)
```

#### 890 - error in expression

Using `LOCAT`

```none
890 VTAB= 12:PRINT L$(LOCAT);" MARKET FORCES HAVE": PRINT "DRIVEN ";G$(I);" PRICES TO ";:PRINT GP(I);: PRINT "!";
```

becomes

```none
890 VTAB= 12:PRINT L$(LOCAT);" MARKET FORCES HAVE": PRINT "DRIVEN ";G$(I);" PRICES TO ";:PRINT GP(I);: PRINT "!";
```


### 200 - error in expression

```none
200 IF GP(I) < L(L,I) OR L(L,I)=0 THEN L(L,I)= GP (I)
```

becomes

```none
200 IF GP(I) < L(LOCAT,I) OR L(LOCAT,I)=0 THEN L(LOCAT,I)= GP (I)
```

#### 820 - error in expression

LOCAT

```none
820 GOSUB 180: GOSUB 860: GOSUB 190:GOSUB 1340:DN = INT ((C / 2) * RND (1)):IF RND (1) > .8 AND TR = 0 AND LOCAT <> 0 THEN VTAB= 12:PRINT "A MESSENGER FROM ";LY$;" ASKS": PRINT "THAT YOU RETURN TO HONG KONG"
```

and RND

```none
820 GOSUB 180: GOSUB 860: GOSUB 190:GOSUB 1340:DN = INT ((C / 2) * RND):IF RND > .8 AND TR = 0 AND LOCAT <> 0 THEN VTAB= 12:PRINT "A MESSENGER FROM ";LY$;" ASKS": PRINT "THAT YOU RETURN TO HONG KONG"
```

Also `TR` not set

```none
12 LOCAT = 0:DA=0:M=0:K=0:TR=0
```

#### 880 - out of range

```none
880 GP(I) = INT (GP(I) * ( RND * 4) + .5)
```

Due to function of RND(1)

```none
860 I = INT ( RND(1)  * 6)
```

becomes

```none
860 I = INT ( RND  * 6)
```

Note: I did a search and replace: RND(1) -> RND


#### More `LOCAT`

Lines 820-850


#### 750 - error in expression
750 GOSUB 60:IF ASC (X$) > 47 AND ASC (X$) < 58 AND VAL (X$) <> L THEN PO = VAL (X$) : GOTO 980

#### `VAL()` not required

```none
750 GO SUB 60:IF ASC (X$) > 47 AND ASC (X$) < 58 AND VAL (X$) <> LOCAT THEN PO = X$ : GO TO 980
```
becomes

```none
750 GO SUB 60:IF ASC (X$) > 47 AND ASC (X$) < 58 AND X$ <> LOCAT THEN PO = X$ : GO TO 980
```

#### `ASC()` not supported?

Again line 750... use `CODE`

#### 980 - error in expression

LOCAT

```none
980 HOME=1: PRINT:INVERSE=1:PRINT A$;:NORMAL=1:PRINT " SEA VOYAGE FROM ";L$(LOCAT) : PRINT " TO ";L$(PO) :INVERSE=1: PRINT A$: NORMAL=1: GO SUB 780: HOME=1:ET = ABS (LO(LOCAT) - LO(PO))
```

There seems to be a problem with `ET = ABS (LO(LOCAT) - LO(PO))`, but `EST = ABS (LO(LOCAT) - LO(PO))` or `RT = ABS (LO(LOCAT) - LO(PO))` are fine!

Change to EST

```none
980 HOME=1: PRINT:INVERSE=1:PRINT A$;:NORMAL=1:PRINT " SEA VOYAGE FROM ";L$(LOCAT) : PRINT " TO ";L$(PO) :INVERSE=1: PRINT A$: NORMAL=1: GO SUB 780: HOME=1:ET = ABS (LO(LOCAT) - LO(PO))
```

#### 160 - error in expression

160 EST = INT (EST + (EST * RND / 3)): GT = GT +EST:D = D + INT (D * (EST / 360)):JD = JD +EST:IF JD > 360 THEN JD = JD-360: Y=Y+1


JD is not set.

```none
12 LOCAT = 0:DA=0:M=0:K=0:TR=0:JD=0
```

### 5350 - error in expression

```none
5350 VTAB= B+1: HTAB=40-A:PRINT LEFT$ (CH$(B),A),;" ";
```

```none
5350 VTAB= B+1: HTAB=40-A:PRINT CH$(B)(TO A),;" ";
```

But now gives "out of range" error: add `1 TO`

```none
5350 VTAB= B+1: HTAB=40-A:PRINT CH$(B)(1 TO A),;" ";
```

> Even more oddly, except on SMS and Minerva, if a start descriptor is omitted, but an end descriptor specified, the index defaults to: 0 TO end_descriptor normally resulting in an error. (On SMS and Minerva this defaults to 1 TO end_descriptor).

Later still get out of range. Change loop index to length of string

```none
5320 FOR A = 1 TO 30
```

to
 
```none
5320 FOR A = 1 TO 24
```


#### 1001 - error in expression

LOCAT

```none
1001 VTAB= 14:PRINT " ANY PORT IN A STORM,                 ": GO SUB 760:PO = INT ( RND * 10) :IF PO = LOCAT THEN VTAB= 14: PRINT" WE CAN'T MAKE IT,                      TAIPAN,            ": SR = 0:GO TO 1090
```

#### 5470 `LEFT$` and `MID$`

Use `TO`

```none
5740 CH$(I1) = LEFT$ (CH$(I1),I - 1) + " " + MID$ (CH$(I1) , I + 1, LEN (CH$(I1)))
5740 CH$(I1) = CH$(I1)(1 TO I - 1) + " " + CH$(I1) (I + 1 TO LEN (CH$(I1)))
```

#### 5470 concatenation

Use `&`

```none
5740 CH$(I1) = CH$(I1)(1 TO I - 1) + " " + CH$(I1) (I + 1 TO LEN (CH$(I1)))
5740 CH$(I1) = CH$(I1)(1 TO I - 1) & " " & CH$(I1) (I + 1 TO LEN (CH$(I1)))
```


#### 6190

```none
6190 HTAB= 1: VTAB=B+1:PRINT RIGHT$ (CH$ (B) ,A) ;" "
6190 HTAB= 1: VTAB=B+1:PRINT CH$ (B)(A TO LEN(CH$ (B))) ;" "
```

#### Fixing the destinations

Only the last destination is printed, and only in one column, in the middle of the screen. Adding `TO` does not help.

```none
720 FOR I=0 TO 9 STEP 2: VTAB= (I / 2) + 4:PRINT A$:VTAB= (I / 2) + 4: PRINT I;" ";L$(I);: HTAB= 20:PRINT I + 1;" ";L$(I+1):NEXT I:PRINT
720 FOR I=0 TO 9 STEP 2: VTAB= (I / 2) + 4:PRINT A$:VTAB= (I / 2) + 4: PRINT I;" ";L$(I);: HTAB= 20:PRINT TO 20;I + 1;" ";L$(I+1):NEXT I:PRINT
```

This snippet works:

```none
10 HOME=1: A$ = "                                        ":W$ = "ELDER BROTHER WU":LY$ = "LI YUEN":YS$ = "YANATO & SMYTHE":TC$ = "O, S, T, A, P, OR R"
30 RESTORE :DIM M$(11,10),G$(5,10),AP(9,5),GG(5),H(9,5),L(9,5),GP(5),V(9),L$(9,10),SG(9):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I

65 REMark INITIALIZATION DATA (70-110)
70 DATA 'HONGKONG', 'FOOCHOU', 'SHANGHAI', 'NAGASAKI', 'MANILA', 'SINGAPORE', 'BATAVIA', 'SAIGON', 'CALCUTTA', 'LIVERPOOL'
80 DATA 'JAN', 'FEB', 'MAR', 'APR', 'MAY', 'JUN'
81 DATA 'JUL', 'AUG', 'SEP', 'OCT', 'NOV', 'DEC'
90 DATA 'OPIUM', 'SILK', 'TEA', 'ARMS', 'PEPPER', 'RICE'

720 FOR I=0 TO 9 STEP 2: VTAB= (I / 2) + 4:PRINT A$:VTAB= (I / 2) + 4: PRINT I;" ";L$(I);: HTAB= 20:PRINT TO 20;I + 1;" ";L$(I+1):NEXT I:PRINT
```

So why doesn't the program print the tabs during the game play? If you break after the screen has been printed and the computer is waiting your response, and then you enter `GOTO 720`, on the "command line", then the table is displayed correctly!

720 is for the records section. The ports of destination are displayed at 731. There is clearly an issue with the FOR on a different line from the rest of the block and next

```none
731 IF SH >= 0 THEN HOME=1: PRINT TO 11;:INVERSE=1:PRINT "EMBARKING":NORMAL=1:PRINT TO 9;"FROM " ;L$ (LOCAT) : INVERSE=1:PRINT A$:NORMAL=1:FOR I = 0 TO 9:IF LOCAT = I THEN NEXT I:GOTO 740
732 IF LOCAT <> I THEN PRINT TO 10;I;" ";L$(I): NEXT I
```

becomes

```none
731 IF SH >= 0 THEN HOME=1: PRINT TO 11;:INVERSE=1:PRINT "EMBARKING":NORMAL=1:PRINT TO 9;"FROM " ;L$ (LOCAT) : INVERSE=1:PRINT A$:NORMAL=1:
732 FOR I = 0 TO 9
733 IF LOCAT = I THEN NEXT I:GOTO 740
734 IF LOCAT <> I THEN PRINT TO 10;I;" ";L$(I)
735 NEXT I
```

### Market Prices

All printed on one line, bunched up:

```none
220 GO SUB 790: GO SUB 1340:VTAB= 11:INVERSE=1:HTAB= 8:PRINT " ";L$(LOCAT);" MARKET PRICES ":NORMAL=1:PRINT A$:FOR I = 0 TO 4 STEP 2: VTAB= 13 + I / 2:HTAB= 1:PRINT G$(I);: HTAB= 10:PRINT GP(I) ;: HTAB= 21:PRINT G$(I + 1);
221 HTAB= 30:PRINT GP(I + 1) :NEXT I
```

becomes

```none
220 GO SUB 790: GO SUB 1340:VTAB= 11:INVERSE=1:HTAB= 8:PRINT TO 8; " ";L$(LOCAT);" MARKET PRICES ":NORMAL=1:PRINT A$:FOR I = 0 TO 4 STEP 2: VTAB= 13 + I / 2:HTAB= 1:PRINT G$(I);: HTAB= 10:PRINT TO 10; GP(I);: HTAB= 21:PRINT TO 21; G$(I + 1);
221 HTAB= 30:PRINT TO 30; GP(I + 1) :NEXT I
```

But still messed up a bit, so separate out the FOR/NEXT

```none
220 GO SUB 790: GO SUB 1340:VTAB= 11:INVERSE=1:HTAB= 8:PRINT TO 8; " ";L$(LOCAT);" MARKET PRICES ":NORMAL=1:PRINT A$
221 FOR I = 0 TO 4 STEP 2
222 VTAB= 13 + I / 2:HTAB= 1:PRINT G$(I);: HTAB= 10:PRINT TO 10; GP(I);: HTAB= 21:PRINT TO 21; G$(I + 1);
223 HTAB= 30:PRINT TO 30; GP(I + 1) 
224 NEXT I
```

### Ship status

Adding `TO x` for the `HTAB` statements:

```none
130 VTAB= 1: HTAB= 1:PRINT "PORT ";L$(LOCAT);: HTAB= 28:PRINT M$(M);". ";DA+1;",";Y
140 VTAB= 2: INVERSE=1:PRINT "CASH ";: Q = C:GO SUB 1330: NORMAL=1:VTAB= 2:HTAB= 28:PRINT "GUNS ";G: VTAB= 3:PRINT "DEBT ";: Q = D:GO SUB 1330:VTAB= 3:HTAB= 28:PRINT "HOLD ";: Q = SH:GO SUB 1330
```

becomes


```none
130 VTAB= 1: HTAB= 1:PRINT "PORT ";L$(LOCAT);: HTAB= 28:PRINT TO 28; M$(M);". ";DA+1;",";Y
140 VTAB= 2: INVERSE=1:PRINT "CASH ";: Q = C:GO SUB 1330: NORMAL=1:VTAB= 2:HTAB= 28:PRINT TO 28; "GUNS ";G: VTAB= 3:PRINT "DEBT ";: Q = D:GO SUB 1330:VTAB= 3:HTAB= 28:PRINT TO 28;"HOLD ";: Q = SH:GO SUB 1330
```

#### Big number routine and subsequent "blank `PRINT`" statements and semicolons

NOTE: These couple of changes are **only** required for versions that scroll, like the CP/M. Full screen versions would not require the changes to the big number routine, nor the blank PRINT statements and semicolons - as the info is *placed* using `AT`, `LOCATE` or whatever, and does not rely upon sequential printing.

But there is still the same issue as was had with the CP/M port, namely the big number routine, needs a `;`

```none
1330 IF ABS (Q) < 1E6 THEN PRINT INT (Q);: NORMAL=1:PRINT "   ": RETurn 
...
1335 PRINT INT (Q);Q$;: NORMAL=1:PRINT "      "
```

becomes

```none
1330 IF ABS (Q) < 1E6 THEN PRINT INT (Q);: NORMAL=1:PRINT "   ";: RETurn 
...
1335 PRINT INT (Q);Q$;: NORMAL=1:PRINT "      ";
```

But now additional blank PRINT is required to clear the line

```none
140 VTAB= 2: INVERSE=1:PRINT "CASH ";: Q = C:GO SUB 1330: NORMAL=1:VTAB= 2:HTAB= 28:PRINT TO 28; "GUNS ";G: VTAB= 3:PRINT "DEBT ";: Q = D:GO SUB 1330:VTAB= 3:HTAB= 28:PRINT TO 28;"HOLD ";: Q = SH:GO SUB 1330
141 VTAB= 4: INVERSE=1:PRINT "GOODS     ABOARD SHIP    HONGKONG GODOWN":NORMAL=1
```

becomes

```none
140 PRINT:VTAB= 2: INVERSE=1:PRINT "CASH ";: Q = C:GO SUB 1330: NORMAL=1:VTAB= 2:HTAB= 28:PRINT TO 28; "GUNS ";G: VTAB= 3:PRINT "DEBT ";: Q = D:GO SUB 1330:VTAB= 3:HTAB= 28:PRINT TO 28;"HOLD ";: Q = SH:GO SUB 1330
141 PRINT:VTAB= 4: INVERSE=1:PRINT "GOODS     ABOARD SHIP    HONGKONG GODOWN":NORMAL=1
```

### Cargo

Need blank print and a semicolon

```none
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT G$(I):VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETurn 
```

becomes

```none
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETurn 
```

but still bad. It is as if the fix above for the big number routine, i.e. the semicolon, is not "respected", and ignored?

[![Bad argo display][1]][1]

Is it the `CHR$(133)` character? No, as these two lines do the same (the first prints "A" as a column, and the second has the column removed entirely):

```none
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (65);: Q=SG(I):GOSUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (65);: Q = GG(I):GOSUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETURN
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; : Q=SG(I):GOSUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;: Q = GG(I):GOSUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETURN
```

***Still investigating***

In an MRE, if the `FOR-NEXT` is removed, and manually setting `I`, then multi line gives a weird spurious `0` on a newline


```none
10 HOME=1: A$ = "                                        ":W$ = "ELDER BROTHER WU":LY$ = "LI YUEN":YS$ = "YANATO & SMYTHE":TC$ = "O, S, T, A, P, OR R"
30 RESTORE :DIM M$(11,10),G$(5,10),AP(9,5),GG(5),H(9,5),L(9,5),GP(5),V(9),L$(9,10),SG(9):FOR I = 0 TO 9:READ L$(I):NEXT I:FOR I = 0 TO 11: READ M$(I):NEXT I:FOR I = 0 TO 5: READ G$(I):NEXT I

65 REMark INITIALIZATION DATA (70-110)
70 DATA 'HONGKONG', 'FOOCHOU', 'SHANGHAI', 'NAGASAKI', 'MANILA', 'SINGAPORE', 'BATAVIA', 'SAIGON', 'CALCUTTA', 'LIVERPOOL'
80 DATA 'JAN', 'FEB', 'MAR', 'APR', 'MAY', 'JUN'
81 DATA 'JUL', 'AUG', 'SEP', 'OCT', 'NOV', 'DEC'
90 DATA 'OPIUM', 'SILK', 'TEA', 'ARMS', 'PEPPER', 'RICE'

141 PRINT:VTAB= 4: INVERSE=1:PRINT "GOODS     ABOARD SHIP    HONGKONG GODOWN":NORMAL=1
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETurn 

150 I = 0: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: INVERSE=1: PRINT A$: NORMAL=1 
151 I = 1: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: INVERSE=1: PRINT A$: NORMAL=1 
152 I = 2: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: INVERSE=1: PRINT A$: NORMAL=1 

1325 REMark BIG NUMBER SUBROUTINE (1330-1370)
1330 IF ABS (Q) < 1E6 THEN PRINT INT (Q);: NORMAL=1:PRINT "   ";: RETurn 
1331 IF ABS (Q) < 1E9 THEN Q = Q / 1E6: Q$ = "MIL":GO TO 1335
1332 IF ABS (Q) < 1E12 THEN Q = Q / 1E9: Q$ = "BIL":GO TO 1335
1333 IF ABS (Q) >= 1E12 THEN Q = Q / 1E12: Q$ = "TRL":GO TO 1335
1335 PRINT INT (Q);Q$;: NORMAL=1:PRINT "      ";
1337 RETurn 
```


[![Spurious 0][2]][2]

However this was only due to the missing `STOP` nd the program runs into the subroutine, without the subroutine having been called:

```none
1321 STOP
```

With line 1321 reinstated, then the output is fine, when manually setting `I`:

[![OK manual][3]][3]

TODO: Remove the `A$`, blank line, just use a blank `PRINT` instead..? Nope! The `A$` is only printer *after* the table... so what is causing the blank lines? Is it the restricted screen width? It turned out that the blank lines dissapeared when the `FOR-NEXT` loop was split out, see below.

So, if manually setting `I` works fine, then maybe it is another `FOR-NEXT` multi-statement line issue that needs to be broken out into individual lines again – as per lines 220/221 (market prices), and lines 731/732 (embarking destinations)

Indeed, it *does* fix it!

Splitting up 

```none
150 FOR I = 0 TO 5: VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330: NEXT I: INVERSE=1: PRINT A$: NORMAL=1: RETurn 
```

to become:

```none
150 FOR I = 0 TO 5
151 VTAB= 5 + I:PRINT:PRINT G$(I);:VTAB= 5 + I:HTAB= 11:PRINT TO 11; CHR$ (133);: Q=SG(I):GO SUB 1330:VTAB= 5 + I:HTAB= 26:PRINT TO 26;CHR$ (133);: Q = GG(I):GO SUB 1330
153 NEXT I
154 INVERSE=1: PRINT A$: NORMAL=1: RETurn 
```

does the trick!

[![Final fix][4]][4]

Just the `CHR$(133)` needs fixing, really. Find a block character in the QL character set? Is there one?


## TODO

 - Add lowercase - DONE!
 - Only Liverpool printed in destination of embark - DONE!
 - Market prices all on one line - DONE!
 - Tidy ship status - DONE!
 - Tidy cargo - DONE!
 - Change or remove the column character (133)
 - Make 2 versions, scrolling, and full screen
 - how to full screen? `MODE`
 - How to PRINT AT?  `AT y,x:PRINT"HI"`

## Conclusion

The QL platform and SuperBASIC are just being kept alive by mugs who bought the machine (years ago). By rights, it should have been consigned to the waste bin, years ago.

Principle crimes:

 - Sir Clive ruined the QL with a hasty release, thus ensuring that the BASIC was poorly crafted. 
 - The use of the 68008, instead of a 68000 was clearly hamstringing itself from the outset, and...
 - The microdrives were just ridiculous.

Subsequent splits in the firmware have just made matters worse (regarding compatibility).



<!-- Images -->

  [1]: xtras/images/cargotest_output.png "Bad cargo display"
  [2]: xtras/images/cargotest_manual_spurious0_output.png "Spurious 0"
  [3]: xtras/images/cargotest_manual_output.png "OK manual"
  [4]: xtras/images/cargotest_final_fix_output.png "Final fix"
    
    
    
    
