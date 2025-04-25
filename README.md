# CS216_P10_Grades.py
# Hannah Shane
# 4/20/25
# Final project for CS216 - Reads CSV, summarizes grades, creates a pie chart, and writes summary

import tkinter as tk
from tkinter import filedialog, messagebox, Canvas

def classify_grade(score):
    if score >= 90:
        return 'A'
    elif score >= 80:
        return 'B'
    elif score >= 70:
        return 'C'
    elif score >= 60:
        return 'D'
    else:
        return 'F'

def process_data():
    # (1) read text or CSV file
    file_path = filedialog.askopenfilename(filetypes=[("CSV files", "*.csv")])
    if file_path == "":
        return

    scores = []
    try:
        f = open(file_path, 'r')
        for line in f:
            line = line.strip()
            if line.isdigit():
                scores.append(int(line))
        f.close()
    except:
        messagebox.showerror("Error", "Failed to read CSV file.")
        return

    if len(scores) == 0:
        messagebox.showerror("Error", "No valid scores found.")
        return

    # (2) summarize data
    a_count = 0
    b_count = 0
    c_count = 0
    d_count = 0
    f_count = 0

    for score in scores:
        grade = classify_grade(score)
        if grade == 'A':
            a_count = a_count + 1
        elif grade == 'B':
            b_count = b_count + 1
        elif grade == 'C':
            c_count = c_count + 1
        elif grade == 'D':
            d_count = d_count + 1
        elif grade == 'F':
            f_count = f_count + 1

    # (3) draw pie chart
    canvas.delete("all")
    total = a_count + b_count + c_count + d_count + f_count
    if total == 0:
        return

    a_extent = a_count / total * 360
    b_extent = b_count / total * 360
    c_extent = c_count / total * 360
    d_extent = d_count / total * 360
    f_extent = f_count / total * 360

    a_start = 270
    b_start = a_start + a_extent
    c_start = b_start + b_extent
    d_start = c_start + c_extent
    f_start = d_start + d_extent

    canvas.create_arc(50, 50, 250, 250, start=a_start, extent=a_extent, fill="red", outline="black")
    canvas.create_arc(50, 50, 250, 250, start=b_start, extent=b_extent, fill="blue", outline="black")
    canvas.create_arc(50, 50, 250, 250, start=c_start, extent=c_extent, fill="green", outline="black")
    canvas.create_arc(50, 50, 250, 250, start=d_start, extent=d_extent, fill="yellow", outline="black")
    canvas.create_arc(50, 50, 250, 250, start=f_start, extent=f_extent, fill="purple", outline="black")

    # (4) write summary to CSV file
    output_path = filedialog.asksaveasfilename(defaultextension=".csv", filetypes=[("CSV files", "*.csv")])
    if output_path == "":
        return
    try:
        f = open(output_path, 'w')
        f.write("Grade,Count\n")
        f.write("A," + str(a_count) + "\n")
        f.write("B," + str(b_count) + "\n")
        f.write("C," + str(c_count) + "\n")
        f.write("D," + str(d_count) + "\n")
        f.write("F," + str(f_count) + "\n")
        f.close()
        messagebox.showinfo("Success", "Summary saved to:\n" + output_path)
    except:
        messagebox.showerror("Error", "Could not write CSV file.")

# clear graph - called by File, New
def clear_graph():
    canvas.delete("all")

# exit program - called by File, Exit
def exit_program():
    main.destroy()

# main window setup
main = tk.Tk()
main.geometry('400x350')
main.title('P10 Project')

# create canvas
canvas = Canvas(main, width=300, height=300, bg='white')
canvas.pack()

# Create a menu bar
menu_bar = tk.Menu(main)

# Add "File" menu
file_menu = tk.Menu(menu_bar, tearoff=0)
file_menu.add_command(label="New", command=clear_graph)
file_menu.add_separator()
file_menu.add_command(label="Open", command=process_data)
file_menu.add_separator()
file_menu.add_command(label="Exit", command=exit_program)
menu_bar.add_cascade(label="File", menu=file_menu)

# Set the menu bar
main.config(menu=menu_bar)

# wait for menu selections
main.mainloop()

