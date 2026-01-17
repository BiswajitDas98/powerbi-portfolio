# Age & Gender Normalization (DAX)

## Objective
Normalize survey responses across age and gender groups
so that overall contribution equals 100% even when
some demographic groups are missing.

---

## Business Problem
In survey data, certain age or gender groups may be absent
for specific regions or time periods. This leads to
skewed percentages and incorrect comparisons.

Example issue:
- 4 age groups expected
- 1 group missing
- Total contribution becomes 83% instead of 100%

---

## Logic Overview
1. Identify available age and gender groups
2. Calculate raw share for each group
3. Re-scale values proportionally to maintain 100% total
4. Preserve relative distribution across groups

---

## Pseudo DAX Logic
```DAX
#N_ANG_SUMMERY_BC = 
VAR _max =
    CALCULATE (
        MAX ( 'Table'[RES_AC_BC]),
        FILTER ( 'Table', 'Table'[ID] = EARLIER('Table'[ID]))
    )
---------------------
VAR _TableAC =
    FILTER ( 'Table', 'Table'[ID] = EARLIER ( 'Table'[ID] ) )

VAR _max2 =
    MAXX ( _TableAC, 'Table'[RES_AC_BC] )
-----------------------------
VAR _win = 
    CALCULATE (
        MAX ('Table'[PARTY_BC] ),
        FILTER (
            'Table',
            'Table'[ID] = EARLIER ( 'Table'[ID] )
                && 'Table'[RES_AC_BC] = _max
        )
    )
VAR _2nd = 
    CALCULATE (
        MAX ( 'Table'[RES_AC_BC]),
        FILTER ( 'Table', 'Table'[ID] = EARLIER('Table'[ID]) &&
        'Table'[RES_AC_BC] <> _max)
    )
VAR _runs = 
    CALCULATE (
        MAX ('Table'[PARTY_BC] ),
        FILTER (
            'Table',
            'Table'[ID] = EARLIER ( 'Table'[ID] )
                && 'Table'[RES_AC_BC] = _2nd
        )
    )
------
VAR _check = 'Table'[RES_AC_BC]
VAR _get = 'Table'[BC_SHARE]
VAR _status = IF(_check > _get, 1,0)
--------
VAR _mar = ROUND((_max - _2nd)*100,2)
RETURN
IF(_win = BLANK(), BLANK(), _win & " " & _mar & "% (" & _runs & ")")
