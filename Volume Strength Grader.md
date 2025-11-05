//@version=5
indicator("Manual Volume Strength Grading", shorttitle="MVSG", overlay=true)

// ===== MANUAL INPUT SECTION =====
// You update these numbers periodically from Yahoo Finance or your broker
manual_shares_outstanding = input.float(100000000.0, "Manual Shares Outstanding", minval=1.0)
manual_avg_volume = input.float(1000000.0, "Manual 20-Day Avg Volume", minval=1.0)

// ===== AUTOMATIC CALCULATIONS =====
current_volume = volume
auto_volume_ratio = current_volume / ta.sma(volume, 20)

// ===== MANUAL TURNOVER CALCULATION =====
manual_turnover_pct = (current_volume / manual_shares_outstanding) * 100

// ===== STRENGTH SCORING =====
// Use manual data if available, otherwise fallback to automatic
turnover_to_use = manual_turnover_pct
volume_ratio_to_use = manual_avg_volume > 0 ? (current_volume / manual_avg_volume) : auto_volume_ratio

// Combined strength score (prioritizes turnover %)
strength_score = (turnover_to_use / 2.5) + (volume_ratio_to_use / 2)

// ===== GRADING SYSTEM =====
var string grade = "F"
var color grade_color = color.red

if strength_score >= 3.0
    grade := "A+"
    grade_color := color.lime
else if strength_score >= 2.0
    grade := "A" 
    grade_color := color.green
else if strength_score >= 1.5
    grade := "B"
    grade_color := color.blue
else if strength_score >= 1.0
    grade := "C"
    grade_color := color.orange
else
    grade := "F"
    grade_color := color.red

// ===== VISUAL DISPLAY =====
// Background color based on grade
bgcolor(grade_color, 85)

// Grade label on chart
var label grade_label = label.new(bar_index, high, grade, 
     color=grade_color, textcolor=color.white, style=label.style_label_center,
     yloc=yloc.abovebar)

label.set_xy(grade_label, bar_index, high * 1.002)
label.set_text(grade_label, grade + " (" + str.tostring(math.round(strength_score * 100) / 100) + ")")
label.set_color(grade_label, grade_color)

// Info table
var table info_table = table.new(position.top_right, 2, 7, 
     bgcolor=color.white, border_width=1)

if barstate.islast
    table.cell(info_table, 0, 0, "VOLUME STRENGTH", 
         bgcolor=color.gray, text_color=color.white)
    table.cell(info_table, 1, 0, "GRADE: " + grade, 
         bgcolor=grade_color, text_color=color.white)
    
    table.cell(info_table, 0, 1, "Turnover %")
    table.cell(info_table, 1, 1, str.tostring(math.round(turnover_to_use * 100) / 100) + "%")
    
    table.cell(info_table, 0, 2, "Volume Ratio")
    table.cell(info_table, 1, 2, str.tostring(math.round(volume_ratio_to_use * 100) / 100) + "x")
    
    table.cell(info_table, 0, 3, "Strength Score")
    table.cell(info_table, 1, 3, str.tostring(math.round(strength_score * 100) / 100))
    
    table.cell(info_table, 0, 4, "Today's Volume")
    table.cell(info_table, 1, 4, str.tostring(current_volume))
    
    table.cell(info_table, 0, 5, "Manual Shares")
    table.cell(info_table, 1, 5, str.tostring(manual_shares_outstanding))

// Plot volume bars with strength coloring
plot(volume, "Volume", color=grade_color, style=plot.style_columns)
