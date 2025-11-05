import tkinter as tk

def calculate_strength():
    try:
        volume = float(volume_entry.get())
        shares = float(shares_entry.get())
        avg_volume = float(avg_volume_entry.get())
        
        turnover_pct = (volume / shares) * 100
        volume_ratio = volume / avg_volume
        strength_score = (turnover_pct / 2.5) + (volume_ratio / 2)
        
        if strength_score >= 3.0: grade, color = "A+", "green"
        elif strength_score >= 2.0: grade, color = "A", "green" 
        elif strength_score >= 1.5: grade, color = "B", "blue"
        elif strength_score >= 1.0: grade, color = "C", "orange"
        else: grade, color = "F", "red"
        
        result_label.config(text=f"Grade: {grade} | Score: {strength_score:.2f}", fg=color)
        details_label.config(text=f"Turnover: {turnover_pct:.4f}% | Ratio: {volume_ratio:.2f}x")
        
    except:
        result_label.config(text="Enter valid numbers!", fg="red")

# Create window
window = tk.Tk()
window.title("Volume Strength Calculator")
window.geometry("400x300")

# Input fields
tk.Label(window, text="Today's Volume:").pack()
volume_entry = tk.Entry(window)
volume_entry.pack()

tk.Label(window, text="Shares Outstanding:").pack()
shares_entry = tk.Entry(window)
shares_entry.pack()

tk.Label(window, text="20-Day Avg Volume:").pack()
avg_volume_entry = tk.Entry(window)
avg_volume_entry.pack()

# Calculate button
tk.Button(window, text="CALCULATE", command=calculate_strength, bg="lightblue").pack(pady=10)

# Results
result_label = tk.Label(window, text="", font=("Arial", 14))
result_label.pack()
details_label = tk.Label(window, text="")
details_label.pack()

# Start
window.mainloop()
