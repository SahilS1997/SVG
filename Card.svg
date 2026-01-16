SVG_Dynamic_Card = 
-- =========================================================
--  1. USER CONFIGURATION (Bind your measures here)
-- =========================================================

-- The main big number (e.g., 16,257)
VAR _MainMeasure = [Total Engagements] 

-- The denominator for the red pill % (e.g., 62,745)
VAR _TotalUniverseMeasure = [Total All Engagements]

-- The text for the pill
VAR _PillLabel = "With DP Risk"

-- The comparison text below the big number
VAR _ComparisonLabel = "Without DP Risk"
VAR _ComparisonMeasure = [Engagements Without Risk]

-- ---------------------------------------------------------
--  2. CHART DATA CONFIGURATION
-- ---------------------------------------------------------
-- This table grabs the last 12 months (or whatever is selected) dynamically.
-- Ensure you use your actual 'Date' table columns here.
VAR _ChartTable = 
    ADDCOLUMNS(
        SUMMARIZE(
            'Date', 
            'Date'[MonthSort], -- Used for sorting 
            'Date'[MonthShort] -- Used for labels (e.g., "Jan", "Feb")
        ),
        "Value", [Total Engagements] -- The measure you want to graph
    )

-- =========================================================
--  3. CALCULATION LOGIC (Do not edit below unless customizing layout)
-- =========================================================
VAR _Width = 400
VAR _Height = 220

-- Calculate the Red Pill Percentage
VAR _RiskPercent = DIVIDE(_MainMeasure, _TotalUniverseMeasure)
VAR _RiskPercentFormatted = FORMAT(_RiskPercent, "0.0%")

-- Format the text values
VAR _MainValueFormatted = FORMAT(_MainMeasure, "#,##0")
VAR _TotalUniverseFormatted = FORMAT(_TotalUniverseMeasure, "#,##0")
VAR _ComparisonValueFormatted = FORMAT(_ComparisonMeasure, "#,##0")

-- Get Chart Scaling Factors
VAR _MaxChartValue = MAXX(_ChartTable, [Value])
VAR _ChartHeight = 60
VAR _BarWidth = 20
VAR _Gap = 10
VAR _ChartLeftMargin = 20
VAR _ChartTopMargin = 130

-- =========================================================
--  4. GENERATE SPARKLINE SVG
-- =========================================================
VAR _Sparkline = 
    CONCATENATEX(
        _ChartTable,
        VAR _BarHeight = IF(_MaxChartValue = 0, 0, ([Value] / _MaxChartValue) * _ChartHeight)
        VAR _YPos = _ChartTopMargin + (_ChartHeight - _BarHeight)
        -- Uses RANKX to space bars evenly if specific month IDs aren't sequential, 
        -- or assumes standard table iteration.
        VAR _XPos = _ChartLeftMargin + (RANKX(_ChartTable, 'Date'[MonthSort], , ASC) - 1) * (_BarWidth + _Gap)
        
        RETURN 
        -- The Bar
        "<rect x='" & _XPos & "' y='" & _YPos & "' width='" & _BarWidth & "' height='" & _BarHeight & "' fill='#1A4D54' />" &
        -- The Label
        "<text x='" & (_XPos + _BarWidth/2) & "' y='" & (_ChartTopMargin + _ChartHeight + 15) & "' text-anchor='middle' font-family='Segoe UI' font-size='12' fill='#666666'>" & 'Date'[MonthShort] & "</text>",
        "",
        'Date'[MonthSort], ASC -- This ensures the SVG draws in the correct month order
    )

-- =========================================================
--  5. ASSEMBLE FINAL SVG
-- =========================================================
VAR _SVG = 
"data:image/svg+xml;utf8, " &
"<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 " & _Width & " " & _Height & "'>
    
    <text x='20' y='35' font-family='Segoe UI' font-size='18' fill='#333333'>DP Risk Engagements</text>

    <rect x='220' y='15' width='180' height='26' rx='13' ry='13' fill='#FADDD9' stroke='#C0392B' stroke-width='1'/>
    <text x='310' y='33' text-anchor='middle' font-family='Segoe UI' font-weight='bold' font-size='14' fill='#C0392B'>" & _PillLabel & ": " & _RiskPercentFormatted & "</text>

    <text x='20' y='85' font-family='Segoe UI' font-weight='bold' font-size='40' fill='black'>" & _MainValueFormatted & "</text>

    <text x='20' y='110' font-family='Segoe UI' font-size='14' fill='#333333'>
        Overall: " & _TotalUniverseFormatted & " | <tspan fill='#A63434'>" & _ComparisonLabel & ": " & _ComparisonValueFormatted & "</tspan>
    </text>

    <line x1='20' y1='" & (_ChartTopMargin + _ChartHeight/1.5) & "' x2='" & (_Width - 20) & "' y2='" & (_ChartTopMargin + _ChartHeight/1.5) & "' stroke='#666666' stroke-width='1' stroke-dasharray='5,5' opacity='0.5'/>

    " & _Sparkline & "

</svg>"

RETURN 
    _SVG
