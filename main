import tkinter as tk
from tkinter import messagebox
import pyautogui
import pygetwindow as gw
import threading
import time
import keyboard


class AutoClicker:
    def __init__(self, root):
        self.root = root
        self.root.title("Auto Clicker")

        self.click_type = tk.StringVar(value="left")
        self.click_count = tk.IntVar(value=10)
        self.window_title = tk.StringVar()
        self.is_running = False

        self.create_widgets()

    def create_widgets(self):
        tk.Label(self.root, text="Click Type:").grid(row=0, column=0, padx=10, pady=10)
        tk.Radiobutton(self.root, text="Left", variable=self.click_type, value="left").grid(row=0, column=1)
        tk.Radiobutton(self.root, text="Right", variable=self.click_type, value="right").grid(row=0, column=2)

        tk.Label(self.root, text="Number of Clicks:").grid(row=1, column=0, padx=10, pady=10)
        tk.Entry(self.root, textvariable=self.click_count).grid(row=1, column=1, columnspan=2)

        tk.Label(self.root, text="Select Window:").grid(row=2, column=0, padx=10, pady=10)
        self.window_menu = tk.OptionMenu(self.root, self.window_title, *self.get_window_titles())
        self.window_menu.grid(row=2, column=1, columnspan=2)

        self.refresh_button = tk.Button(self.root, text="Refresh Windows", command=self.refresh_windows)
        self.refresh_button.grid(row=3, column=0, columnspan=3, pady=10)

        self.start_button = tk.Button(self.root, text="Start (F10)", command=self.start_clicking)
        self.start_button.grid(row=4, column=0, columnspan=3, pady=10)

        self.stop_button = tk.Button(self.root, text="Stop (F9)", command=self.stop_clicking)
        self.stop_button.grid(row=5, column=0, columnspan=3, pady=10)
        self.stop_button.config(state=tk.DISABLED)

    def get_window_titles(self):
        return [window.title for window in gw.getAllWindows() if window.title]

    def refresh_windows(self):
        menu = self.window_menu["menu"]
        menu.delete(0, "end")

        new_windows = self.get_window_titles()
        for window in new_windows:
            menu.add_command(label=window, command=lambda value=window: self.window_title.set(value))

    def start_clicking(self):
        click_type = self.click_type.get()
        click_count = self.click_count.get()
        window_title = self.window_title.get()

        if click_count <= 0:
            messagebox.showerror("Error", "Number of clicks must be greater than 0")
            return

        if not window_title:
            messagebox.showerror("Error", "Window title must be provided")
            return

        target_window = gw.getWindowsWithTitle(window_title)
        if not target_window:
            messagebox.showerror("Error", "Window not found")
            return

        self.target_window = target_window[0]
        self.is_running = True
        self.start_button.config(state=tk.DISABLED)
        self.stop_button.config(state=tk.NORMAL)
        thread = threading.Thread(target=self.perform_clicks, args=(click_type, click_count))
        thread.start()

    def stop_clicking(self):
        self.is_running = False
        self.start_button.config(state=tk.NORMAL)
        self.stop_button.config(state=tk.DISABLED)

    def perform_clicks(self, click_type, click_count):
        for _ in range(click_count):
            if not self.is_running:
                break

            if not self.target_window.isActive:
                self.target_window.activate()
                time.sleep(0.1)  # Pause to ensure the window is activated

            # Use the current mouse position
            x, y = pyautogui.position()

            if click_type == "left":
                pyautogui.mouseDown(x=x, y=y, button="left")
                pyautogui.mouseUp(x=x, y=y, button="left")
            elif click_type == "right":
                pyautogui.mouseDown(x=x, y=y, button="right")
                pyautogui.mouseUp(x=x, y=y, button="right")

            time.sleep(0.1)  # Small pause between clicks

        self.start_button.config(state=tk.NORMAL)
        self.stop_button.config(state=tk.DISABLED)


def main():
    root = tk.Tk()
    app = AutoClicker(root)

    # Register hotkeys
    keyboard.add_hotkey('F10', app.start_clicking)
    keyboard.add_hotkey('F9', app.stop_clicking)

    root.mainloop()


if __name__ == "__main__":
    main()
