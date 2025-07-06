
# 🕵️ Reverse Engineering CrackMe 2012 App – Full Deep Dive with Sass & Skill

> _"This is for educational purposes only, officer. I swear on my disassembler."_

---

## 🎯 I. Recon and Enumeration – The Calm Before the Storm

We download the "Trial" version of the Pro-App, fresh from the archives — a fine vintage, circa 2012. Perfect.

Upon launching, we’re immediately greeted with a dramatic splash screen:  
**“This screen won’t appear after registration.”**  
Oh? Challenge accepted.

Let's explore. Time to play detective and open every menu like it’s a murder mystery.

In the title bar, an honest little tag: **“Unregistered.”**  
Yup. That’s our first suspect.

The “Help” section’s ‘About’ tab also proudly displays **“Unregistered.”**  
They're really trying to make a point here.

We try to register with a fake key — and as expected:  
❌ **“Bad serial key”** – Classic.

💡 _Insight: Since it’s a small app, recon is fast. For larger or modern apps, this could take hours._  
📜 _Legal Note: This is a legacy app, no longer in active use — pulled from the archives for educational fun._  
⚠️ _Cracking apps you don't own? Not cool. Learning how cracking works? Totally fair game._

---

## 🧠 II. Advanced Recon & Enumeration – Peeking Under the Hood

1. Load the executable into **Detect It Easy (DIE)**.  
   ➤ It’s written in **Delphi** and... **UNPACKED**.  
   ➤ No packers, no obfuscation. This is like finding a safe with the door wide open.

2. Launch **OllyDBG**, load the app.

3. Right-click → `Search for → All Referenced Strings`  
   → Right-click again → `Search for Text` → type **"register"**  
   → Check “Entire Scope”

🧠 _Why "register"? Breadcrumb trail: we saw “Register”, “Unregister”, and “Registration” in the UI._

4. Use `Ctrl + L` until you locate the string **"Registered"**.  
   Right-click → **Breakpoint → On Access**

📌 _This string controls the title bar — a good place to catch the license check._

5. Press `F9` → Olly breaks at BP. Let’s analyze:

```asm
String used at 9AABA9
Stored at 9AABBC
Conditional jump just above at 9AABA5
```

6. Breakpoint on `9AAB9E` → CMP instruction.  
   ➤ Step with `F8` → Olly shows **“Jump is taken.”**  
   ➤ Toggle the **ZF (Zero Flag)** → jump is not taken.

7. Repeat: `F8 → F9 → set ZF → repeat` until app loads.

But...  
- Splash screen still there  
- Features still locked  
- But About section and title say “Registered”

✅ **Cosmetic win. Functional fail.**

💡 _Conclusion: This section is purely cosmetic. We need to go deeper._

---

## ✨ III. Basic Patching – Just a Little Tweak...

Back to `9AABA5` (**JE** instruction):

- Press `Space` → change `JE` to `JNZ`  
- Click **Assemble**  
- Right-click → `Copy to Executable → All modifications`  
- Save as `TreeDBNotes_mod.exe`

🎭 Cosmetic success — app says “Registered”.

---

## 🧬 IV. Advanced Patching – When Cosmetic Isn’t Enough

1. Set BP at `9AAB9E`

2. In dump: `Follow memory at [EAX+15B8]`  
   - This holds the **registration flag**:  
     - `00` = unregistered  
     - `01` = registered

Try editing:  
→ `00` → `01` → Run (`F9`) → resets to `00`

```asm
CMP BYTE PTR DS:[EAX+15B8], 0
```

3. Set **HW breakpoint on write** on the byte:
   
  > _Now you may wonder why I didn’t just put a regular breakpoint on this. It is because I tried that first! But Olly would not break on it. There are several reasons that could cause this; this code changes polymorphically, so our BP is lost, there is a check in the app for a software breakpoint and the app removes it, the breakpoint is in a section that Olly will not track automatically… It happens. If it does, we need to set a hardware breakpoint on it instead. There are no guarantees that a HW breakpoint will work, as the app may specifically check for these as well, but it is a more robust way of placing a breakpoint, so it usually works._
   
   → Right-click → `Breakpoint → Hardware on Write → Byte`

