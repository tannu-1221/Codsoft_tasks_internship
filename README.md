
import tkinter as tk

# Function to update the display
def click_button(item):
    current = str(entry_var.get())
    entry_var.set(current + str(item))

# Function to clear the display
def clear_display():
    entry_var.set("")

# Function to evaluate the expression
def calculate():
    try:
        expression = entry_var.get()
        result = eval(expression)
        entry_var.set(str(result))
    except ZeroDivisionError:
        entry_var.set("Cannot divide by 0")
    except:
        entry_var.set("Error")

# Create main window
root = tk.Tk()
root.title("Elegant Calculator")
root.geometry("320x450")
root.config(bg="#f7f7f7")

# Display variable
entry_var = tk.StringVar()

# Display field
entry = tk.Entry(
    root,
    textvariable=entry_var,
    font=("Arial", 20),
    bd=0,
    bg="#ffffff",
    fg="#333333",
    justify="right",
    relief="flat",
)
entry.pack(fill="both", ipadx=8, ipady=15, pady=(15, 10), padx=15)

# Frame for buttons
frame = tk.Frame(root, bg="#f7f7f7")
frame.pack()

# Button style helper
def create_button(text, row, col, bg="#e8e8e8", fg="#333333", cmd=None):
    btn = tk.Button(
        frame,
        text=text,
        width=5,
        height=2,
        font=("Arial", 14, "bold"),
        bg=bg,
        fg=fg,
        relief="flat",
        activebackground="#dcdcdc",
        activeforeground="#000000",
        command=cmd
    )
    btn.grid(row=row, column=col, padx=5, pady=5, sticky="nsew")
    return btn

# Button layout
buttons = [
    ('7', 1, 0), ('8', 1, 1), ('9', 1, 2), ('/', 1, 3, "#ffb3b3"),
    ('4', 2, 0), ('5', 2, 1), ('6', 2, 2), ('*', 2, 3, "#ffcc99"),
    ('1', 3, 0), ('2', 3, 1), ('3', 3, 2), ('-', 3, 3, "#ffd699"),
    ('0', 4, 0), ('.', 4, 1), ('+', 4, 2, "#ffff99"), ('=', 4, 3, "#b3ffb3"),
]

# Create buttons
for btn in buttons:
    if len(btn) == 4:
        text, row, col, color = btn
        cmd = calculate if text == "=" else lambda t=text: click_button(t)
        create_button(text, row, col, bg=color, cmd=cmd)
    else:
        text, row, col = btn
        cmd = calculate if text == "=" else lambda t=text: click_button(t)
        create_button(text, row, col, cmd=cmd)

# Clear button
clear_btn = tk.Button(
    root,
    text="Clear",
    font=("Arial", 14, "bold"),
    bg="#ff9999",
    fg="white",
    relief="flat",
    activebackground="#ff8080",
    activeforeground="white",
    command=clear_display
)
clear_btn.pack(fill="x", padx=15, pady=10)

# Make columns expand evenly
for i in range(4):
    frame.grid_columnconfigure(i, weight=1)

root.mainloop()












import tkinter as tk
from tkinter import messagebox
import random
import string

# ---------------- FUNCTIONS ----------------
def add_length(num):
    """Append a number to the password length."""
    current = length_var.get()
    length_var.set(current + str(num))

def clear_length():
    """Clear the length entry."""
    length_var.set("")

def set_complexity(level):
    """Set password complexity."""
    complexity_var.set(level)
    for btn in complexity_buttons:
        btn.config(bg="#d9d9d9", fg="black")  # reset all buttons
    complexity_map[level].config(bg="#4CAF50", fg="white")  # highlight selected

def generate_password():
    """Generate a random password."""
    try:
        length = int(length_var.get())
        if length <= 0:
            messagebox.showerror("Error", "Password length must be greater than 0")
            return

        if complexity_var.get() == "":
            messagebox.showerror("Error", "Please select complexity level")
            return

        if complexity_var.get() == "Letters":
            chars = string.ascii_letters
        elif complexity_var.get() == "Letters+Numbers":
            chars = string.ascii_letters + string.digits
        elif complexity_var.get() == "Strong":
            chars = string.ascii_letters + string.digits + string.punctuation

        password = "".join(random.choice(chars) for _ in range(length))
        password_var.set(password)

    except ValueError:
        messagebox.showerror("Error", "Please enter a valid number for length")

