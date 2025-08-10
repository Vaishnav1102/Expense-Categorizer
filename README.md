import tkinter as tk
from tkinter import ttk, messagebox
from datetime import datetime

expenses = []

CATEGORY_KEYWORDS = {
    "Food": ["coffee", "starbucks", "lunch", "mcdonald", "pizza", "food", "restaurant"],
    "Travel": ["uber", "taxi", "flight", "bus", "train", "ride", "travel"],
    "Bills": ["electricity", "internet", "gas", "bill", "water", "rent"]
}

def categorize_description(description):
    desc = description.lower()
    for category, keywords in CATEGORY_KEYWORDS.items():
        if any(keyword in desc for keyword in keywords):
            return category
    return "Other"

def add_expense():
    date = date_var.get()
    description = desc_var.get()
    amount = amount_var.get()

    if not (date and description and amount):
        messagebox.showwarning("Missing Info", "Please fill all fields.")
        return

    try:
        # Validate date and amount
        datetime.strptime(date, "%Y-%m-%d")
        amount = float(amount)

        category = categorize_description(description)

        expense = {
            "Date": date,
            "Description": description,
            "Amount": amount,
            "Category": category
        }

        expenses.append(expense)
        update_table()
        clear_fields()
    except ValueError:
        messagebox.showerror("Invalid Input", "Check date format (YYYY-MM-DD) and amount (number).")

def update_table():
    for row in tree.get_children():
        tree.delete(row)

    for exp in expenses:
        tree.insert("", "end", values=(exp["Date"], exp["Description"], f"{exp['Amount']:.2f}", exp["Category"]))

def clear_fields():
    date_var.set("")
    desc_var.set("")
    amount_var.set("")

root = tk.Tk()
root.title("Manual Expense Categorizer")
root.geometry("750x500")

input_frame = tk.LabelFrame(root, text="Add New Expense", padx=10, pady=10)
input_frame.pack(fill="x", padx=10, pady=10)

date_var = tk.StringVar()
desc_var = tk.StringVar()
amount_var = tk.StringVar()

tk.Label(input_frame, text="Date (YYYY-MM-DD)").grid(row=0, column=0, padx=5, pady=5)
tk.Entry(input_frame, textvariable=date_var).grid(row=0, column=1, padx=5)

tk.Label(input_frame, text="Description").grid(row=0, column=2, padx=5, pady=5)
tk.Entry(input_frame, textvariable=desc_var).grid(row=0, column=3, padx=5)

tk.Label(input_frame, text="Amount").grid(row=0, column=4, padx=5, pady=5)
tk.Entry(input_frame, textvariable=amount_var).grid(row=0, column=5, padx=5)

tk.Button(input_frame, text="Add Expense", command=add_expense).grid(row=0, column=6, padx=10)

# Table frame
table_frame = tk.Frame(root)
table_frame.pack(pady=10)

columns = ("Date", "Description", "Amount", "Category")
tree = ttk.Treeview(table_frame, columns=columns, show="headings", height=15)

for col in columns:
    tree.heading(col, text=col)
    tree.column(col, anchor="center", width=150 if col == "Description" else 100)

tree.pack()

root.mainloop()
