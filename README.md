# Taipan for QL

***Work in progress***

Not playable, just about starts up...

## Preamble

What a horrific port! I do not like entering programms into the QL at all. 

Funnily enough, I was tempted to purchase a QL for retro purposes, after having briefly owned one in the 80s (and clearly having very rose-tinted glasses), but after having ported this, I have no desire, whatesoever, to ever touch or see a QL ever again. No wonder the platform failed. Shockingly bad.

I am disappointed to say this, but I think that this QL port was even more horrific than the god-awful MMBASIC port.  It took hours to get the splash screen right and days to get the `READ` statements to work.

The emulator is pretty bad too. The "try me" dialog tends to lock up. Don't try to attach a drive before dismissing the "try me" dialog, it locks up.

### Main issues

 - Have to paste lines one or two at a time (no easy copy paste, nor text file input)
 - Problem getting line entered, whilst avoiding "bad line" errors
 - Problem then getting it to run
 - Have to slowly delete a line, one character at a time, for a "bad line" error. Can not just discard the line with a single keystroke
 - Microdrives are slow! It takes almost as long as the Hobbit (8 minutes), to load!
 - No ordering on `dir` output
 - No indication of where, in a multi-statement line, an error exists
 - Undeclared/undefined variables contain '*' instead of'0', causes many "error in expression" errors

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

Similarly, trying to enter `l=0` gives a "not implmented" error, where as `k=0` works as one would expect.















### Runtime bugs

#### Set `HOME`, `INVERSE` and `NORMAL` to be variables

Search and replace with a `=1` suffix.

`FLASH` is an accepted keyword and need to be either `FLASH 1` or `FLASH 0`.


#### Set `VTAB` and `HTAB` to be variables

Search and replace with a `=1` suffix.


#### No `PRINT TAB()`

Use `PRINT TO`

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

Side note: when the program crahes at this point, `PRINT I` correctly shows `0`, but `PRINT L` scrolls a series of `0` up the screen!!!

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
