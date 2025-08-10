# Part 1 – Crackme Analysis

---

## 🛠 Enum and Recon

![image](<image-placeholder>)

Let's lock and load the crackme in our **OLLY**.  
Hmm, interesting 🤔 — no display for the input.  
A keypad from range `1–F`.

Let’s press it... hmm no response... let's press again and again... wait, what?!  
**"Trying to Brute Force"** appears.

Let's restart and count again.  
It says **30 presses** until the bad boy message appears.

**Assumption:**  
It’s a **10-character password** with **3 tries** (a quite common pattern).

---

## 🔍 Advanced Enum and Recon

Go ahead and load **Crackme12.exe** into **OllyDbg** and let’s have a look around.

This is what a standard app, written in C or C++, looks like when using a **dialog box** as the main program window.

> 💡 If the program used a **regular window** instead of a dialog box, it would look different.

---

### DialogBoxParamA

We see the arguments being pushed onto the stack and the call to:

```c
DialogBoxParamA(...)
```

This sets up a dialog box to be used as the program’s main window.  

Getting help on `DialogBoxParamA`, the most important thing for us is the address of **DLGPROC** — the callback function that handles all Windows messages.

Looking back at the disassembly, we can see this address:

```
40102B
```

---

## 📍 Main Dialog Callback Message Handler

![image](<image-placeholder>)

This is a fairly normal-looking `DlgProc`.  
Usually, it’s a **big switch statement**, though in assembly it becomes a **big if/then block**.

If you read my last tutorial, it should look familiar — the only difference here is that Olly couldn't figure out the `case` labels (e.g., `case 113 (WM_TIMER)`).

Essentially, it's checking the message ID sent with the `DLGProc` window against `if-else` statements.

---

### Running the App

![image](<image-placeholder>)

Playing with it, look for some *unusualities* —  
For example, the **Clear** button doesn’t work. Why? Nobody knows.

---

## ⏱ Breakpoint on DlgProc

Let’s put a breakpoint at:

```
40102B
```

Restart the app to watch the messages come in.

The moment we start, the breakpoint hits, and we get:

```asm
40102E CMP [ARG.2], 110
```

If we Google `110`, we find it’s **InitDialog** — used for initializing controls and basic setup.  

Reference: [Windows Message Codes](https://www.autoitscript.com/autoit3/docs/appendix/WinMsgCodes.htm)

---

### Message Flow Observed

- `WM_SETFONT` (`30`)
- `WM_CLOSE` (`10`)
- `WM_COMMAND` (`111`)

Example:

```asm
CMP [ARG.2], 0x66 ; Button ID check
```

---

## 📩 WM_COMMAND – Why It’s Important

`WM_COMMAND` is a **catch-all** for many user interaction events:

| Action                | Triggered By                                   |
|-----------------------|-----------------------------------------------|
| Button clicks         | `BN_CLICKED`                                  |
| Menu selections       | `WM_COMMAND` with menu ID                     |
| Toolbar actions       | ID of toolbar item                            |
| Dialog item changes   | Combo boxes, checkboxes, etc.                 |

In **reverse engineering**, this is where the **serial check** or other key logic often happens.

---

### Reading wParam in WM_COMMAND

In OllyDbg:

| Windows Name | Olly ARG | Contents                                        |
|--------------|----------|------------------------------------------------|
| `wParam`     | `ARG.2`  | Control ID (low word) + Notification code (hi word) |
| `lParam`     | `ARG.3`  | Handle to the control (or `0`)                  |

```c
wParam = (HIWORD << 16) | LOWORD;
```

**Parts:**
- `LOWORD(wParam)` → Control ID (button/menu item)
- `HIWORD(wParam)` → Notification code (`BN_CLICKED`)

---

### Example in C

```c
switch(msg) {
    case WM_COMMAND:
        switch(LOWORD(wParam)) {
            case ID_REGISTER_BUTTON:
                if (HIWORD(wParam) == BN_CLICKED)
                    ShowNagDialog();
                break;
        }
        break;
}
```

---

## 🧠 Code Filtering Logic

```asm
MOV     EAX, [ARG.3]
MOV     EDX, EAX
SHR     EDX, 10h       ; Get HIWORD
OR      EDX, EDX
JNZ     SHORT skip     ; Skip if HIWORD != 0
```

In C-like pseudocode:

```c
HIWORD = (wParam >> 16);
if (HIWORD != 0)
    goto skip;
```

---

## 💾 Memory Initialization

Finally, we see:

```asm
MOV [403048], 0
MOV [403038], 0xDEAD
MOV [40303C], 0xDEAD
MOV [403040], 0x42424242
```

**Meaning:**
1. `[403048] = 0` → Counter/flag reset.
2. `[403038] = 0xDEAD` & `[40303C] = 0xDEAD` → Magic numbers for state verification.
3. `[403040] = "BBBB"` → Placeholder data.

---

## 🖥 Rendering Observed

Pressing `F9` repeatedly (~10 times) shows the crackme **rendering piece by piece** in real-time.

---

## 📜 Summary

- A dialog box is set up with `DialogBoxParamA`.
- Windows sends initialization messages (`WM_SETFONT`, `WM_CLOSE`, `WM_COMMAND`, etc.).
- Most logic happens inside `WM_COMMAND`.
- Magic constants (`0xDEAD`, `"BBBB"`) are set during initialization.
- Rendering occurs incrementally, step-by-step, after setup.

> Even moving the mouse over the dialog will send new messages to the handler.
