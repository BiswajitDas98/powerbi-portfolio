# Change in Vote Share (DAX)

## Objective
Measure the change in vote share between two entities
to track campaign momentum and performance shifts.

---

## Business Problem
Vote share at a single point in time does not show trend.
Campaign teams need to know whether support is increasing
or decreasing compared to a previous period.

---

## Logic Overview
1. Calculate vote share for the current period
2. Calculate vote share for the previous period
3. Subtract previous vote share from current vote share

---

## Pseudo DAX Logic
```DAX
VAR _subval = SELECTEDVALUE('DECREASE_IN_VS_VALUES'[VALUES])
VAR _check = SELECTEDVALUE('GENDER'[GENDER])
VAR _tbl =
    ADDCOLUMNS(
        SUMMARIZE(
            'Table',
            'Table'[RESPONSE]
        ),
        "RESCOUNT", CALCULATE(DISTINCTCOUNT('Table'[ID])),
        "T_RES", CALCULATE(DISTINCTCOUNT('Table'[ID]), REMOVEFILTERS('Table'[RESPONSE])),
        "M_RES", CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"M"}),
        "M_VS", DIVIDE(CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"M"}), CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"M"}, REMOVEFILTERS('Table'[RESPONSE]))),
        "F_RES", CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"F"}),
        "F_VS", DIVIDE(CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"F"}), CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"F"}, REMOVEFILTERS('Table'[RESPONSE]))),
        "M_T", ROUND(CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"M"}, REMOVEFILTERS('Table'[RESPONSE])), 0),
        "F_T", ROUND(CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"F"}, REMOVEFILTERS('Table'[RESPONSE])), 0),
        "AD_M_T", CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"M"}, REMOVEFILTERS('Table'[RESPONSE])) - ROUND(CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"M"}, REMOVEFILTERS('Table'[RESPONSE])) * _subval, 0),
        "AD_F_T", CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"F"}, REMOVEFILTERS('Table'[RESPONSE])) - ROUND(CALCULATE(DISTINCTCOUNT('Table'[ID]), 'Table'[GENDER_CODE] IN {"F"}, REMOVEFILTERS('Table'[RESPONSE])) * _subval, 0)
    )

VAR _withCalcs =
    ADDCOLUMNS(
        _tbl,
        "AD_M_VC", [AD_M_T] * [M_VS],
        "AD_F_VC", [AD_F_T] * [F_VS],
        "AD_T_VC_M", ROUND(([AD_M_T] * [M_VS]) + [F_RES], 0),
        "AD_T_VS_M", ROUND(([AD_M_T] * [M_VS]) + [F_RES], 0) / ([AD_M_T] + [F_T]),
        "AD_T_VC_F", ROUND([M_RES] + ([AD_F_T] * [F_VS]), 0),
        "AD_T_VS_F", ROUND([M_RES] + ([AD_F_T] * [F_VS]), 0) / ([M_T] + [AD_F_T])
    )

VAR _top1RowM = TOPN(1, _withCalcs, [AD_T_VC_M], DESC)
VAR _winvsM = MAXX(_top1RowM, [AD_T_VC_M])
VAR _top2RowM = TOPN(1, FILTER(_withCalcs, [AD_T_VC_M] <> _winvsM), [AD_T_VC_M], DESC)
VAR _runsvsM = MAXX(_top2RowM, [AD_T_VC_M])

VAR _top1RowF = TOPN(1, _withCalcs, [AD_T_VC_F], DESC)
VAR _winvsF = MAXX(_top1RowF, [AD_T_VC_F])
VAR _top2RowF = TOPN(1, FILTER(_withCalcs, [AD_T_VC_F] <> _winvsF), [AD_T_VC_F], DESC)
VAR _runsvsF = MAXX(_top2RowF, [AD_T_VC_F])

RETURN
SWITCH(TRUE(),
_check = BLANK(), [@I_D_PERCEN_DECR_BC],
_check IN {"MALE"}, IF(_winvsM = BLANK(), BLANK(), MAXX(_top1RowM, [RESPONSE]) & " " & FORMAT((_winvsM - _runsvsM) / SUMX(_withCalcs, [AD_T_VC_M]), "0.00%") & " (" & MAXX(_top2RowM, [RESPONSE]) & ")"),
_check IN {"FEMALE"}, IF(_winvsF = BLANK(), BLANK(), MAXX(_top1RowF, [RESPONSE]) & " " & FORMAT((_winvsF - _runsvsF) / SUMX(_withCalcs, [AD_T_VC_F]), "0.00%") & " (" & MAXX(_top2RowF, [RESPONSE]) & ")")
)
