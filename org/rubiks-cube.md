# rubik's cube notes

## general

! denotes counterclockwise

r! d! r d (x6) – return cube to original state

## create base cross

f! u l! u! – flip over a cross piece (with center color of interest at top)

## complete face

r! d! r d – move corner directly underneath (not flipped) up (xN, until in correct orientation)

## complete second layer

flip cube over so that solved side is down

look for cross piece that does not have center color

move with one of two algorithms depending on which direction (right or left) the piece needs to go: u! l! u l u f u! f! (left version) u r u! r! u! f! u f (right version)

## complete cross on opposite side

now on top (unsolved side) will have either (1) single blue center (only considering cross pieces), (2) L-shape, (3) line, or (4) cross. (goal is to get to cross)

if starting with L-shape: hold with L-shape in upper left corner, then f r u r! u! f! – should give line hold with line across top from left to right and repeat above – should give cross

## align cross pieces

rotate to get at least two cross pieces aligned with side centers. will be (1) either directly across from each other or (2) beside each other

if (1): align cube with one in front and one in back, then r u r! u r u u r! – should now have case (2)

if (2): align cube with one matching cross piece directly opposite from you and one in right hand, then r u r! u r u u r! – should line up all cross pieces

## get corner pieces in correct places

need to get corners in right place (but not necessarily flipped correctly). look for one that is already in correct location. hold cube with this corner at bottom right of the top face, then u r u! l! u r! u! l may have to do this more than once to get all corner pieces in correct places

## final corner piece orientation

now hold with any of the unsolved corners at the bottom right of the top face, then r! d! r d – (xN) repeat until bottom right corner of top face is correctly oriented now rotate top layer until another unsolved corner is at the bottom right corner of top face, then r! d! r d – (xN) repeat until bottom right corner of top face is correctly oriented (repeat until cube is solved)
