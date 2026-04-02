import tkinter as tk
from tkinter import messagebox
import random
import copy
root = tk.Tk()
root.title("Sudoku Game")
root.configure(bg="#2c3e50")
cells = [[None for _ in range(9)] for _ in range(9)]
board = [[0]*9 for _ in range(9)]
solution = [[0]*9 for _ in range(9)]
def only_numbers(char):
    return char.isdigit() and 1 <= int(char) <= 9
vcmd = (root.register(only_numbers), '%S')
def is_valid(b, row, col, num):
    for i in range(9):
        if b[row][i] == num or b[i][col] == num:
            return False

    sr, sc = row - row % 3, col - col % 3
    for i in range(3):
        for j in range(3):
            if b[sr+i][sc+j] == num:
                return False
    return True


def solve(b):
    for i in range(9):
        for j in range(9):
            if b[i][j] == 0:
                nums = list(range(1, 10))
                random.shuffle(nums)
                for num in nums:
                    if is_valid(b, i, j, num):
                        b[i][j] = num
                        if solve(b):
                            return True
                        b[i][j] = 0
                return False
    return True
def generate_board():
    global board, solution
    board = [[0]*9 for _ in range(9)]
    solve(board)
    solution = copy.deepcopy(board)

    for _ in range(45):
        i, j = random.randint(0, 8), random.randint(0, 8)
        board[i][j] = 0

    update_gui()
def update_gui():
    for i in range(9):
        for j in range(9):
            e = cells[i][j]
            e.config(state='normal')
            e.delete(0, tk.END)

            if board[i][j] != 0:
                e.insert(0, str(board[i][j]))
                e.config(state='disabled', disabledbackground="#bdc3c7")
def check_solution():
    for i in range(9):
        for j in range(9):
            val = cells[i][j].get()
            if val == "" or int(val) != solution[i][j]:
                messagebox.showerror("Wrong", "❌ Incorrect solution!")
                return
    messagebox.showinfo("Success", "🎉 You solved it!")

def solve_board():
    for i in range(9):
        for j in range(9):
            cells[i][j].config(state='normal')
            cells[i][j].delete(0, tk.END)
            cells[i][j].insert(0, str(solution[i][j]))

def clear_board():
    for i in range(9):
        for j in range(9):
            if cells[i][j]['state'] == 'normal':
                cells[i][j].delete(0, tk.END)
def create_grid():
    frame = tk.Frame(root, bg="#2c3e50")
    frame.grid(row=0, column=0, padx=10, pady=10)

    for i in range(9):
        for j in range(9):
            bg_color = "#ecf0f1" if (i//3 + j//3) % 2 == 0 else "#dfe6e9"

            e = tk.Entry(frame, width=2, font=('Arial', 20, 'bold'),
                         justify='center',
                         bg=bg_color,
                         validate="key",
                         validatecommand=vcmd)

            e.grid(row=i, column=j, ipadx=8, ipady=8, padx=1, pady=1)
            cells[i][j] = e
btn_frame = tk.Frame(root, bg="#2c3e50")
btn_frame.grid(row=1, column=0, pady=10)

def styled_button(text, cmd, color):
    return tk.Button(btn_frame, text=text, command=cmd,
                     font=('Arial', 12, 'bold'),
                     bg=color, fg="white",
                     width=10, relief="flat")

styled_button("Generate", generate_board, "#27ae60").grid(row=0, column=0, padx=5)
styled_button("Check", check_solution, "#2980b9").grid(row=0, column=1, padx=5)
styled_button("Solve", solve_board, "#8e44ad").grid(row=0, column=2, padx=5)
styled_button("Clear", clear_board, "#c0392b").grid(row=0, column=3, padx=5)
create_grid()
root.mainloop()