def copy_to_clipboard():
    root.clipboard_clear()
    root.clipboard_append(password_var.get())
    root.update()
    messagebox.showinfo("Copied", "Password copied to clipboard!")

# ---------------- UI SETUP ----------------
root = tk.Tk()
root.title("Keypad Password Generator")
root.geometry("350x500")
root.config(bg="#f7f7f7")

# Title
tk.Label(root, text="Password Generator", font=("Arial", 18, "bold"), bg="#f7f7f7").pack(pady=10)

# Length entry
length_var = tk.StringVar()
tk.Entry(root, textvariable=length_var, font=("Arial", 20), justify="center", relief="flat", bg="white").pack(pady=5, padx=20, fill="x")

# Number keypad frame
keypad_frame = tk.Frame(root, bg="#f7f7f7")
keypad_frame.pack(pady=5)

# Number keypad layout
nums = [
    (1, 0, 0), (2, 0, 1), (3, 0, 2),
    (4, 1, 0), (5, 1, 1), (6, 1, 2),
    (7, 2, 0), (8, 2, 1), (9, 2, 2),
    ("C", 3, 0), (0, 3, 1), ("OK", 3, 2)
]

for (text, row, col) in nums:
    if text == "C":
        cmd = clear_length
        bg = "#ff9999"
    elif text == "OK":
        cmd = generate_password
        bg = "#4CAF50"
    else:
        cmd = lambda t=text: add_length(t)
        bg = "#e8e8e8"

    tk.Button(keypad_frame, text=text, width=6, height=2, font=("Arial", 12, "bold"),
              bg=bg, fg="black" if text not in ("C", "OK") else "white",
              relief="flat", command=cmd).grid(row=row, column=col, padx=3, pady=3)

# Complexity buttons
tk.Label(root, text="Select Complexity:", font=("Arial", 12, "bold"), bg="#f7f7f7").pack(pady=5)
complexity_var = tk.StringVar(value="")
complexity_buttons = []
complexity_map = {}

complexities = [("Letters", "#d9d9d9"), ("Letters+Numbers", "#d9d9d9"), ("Strong", "#d9d9d9")]
complexity_frame = tk.Frame(root, bg="#f7f7f7")
complexity_frame.pack()

for i, (level, color) in enumerate(complexities):
    btn = tk.Button(complexity_frame, text=level, width=14, height=2, font=("Arial", 11, "bold"),
                    bg=color, relief="flat", command=lambda l=level: set_complexity(l))
    btn.grid(row=0, column=i, padx=5, pady=5)
    complexity_buttons.append(btn)
    complexity_map[level] = btn

# Password display
password_var = tk.StringVar()
tk.Entry(root, textvariable=password_var, font=("Arial", 14), justify="center", relief="flat", bg="white").pack(pady=10, padx=20, fill="x")

# Copy button
tk.Button(root, text="Copy to Clipboard", font=("Arial", 12, "bold"), bg="#2196F3", fg="white", relief="flat",
          command=copy_to_clipboard).pack(pady=5)

root.mainloop()






import tkinter as tk
import random

# Game Variables
user_score = 0
computer_score = 0
choices = ["Rock", "Paper", "Scissors"]

# Symbols (black & white + colored)
symbols_bw = {"Rock": "⚪", "Paper": "⬜", "Scissors": "✂️"}  # Monochrome style
symbols_color = {"Rock": "🪨", "Paper": "📄", "Scissors": "✂️"}  # Colored style