5. `F9` → breaks in new routine (the flag-resetter)

💡 _HW breakpoints are clean & undetectable — no memory edits._

5. Clear HW BP: `Debug → Hardware Breakpoints → Delete All`

6. Set HW **execution** BP at `9ADBF4`

🧩 Strategy: Patch it to **always write 01**

---

## 🛠️ Binary Patching for Real

>_K, now let’s think for a minute. This routine is called before our original break. This routine checks if we are registered or not and puts a zero in the memory address pointed to by [EAX+15B8] if it is not, and a 01 (or any non-zero) if it is. Then our old routine is called, the one that either prints “Registered” or “Unregistered” on the title of the window based on if this memory location contains a 0 or 1. So if we make sure a 1 is put into that memory location every time this routine is run, then any other routines will check that memory location and see that it is a 1 and think that we’re registered. What would happen if we just change this routine to always put a 01 into the proper memory location? Well if you've learnt Assembly you could of think of the steps or you could learn from my programs only from the Assembly repo......otherwise follow-along_

>Now the next question is what’s the easiest way to do that. Well, we have the memory location already being populated with something (DL) at address 9ADBFC, so we could just change the DL top a one. The problem with this is that changing the DL to a one will add a byte to the length of this instruction, and this will overwrite our RETN statement.What about if we replace the compare and jump instructions and instead just load 01 into DL. That way, on the last line, DL will be moved into our memory location! So here’s what we do- highlight the two compare and jump instructions

1. Right-Click -> Binary -> Fill with "NOPs" [which gives us 8 consecutive NOPS]
   
2. On the first line ie:9ADBF4 highlight it -> space bar -> replace NOP with mov DL,1 -> Click "Assemble"
   
3. Now, whenever this routine is called, a one will be put into the memory flag instead of a zero. Since we are still paused on the first line of this routine, you can single step to see DL being loaded with 1, and then the 1 being put into the memory address (you may need to go to the proper address in your dump as Olly has probably reset it again). Now run the app and Olly will break at our original breakpoint
   
4. And we can see that we are going to fall through to the correct string. Go ahead and keep running and we will break in our modified registration check routine, and it will put a 01 into our address again as we planned. This will go back and forth a couple times until finally

**Original Code:**
```asm
009ADBF4  3A90 B8150000   CMP DL, BYTE PTR [EAX+15B8]
009ADBFA  74 06           JE SHORT 009ADC02
009ADBFC  8890 B8150000   MOV BYTE PTR [EAX+15B8], DL
009ADC02  C3              RET
```

**Replace With:**
```asm
009ADBF4  B2 01           MOV DL, 1
009ADBF6  90              NOP
009ADBF7  90              NOP
009ADBF8  8890 B8150000   MOV BYTE PTR [EAX+15B8], DL
009ADC02  C3              RET
```

- Press `Space` → assemble `MOV DL, 1`
- Fill rest with **NOPs**

✅ Memory now holds at `01`  
✅ Splash screen gone  
✅ Feature unlocks possible

---

## 💾 Step 16: Save Your Masterpiece

Save as `TreeDBNotes_mod1.exe`  
Backup or discard previous versions

---

## 🧪 Debug Challenge: ASLR is Watching

**ASLR (Address Space Layout Randomization)** = address changes on every run.

📌 _Avoid hardcoding addresses. Use labels, relative jumps, or execution flows._

---

## 🔥 BONUS: Why Hardware Breakpoints Are a Hacker's Best Friend

### ❌ Why Software Breakpoints Can Fail:
- Use `INT 3` (0xCC)
- Can be detected
- Fails on:
  - Self-modifying code  
  - Read-only memory  
  - Obfuscated / dynamic code  

### ✅ Why Hardware Breakpoints Rule:
- Set via CPU registers (`DR0–DR3`)
- Don’t modify memory
- Work in read-only segments
- Track **read/write/exec**
- Limit: 4 per thread

---

## 🎉 KISTIMATTTTTTTT !!!

You did it.  
From recon to runtime patching, you've walked the path of a reverse engineer — with sass and precision.

> _“It’s not about cracking apps. It’s about understanding how apps are built, defended, and tricked.”_

🧠 You, after this guide.
