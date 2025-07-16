_# ⚔️ WINRAR Cracking Adventure

> **Well hello there!!!**  
> Back to another cracking adventure, and this time — it’s the LEGENDARY **WINRARRRRR!!!**

---

### ⚠️ Disclaimer

*This isn’t the current version of WinRAR (they're pretty sweet peeps and we don’t want to kick them in the guts). This is a deprecated version pulled from an archive, for **learning purposes only**.*

---

## 🔍 Recon and Enumeration

We’ve got the installer. Install the app, right-clicking through all the suggestions as we go.  
Let’s start the app — **\*NAG\***. Close it. Open it again — **\*NAG\*** again. Close it again.

Let’s investigate.

The title bar shows **"Evaluation Copy"** — hmm, interesting.  
To make things more exciting, let’s increment our system date by 40 days (beyond the trial period) and… voilà!  
**\*NAG\*** after **\*NAG\*** after **\*NAG\*** **\*NAG\*** **\*NAG\*** — yeah, it’s persistent!

---

### 🧪 OllyDbg: Version Comparison

| Feature                            | **OllyDbg 1.10** (Classic)                                          | **OllyDbg 2.0** (Rewritten)                              |
|------------------------------------|----------------------------------------------------------------------|-----------------------------------------------------------|
| 🔢 **Architecture**                | 32-bit only                                                         | 32-bit only (partial 64-bit support in 2.01 beta)         |
| 🧠 **Stability & Plugins**         | Extremely stable, **TONS of plugins**                               | Rewritten from scratch, **limited plugin support**        |
| 🧱 **Stack & Memory Views**        | Functional but dated UI                                             | Improved **stack visualization** and cleaner UI           |
| 🧩 **Plugin Compatibility**        | Supports plugins like **Phant0m, StrongOD, Olly Advanced**          | **Plugins must be rewritten** for 2.0                     |
| 🔍 **Analysis Engine**             | Basic analysis                                                      | Slightly improved engine, **but some bugs exist**         |
| 🧑‍💻 **Community & Tutorials**    | 99% of tutorials are for 1.10                                       | Few tutorials exist for 2.0                               |
| 🪲 **Debugging Protection Bypass** | Compatible with classic tools                                       | Less compatible with tools like **ScyllaHide**, **StrongOD** |
| 📜 **Script Support**              | Supports **ODbgScript** for automation                              | Limited scripting support                                 |
| ⚒️ **Development Status**          | Abandoned but still heavily used                                    | Also abandoned, never fully completed                     |

---

## 🧭 Advanced Recon and Enumeration

1. Fire up OllyDbg.  
   > *Note: For better stack operations, OllyDbg 2.0 is recommended. Unfortunately, I couldn’t get it to work properly, so I’m continuing with OllyDbg 1.10 and the Advanced plugin.*

2. Drag and drop the WinRAR executable.

3. Now for some wisdom:  
   Trying "Search by String Reference" may leave you completely lost.  
   Don’t believe me? Try out all the traditional crackme steps — you’ll quickly see how **impractical** they are for real-world apps.

### We’ll explore 3 ways to analyze it:

---

#### ✅ i. Using the Pause Button

- Run the program.
- Wait for the **NAG** to appear — **don’t close it**.
- In Olly, press the pause `||` button beside "Play".

> 💡 Boom! You’ll be taken directly to the address that triggered it.

---

#### ✅ ii. Using the Call Stack

- Press `K` (for *Kall*, Russian spelling of "Call")  
  Or if using Olly with the Advanced plugin, press `St` (Stack Trace).

> Programs are built from many smaller functions that call each other — these are organized in a **stack**, with the last called function on top.

This window traces:
- The functions called.
- The order of execution.
- The arguments passed.

Example:
- We see a call to `DialogBoxParamA` with arguments.
- It’s from `WinRAR`, and passed through `user32.dll` functions.

The **Call Stack** helps you identify:
- Who called whom.
- Whether the dialog box is initiated from your app or Windows itself.
- Conditions like:  
  - `"Case XX (WM_SOMETHING) of switch 0043F0A4"`

> These are Windows Message Handler switch-cases. If you’re not familiar with Windows message procedures, don’t worry — future tutorials will cover it.

Look at the `WM_TIMER` case:
- It’s likely a timer was triggered to display the NAG screen.
- Scroll up and you’ll find where the timer was set.

🧠 Assembly handles switch/case as a series of `if/else` jumps. Something like:


**Assembly switch-case breakdown (as the pseudo-code):**

```c
if (msg != WM_CREATE)
jump to next if
Do WM_CREATE code
Jump to end
if (msg != WM_DESTROY)
jump to next if
Do WM_DESTROY code
Jump to end
if (msg != WM_SIZE)
jump to next if
Do WM_SIZE code
```

So at the beginning of each case, the code checks whether the incoming message matches the condition. If not, it jumps to the next. If it matches, it executes that block.

In our case, the NAG screen is tied to the `WM_TIMER` message — meaning some timer is triggering it.  
If we scroll up enough in Olly, we’ll find the function or message that starts the timer. And that’s our root cause.

---

### ✅ iii. Using Resource Hacker

Time for a static GUI-based approach.

- Open **Resource Hacker**
- Load the WinRAR executable (`WinRAR.exe`)
- Navigate to `.rsrc` → `REMINDER` → `1049`

🎯 That's the **NAG** screen — in all its glory!

> For fun and knowledge:  
> Click through the folders, subfolders, and bitmaps to view different windows/dialogs.  
> You could even **edit** and save them — and see the **MAGIC**.

Now let’s combine this with OllyDbg:
- In Olly: `Search for Referenced Strings`
- Right-click → `Search for text` → type `"REMINDER"`
- This lands us back at the exact section responsible for triggering the dialog

🧠 Note:  
Many cracking tutorials will tell you to simply **delete the dialog** in Resource Hacker.  
That **does work** — in this case. The NAG disappears!

However, this won’t work universally. It depends on whether the program **expects** the resource to be there.  
Still, it’s a great trick to test early.

---

## 🩹 Patching the Code

Now let’s perform a **proper binary patch** to remove the NAG.

### Goal:
Make the message handler **skip** the WM_TIMER case completely.

### How?

1. Go to this address in Olly:
   ```
   0043F79E  JNZ SHORT WinRAR.0043F81D
   ```

2. Press **Spacebar** on the line.

3. Change `JNZ` (Jump if Not Zero) to `JMP` (unconditional jump).

4. Apply patch:
   - Right-click → `Copy to Executable` → `All Sections`
   - Save the modified binary as: `WinRARmod.exe`

✅ That’s it! 🎉

---

## 🎉 KISTIMAT!!!

You’ve just **removed the annoying NAG screen** from WinRAR!  
You may still spot the "Evaluation Copy" string — but that’s embedded in a different way (possibly a static string or resource) and will be covered in a future chapter.

> 🔒 This walkthrough was for educational purposes only — never use reverse engineering to harm real-world software vendors.

---

## 🛡️ Final Thoughts

This exploration showed you:
- How real-world binaries use **timers** and **message handlers**
- How to trace **runtime events** with OllyDbg
- How to statically inspect resources using **Resource Hacker**
- How to patch binaries safely and correctly

---

## 💬 Questions or Suggestions?

Feel free to open an issue or pull request. Let’s keep learning and building responsibly.

---

## ☕ with love, curiosity, and a little bit of madness...

Stay tuned for the next crack!_