# Countdown animation before showing result
def countdown_animation(choice, step=0):
    sequence = ["⚪", "⬜", "✂️"]  # B/W sequence
    if step < len(sequence) * 2:  # repeat twice
        user_symbol_label.config(text=sequence[step % 3], fg="black")
        comp_symbol_label.config(text=sequence[(step + 1) % 3], fg="black")
        root.after(250, countdown_animation, choice, step + 1)
    else:
        show_result(choice)

# Show final result
def show_result(choice):
    global user_score, computer_score
    computer_choice = random.choice(choices)

    # Display colored symbols
    user_symbol_label.config(text=symbols_color[choice], fg="black")
    comp_symbol_label.config(text=symbols_color[computer_choice], fg="black")

    # Determine winner
    if choice == computer_choice:
        result = "It's a Tie!"
    elif (choice == "Rock" and computer_choice == "Scissors") or \
         (choice == "Scissors" and computer_choice == "Paper") or \
         (choice == "Paper" and computer_choice == "Rock"):
        result = "You Win!"
        user_score += 1
        user_symbol_label.config(fg="green")  # highlight winner
        comp_symbol_label.config(fg="red")
    else:
        result = "You Lose!"
        computer_score += 1
        user_symbol_label.config(fg="red")
        comp_symbol_label.config(fg="green")

    result_var.set(result)
    score_var.set(f"{user_score}  -  {computer_score}")

# Start game with animation
def play(choice):
    result_var.set("")
    # Set initial black & white look
    user_symbol_label.config(fg="black")
    comp_symbol_label.config(fg="black")
    countdown_animation(choice)

# Function to reset game
def reset_game():
    global user_score, computer_score
    user_score = 0
    computer_score = 0
    user_symbol_label.config(text="❔", fg="black")
    comp_symbol_label.config(text="❔", fg="black")
    result_var.set("")
    score_var.set("0  -  0")

# Main Window
root = tk.Tk()
root.title("Rock Paper Scissors")
root.geometry("420x550")
root.config(bg="#f7f7f7")

# Title
tk.Label(root, text="Rock Paper Scissors", font=("Arial", 20, "bold"), bg="#f7f7f7").pack(pady=5)

# Symbols (big display)
symbols_frame = tk.Frame(root, bg="#f7f7f7")
symbols_frame.pack(pady=5)

user_symbol_label = tk.Label(symbols_frame, text="❔", font=("Arial", 60), bg="#f7f7f7")
user_symbol_label.grid(row=0, column=0, padx=30)

tk.Label(symbols_frame, text="VS", font=("Arial", 18, "bold"), bg="#f7f7f7").grid(row=0, column=1)

comp_symbol_label = tk.Label(symbols_frame, text="❔", font=("Arial", 60), bg="#f7f7f7")
comp_symbol_label.grid(row=0, column=2, padx=30)

# Result Label
result_var = tk.StringVar(value="")
tk.Label(root, textvariable=result_var, font=("Arial", 16, "bold"), bg="#f7f7f7").pack(pady=5)

# Scoreboard
tk.Label(root, text="Score", font=("Comic Sans MS", 16, "bold"), bg="#f7f7f7").pack()
score_var = tk.StringVar(value="0  -  0")
tk.Label(root, textvariable=score_var, font=("Impact", 28, "bold"), fg="#333", bg="#f7f7f7").pack(pady=5)

# Keypad Frame
frame = tk.Frame(root, bg="#f7f7f7")
frame.pack(pady=10)

# Buttons with symbols
buttons = [
    ("Rock", "🪨", "#ff9999"),
    ("Paper", "📄", "#99ccff"),
    ("Scissors", "✂️", "#99ff99")
]

for i, (text, emoji, color) in enumerate(buttons):
    tk.Button(
        frame, text=f"{emoji}\n{text}", width=10, height=3, font=("Arial", 12, "bold"),
        bg=color, relief="flat", command=lambda t=text: play(t)
    ).grid(row=0, column=i, padx=5, pady=5)

# Reset Button
tk.Button(
    root, text="Play Again", font=("Arial", 12, "bold"), bg="#4CAF50", fg="white", relief="flat",
    command=reset_game
).pack(pady=10)

root.mainloop()
