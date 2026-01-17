# Predictive Allocation Model (PAM) – DAX

## Objective
Allocate resources, focus areas, or projected vote share
based on historical performance, current trends,
and demographic weightage.

---

## Business Problem
Campaign resources are limited and cannot be distributed
equally across all regions. Decision-makers need a
data-driven way to prioritize constituencies with
higher impact potential.

---

## Model Concept
PAM combines:
- Historical vote share
- Recent change in vote share
- Demographic influence (age, gender, caste)
- Seat competitiveness

Each factor is weighted to produce a priority score.

---

## Logic Overview
1. Calculate historical performance score
2. Calculate recent momentum (change in vote share)
3. Apply demographic weight factors
4. Generate final allocation / priority score

---

## Pseudo DAX Logic
```DAX
VAR _consider =  0.05

VAR _check = SELECTEDVALUE('Table'[AREA])
VAR _tbl =
    ADDCOLUMNS(
        SUMMARIZE(
            'Table',
            'Table'[RESPONSE]
        ),
        "RESCOUNT", CALCULATE(DISTINCTCOUNT('Table'[ID])),
        "TURNOUT_C", CALCULATE(MAX('Table'[TURNOUT])),
        "RATIO_MUS", CALCULATE(MAX('Table'[RATIO_M])),
        "RATIO_OTH", CALCULATE(MAX('Table'[RATIO_O])),
        "MUS_TVC", 
            CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"},
                REMOVEFILTERS('Table'[RESPONSE])
            ),
        "MUS_WOBO_VC",
            IF('Table'[RESPONSE] IN {"BJP","NDA"}, 
            ROUND(
            CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"}
            ) * _consider, 0)
            , 
            CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"}
            )
        ),
        "MUS_WOBO_TVC",
            CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"},
                FILTER('Table', NOT('Table'[RESPONSE] IN {"BJP","NDA"})),
                REMOVEFILTERS('Table'[RESPONSE])
        ),
        "MUS_WOBO_VC_NBO", 
            IF('Table'[RESPONSE] IN {"BJP","NDA"}, BLANK(), 
            CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"}
            )
        ),
        "MUS_BO_TVC",
        ROUND(
            CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"},
                FILTER('Table', 'Table'[RESPONSE] IN {"BJP","NDA"}),
                REMOVEFILTERS('Table'[RESPONSE])
        ) - 
        CALCULATE(
                DISTINCTCOUNT('Table'[ID]),
                'Table'[CASTE] IN {"M", "Muslim"},
                FILTER('Table', 'Table'[RESPONSE] IN {"BJP","NDA"}),
                REMOVEFILTERS('Table'[RESPONSE])
        ) * _consider
        , 0 )
        ,
        "OTH_TT_VS", 
            DIVIDE(
                CALCULATE(DISTINCTCOUNT('Table'[ID]), NOT('Table'[CASTE] IN {"M","Muslim"})),
                CALCULATE(DISTINCTCOUNT('Table'[ID]), NOT('Table'[CASTE] IN {"M","Muslim"}), REMOVEFILTERS('Table'[RESPONSE]))
            )
    )
VAR _withCalcs =
    ADDCOLUMNS(
        _tbl,
        "MUS_VS", DIVIDE([MUS_WOBO_VC_NBO], [MUS_WOBO_TVC]),
        "MUS_TT_VS", ([MUS_WOBO_VC] + ROUND([MUS_BO_TVC] * DIVIDE([MUS_WOBO_VC_NBO], [MUS_WOBO_TVC]), 0)) / [MUS_TVC],
        "MUS_PAM_FACTOR", [TURNOUT_C] * DIVIDE([RATIO_MUS], 100),
        "OTH_PAM_FACTOR", [TURNOUT_C] * DIVIDE([RATIO_OTH], 100)
    )
VAR _result =
    ADDCOLUMNS(
        _withCalcs,
       "MUS_RESULT", CONVERT(([MUS_TT_VS] * [MUS_PAM_FACTOR]), INTEGER),
       "OTH_RESULT", CONVERT([OTH_TT_VS] * [OTH_PAM_FACTOR], INTEGER),
       "END_RESULT", CONVERT(([MUS_TT_VS] * [MUS_PAM_FACTOR]), INTEGER) + CONVERT([OTH_TT_VS] * [OTH_PAM_FACTOR], INTEGER)
    )

VAR _top1Row = TOPN(1, _result, [END_RESULT], DESC)
VAR _winvc = MAXX(_top1Row, [END_RESULT])
VAR _top2Row = TOPN(1, FILTER(_result, [END_RESULT] <> _winvc), [END_RESULT], DESC)
VAR _runsvc = MAXX(_top2Row, [END_RESULT])

RETURN
IF( _check = BLANK(), BLANK(), MAXX(_top1Row, [RESPONSE]) & " " & FORMAT((_winvc - _runsvc) / SUMX(_result, [END_RESULT]), "0.00%") & " (" & MAXX(_top2Row, [RESPONSE]) & ")")
