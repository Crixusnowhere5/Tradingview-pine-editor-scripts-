//@version=5
indicator("Ultimate Volume Strength", shorttitle="UVS", overlay=true)

// ===== MANUAL INPUTS =====
manual_shares = input.float(100000000.0, "Shares Outstanding", minval=1.0)
manual_avg_vol = input.float(1000000.0, "20-Day Avg Volume", minval=1.0)

// ===== CALCULATIONS =====
current_volume = volume
turnover_pct = (current_volume / manual_shares) * 100
vol_ratio = current_volume / manual_avg_vol
strength_score = (turnover_pct / 2.5) + (vol_ratio / 2)

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

// ===== VISUAL GRADE DISPLAY =====
// Background color
bgcolor(grade_color, 85)

// Grade label on each candle
var label grade_label = label.new(bar_index, na, "", color=grade_color, 
     textcolor=color.white, style=label.style_label_center, size=size.normal)

label.set_xy(grade_label, bar_index, high * 1.002)
label.set_text(grade_label, grade + " (" + str.tostring(math.round(strength_score * 100) / 100) + ")")
label.set_color(grade_label, grade_color)

// ===== DETAILED DATA TABLE =====
var table info_table = table.new(position.top_right, 2, 7, bgcolor=color.white, border_width=1)

if barstate.islast
    table.cell(info_table, 0, 0, "VOLUME STRENGTH", bgcolor=color.gray, text_color=color.white)
    table.cell(info_table, 1, 0, "GRADE: " + grade, bgcolor=grade_color, text_color=color.white)
    
    table.cell(info_table, 0, 1, "Turnover %")
    table.cell(info_table, 1, 1, str.tostring(math.round(turnover_pct * 100) / 100) + "%")
    
    table.cell(info_table, 0, 2, "Volume Ratio")
    table.cell(info_table, 1, 2, str.tostring(math.round(vol_ratio * 100) / 100) + "x")
    
    table.cell(info_table, 0, 3, "Strength Score")
    table.cell(info_table, 1, 3, str.tostring(math.round(strength_score * 100) / 100))
    
    table.cell(info_table, 0, 4, "Today's Volume")
    table.cell(info_table, 1, 4, str.tostring(current_volume))
    
    table.cell(info_table, 0, 5, "Manual Shares")
    table.cell(info_table, 1, 5, str.tostring(manual_shares))
    
    table.cell(info_table, 0, 6, "Avg Volume")
    table.cell(info_table, 1, 6, str.tostring(manual_avg_vol))

// ===== VOLUME BARS COLORED BY STRENGTH =====
plot(volume, "Volume", color=grade_color, style=plot.style_columns, histbase=0)
