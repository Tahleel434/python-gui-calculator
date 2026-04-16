# python-gui-calculator
A feature-rich desktop calculator built in Python with a modern UI, keyboard support, and interactive design using Tkinter.

CODE:

import tkinter as tk
import math

# ---------------- LOGIC ---------------- #
class Engine:
    def __init__(self):
        self.expr = ""

    def set(self, val):
        self.expr = val

    def clear(self):
        self.expr = ""
        return ""

    def delete(self):
        self.expr = self.expr[:-1]
        return self.expr

    def calculate(self):
        try:
            # Safe eval (basic protection)
            result = str(eval(self.expr, {"__builtins__": None}, {}))
            self.expr = result
            return result
        except:
            return "Error"


# ---------------- UI ---------------- #
class UltraCalculator:
    def __init__(self, root):
        self.root = root
        self.root.title("Ultra Calculator")
        self.root.geometry("380x600")
        self.root.configure(bg="#020617")

        self.engine = Engine()

        self.container = tk.Frame(root, bg="#020617")
        self.container.pack(fill="both", expand=True, padx=10, pady=10)

        self.display = tk.Entry(
            self.container,
            font=("Segoe UI", 24, "bold"),
            bg="#020617",
            fg="#38bdf8",
            bd=0,
            justify="right",
            insertbackground="#38bdf8"
        )
        self.display.pack(fill="both", pady=20, ipady=20)

        tk.Frame(self.container, height=2, bg="#38bdf8").pack(fill="x", pady=5)

        self.create_buttons()

        # Keyboard support
        self.root.bind("<Key>", self.key_input)

    # ---------------- BUTTON ---------------- #
    def button(self, parent, text):
        frame = tk.Frame(parent, bg="#020617")
        frame.pack(side="left", expand=True, fill="both", padx=6, pady=6)

        btn = tk.Label(
            frame,
            text=text,
            font=("Segoe UI", 13, "bold"),
            bg="#0f172a",
            fg="white",
            padx=10,
            pady=18
        )
        btn.pack(expand=True, fill="both")

        def enter(e):
            btn.config(bg="#1e293b", fg="#38bdf8")

        def leave(e):
            btn.config(bg="#0f172a", fg="white")

        def press(e):
            btn.config(bg="#334155")

        def release(e):
            btn.config(bg="#1e293b")
            self.click(text)

        btn.bind("<Enter>", enter)
        btn.bind("<Leave>", leave)
        btn.bind("<Button-1>", press)
        btn.bind("<ButtonRelease-1>", release)

    # ---------------- LAYOUT ---------------- #
    def create_buttons(self):
        layout = [
            ["7","8","9","/"],
            ["4","5","6","*"],
            ["1","2","3","-"],
            ["0",".","=","+"],
            ["C","⌫","√","x²"]
        ]

        for row in layout:
            r = tk.Frame(self.container, bg="#020617")
            r.pack(expand=True, fill="both")
            for item in row:
                self.button(r, item)

    # ---------------- INPUT HANDLER ---------------- #
    def key_input(self, event):
        char = event.char
        if char in "0123456789+-*/.":
            self.display.insert(tk.END, char)
        elif event.keysym == "Return":
            self.click("=")
        elif event.keysym == "BackSpace":
            self.click("⌫")

    # ---------------- ACTION ---------------- #
    def click(self, val):
        current = self.display.get()

        if val == "C":
            self.display.delete(0, tk.END)
            self.engine.clear()

        elif val == "⌫":
            self.display.delete(0, tk.END)
            self.display.insert(0, current[:-1])
            self.engine.set(current[:-1])

        elif val == "=":
            self.engine.set(current)
            result = self.engine.calculate()
            self.display.delete(0, tk.END)
            self.display.insert(0, result)

        elif val == "√":
            try:
                result = str(math.sqrt(float(current)))
                self.display.delete(0, tk.END)
                self.display.insert(0, result)
                self.engine.set(result)
            except:
                self.display.delete(0, tk.END)
                self.display.insert(0, "Error")

        elif val == "x²":
            self.display.insert(tk.END, "**2")

        else:
            self.display.insert(tk.END, val)

# ---------------- RUN ---------------- #
if __name__ == "__main__":
    root = tk.Tk()
    app = UltraCalculator(root)
    root.mainloop()
