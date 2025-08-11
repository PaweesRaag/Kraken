# PART - 2
## <br>MUCH BETTER (*CRACKING THE KNUCKLES*) It's patching time

In part two of this three part series we will go over self-modifying code and will eventually crack this binary. As promised, it will be challenging, but don’t worry if you don’t get everything- a lot is specific to this binary and you may never see again. But still we might need to remember until we meet again.

### Understanding the app
Now that we’ve seen how the basic message handler callback works, let’s see if we can use this to crack this crackme.

We can see that there are really only three messages that this app handles:

- `110` (`INITDIALOG`)
- `10` (`DESTROY_WINDOW`)
- `111` (`COMMAND`)

Any other messages are ignored.

We’ve already gone through the init dialog code, and we don’t really care about the destroy window code, as that’s only called when we close the app. Therefore, anything worth noting happens in the **WM_COMMAND** section.

So let’s only pause Olly in that section. Remove any old breakpoints and set a new one at address `40108E`, or after :the compare/jump for ID `111`, and run the app.

You will notice that now if you move the mouse over the window, resize it, move it, or do anything that doesn’t involve clicking a button, Olly continues to run, as all of these messages are ignored.

Now click on the first button, `'1'`.

Olly breaks at our breakpoint. We can also see that the `ARG.3` variable contains `'65'`

<img width="228" height="133" alt="Screenshot 2025-08-11 112631" src="https://github.com/user-attachments/assets/66075b2e-c35a-48ea-8411-0efa7feb2ce9" />


<br>

If we were to open our crackme in **Resource Hacker** (from the last tutorial) and open up the main dialog, you would see that `65` (or `101` in decimal) is the ID for the number **'1'** button:

<img width="726" height="483" alt="Screenshot 2025-08-11 112700" src="https://github.com/user-attachments/assets/d8b3be21-a9d4-42bc-ad5a-a2dde7c45124" />


<br>

That is the ID that is in `ARG.3`! It is just the ID of the button.

So we step down a couple of lines and we see the compares begin, comparing the ID sent in with this message with the ID’s hard coded into the app.


```asm
0040109A  |. /0F85 AC010000 JNZ crackme1.0040124C
004010A0  |. |66:83F8 65    CMP AX,65
004010A4  |. |75 0C         JNZ SHORT crackme1.004010B2
004010A6  |. |6A 01         PUSH 1
004010A8  |. |E8 F8010000   CALL crackme1.004012A5
004010AD  |. |E9 40010000   JMP crackme1.004011F2
004010B2  |> |66:83F8 66    CMP AX,66
004010B6  |. |75 0C         JNZ SHORT crackme1.004010C4
004010B8  |. |6A 02         PUSH 2
004010BA  |. |E8 E6010000   CALL crackme1.004012A5
004010BF  |. |E9 2E010000   JMP crackme1.004011F2
004010C4  |> |66:83F8 67    CMP AX,67
004010C8  |. |75 0C         JNZ SHORT crackme1.004010D6
004010CA  |. |6A 03         PUSH 3
004010CC  |. |E8 D4010000   CALL crackme1.004012A5
004010D1  |. |E9 1C010000   JMP crackme1.004011F2
004010D6  |> |66:83F8 68    CMP AX,68
004010DA  |. |75 0C         JNZ SHORT crackme1.004010E8
004010DC  |. |6A 04         PUSH 4
004010DE  |. |E8 C2010000   CALL crackme1.004012A5
004010E3  |. |E9 0A010000   JMP crackme1.004011F2
004010E8  |> |66:83F8 69    CMP AX,69
004010EC  |. |75 0C         JNZ SHORT crackme1.004010FA
004010EE  |. |6A 05         PUSH 5
004010F0  |. |E8 B0010000   CALL crackme1.004012A5
004010F5  |. |E9 F8000000   JMP crackme1.004011F2
004010FA  |> |66:83F8 6A    CMP AX,6A
004010FE  |. |75 0C         JNZ SHORT crackme1.0040110C
00401100  |. |6A 06         PUSH 6
00401102  |. |E8 9E010000   CALL crackme1.004012A5
00401107  |. |E9 E6000000   JMP crackme1.004011F2
0040110C  |> |66:83F8 6B    CMP AX,6B
00401110  |. |75 0C         JNZ SHORT crackme1.0040111E
00401112  |. |6A 07         PUSH 7
00401114  |. |E8 8C010000   CALL crackme1.004012A5
00401119  |. |E9 D4000000   JMP crackme1.004011F2
0040111E  |> |66:83F8 6C    CMP AX,6C
00401122  |. |75 0C         JNZ SHORT crackme1.00401130
00401124  |. |6A 08         PUSH 8
00401126  |. |E8 7A010000   CALL crackme1.004012A5
0040112B  |. |E9 C2000000   JMP crackme1.004011F2
00401130  |> |66:83F8 6D    CMP AX,6D
00401134  |. |75 0C         JNZ SHORT crackme1.00401142
00401136  |. |6A 09         PUSH 9
00401138  |. |E8 68010000   CALL crackme1.004012A5
0040113D  |. |E9 B0000000   JMP crackme1.004011F2
00401142  |> |66:83F8 6E    CMP AX,6E
00401146  |. |75 0C         JNZ SHORT crackme1.00401154
00401148  |. |6A 0A         PUSH 0A
0040114A  |. |E8 56010000   CALL crackme1.004012A5
0040114F  |. |E9 9E000000   JMP crackme1.004011F2
00401154  |> |66:83F8 6F    CMP AX,6F
00401158  |. |75 0C         JNZ SHORT crackme1.00401166
0040115A  |. |6A 0B         PUSH 0B
0040115C  |. |E8 44010000   CALL crackme1.004012A5
00401161  |. |E9 8C000000   JMP crackme1.004011F2
00401166  |> |66:83F8 70    CMP AX,70
0040116A  |. |75 09         JNZ SHORT crackme1.00401175
0040116C  |. |6A 0C         PUSH 0C
0040116E  |. |E8 32010000   CALL crackme1.004012A5
00401173  |. |EB 7D         JMP SHORT crackme1.004011F2
00401175  |> |66:83F8 71    CMP AX,71
00401179  |. |75 09         JNZ SHORT crackme1.00401184
0040117B  |. |6A 0D         PUSH 0D
0040117D  |. |E8 23010000   CALL crackme1.004012A5
00401182  |. |EB 6E         JMP SHORT crackme1.004011F2
00401184  |> |66:83F8 72    CMP AX,72
00401188  |. |75 09         JNZ SHORT crackme1.00401193
0040118A  |. |6A 0E         PUSH 0E
0040118C  |. |E8 14010000   CALL crackme1.004012A5
00401191  |. |EB 5F         JMP SHORT crackme1.004011F2
00401193  |> |66:83F8 73    CMP AX,73
00401197  |. |75 09         JNZ SHORT crackme1.004011A2
00401199  |. |6A 0F         PUSH 0F
0040119B  |. |E8 05010000   CALL crackme1.004012A5
004011A0  |. |EB 50         JMP SHORT crackme1.004011F2
004011A2  |> |66:83F8 01    CMP AX,1
```

---
> Read this section carefully, what it does is it takes input from the keypad and then compare with switch case and then calls back to the section to perform an action (keep this in mind, it would be very important for the third part)
<br>At 4012A5 the main work seems to be done

<br><table><tr><td>Well now were into the meat of it!</table></tr></td>
<br>After setting up the stack we begin accessing the same memory locations we accessed in the WM_INITDIALOG section, namely starting at address 403038. So let’s open that up in the dump so we have a frame of reference. 
<br>There’s our _“DEAD”_ twice along with our 0x42s and the address 403000. Single stepping, we first move the 42s into ECX, and the two 0xDEADs into EBX and EAX

Next, we do a series of compares to find out which button we pushed based on the value that was pushed onto the stack. Here, **`SS:[EBP+8]`** is directly accessing this pushed value. Since we clicked the first button, we will perform the first set of instructions:

> The first thing we will do is add `0x54B` to `ECX` (`42424242`) which gives us `4242478D`.  
> Next we multiply `EAX` by `EBX` (which is `0xDEAD` times `0xDEAD`) which gives us `C1B080E9`.  
> Finally, we XOR the `ECX` register with the `EAX` register and jump to location `4013E7`. Stepping over the jump lands us here:

Which is toward the end of this method. If you scroll back up and take a look, you will see that basically all of the buttons do the same thing; they add a value, XOR a value and jump to the end. They just differ by the values.

Then here, at the end, we increment the contents of memory location `403044` (which started as zero), and we can assume this is some sort of counter. We then store our new values for `ECX`, `EBX` and `EAX` back into the same memory we read them from.

After returning, we come back to the main function (not including that part as I'd be doing an elaborate discussion on it later in part 3).

---

Next we compare memory location `403048` (which is zero) with `3` (we don’t know why yet), then compare our counter at address `403044` with `0x0A`. Again, this indicates that `403044` contains a counter that counts to `0x0A`. We then jump if it’s not equal to `0x0A`, telling us that we will run through this loop 10 times before we fall through.

You may also have noticed the `JNB` at address `4011F9` that points to the brute-force message. Obviously, location `403048` will have some sort of counter in it, and if it gets above 3 we will get the brute-force message.

<img width="579" height="388" alt="Screenshot 2025-08-11 112800" src="https://github.com/user-attachments/assets/210aa3bc-1b1f-4cea-b7e4-e218a088f10d" />


---

Now, let’s continue running the program and click on button #2. We break at our `BP ARG.3`, and in return, `EAX` and `EDX` will equal the ID of button number 2, or `0x66`.

That means we will now run the code associated with button #2:

Jumping into the call at `4010BA`, we do the same thing we did the first time through, only this time:

1. The memory will **not** contain `0xDEAD` and `42424242`, but instead will contain adjusted values  
2. Since we clicked on the second button, we will perform the code at address `4012D6` which performs a `SUB ECX, 233` and `IMUL EBX, EBX, 14` etc.

We then jump to the end of the routine again.

Here, we increment the counter at `403044`, move the new variables back into their memory locations and return to our main loop.

Stepping once jumps to the end of our main loop:

<img width="609" height="372" alt="Screenshot 2025-08-11 112959" src="https://github.com/user-attachments/assets/4b46be11-0d73-4bca-898b-2dd189bca8f9" />


Where we compare `403048` (which is still zero) and jump to the brute force message if it’s greater than `3`.

We also compare `403044` with `0x0A` and jump to the error code if our ID is above this (can you figure out why?).

We then return from our main loop and return to the Windows loop that waits for us to do something.


### Cracking The App:

Now that we understand how the app works, let’s patch it. For this app, we need to use a little intuition.

By following through the entire flow of this app, we can see that there are not a lot of compare/jumps out of the normal flow. Really, the only ones we see are:

- The jump to the **brute force message** at address `4011F9`
- A jump to the **‘about’ box** if we fall all the way through all of the compares from address `4010B2` to `4011A6`
- A jump to the **‘clear’ code** that resets the memory locations back to `0xDEAD` and `42424242` at address `4011CA`
- And a fall through to the **‘error message’** at `401204`

If you click the **‘about’** button and trace the code, you will see that it only displays the about box and then returns to our main loop. Doing the same on the **‘clear’** button does the same.

So that leaves either the brute force code or the error code.

---

Now here’s where a little intuition comes in:

Every time we checked the address `403048` to see if we should jump to the brute force message, the contents were zero and the jump was never taken.

However, the compare at address `4011FB` compares the counter at address `403044` and this will jump after reaching `0x0A`.

We also know that every time through the loop, the contents of `403044` were incremented, so we can assume this counter **‘counts’ how many buttons we’ve pressed**.

---

Of course, your first thought will be:

> “Yeah, but that code leads to an error message!”

But does it? All the code does is load a pointer to a message that says there was an error, but is this displayed? Not in this code… so maybe **not at all**.

This section of code looks highly suspicious, so let’s trace through it.

We know that we get to this code by clicking at least `0x0A` (10) buttons.

So let’s place a **breakpoint (BP)** at address `401204`, clear our other breakpoints, and re-start the app (so we can count off 10 button presses):

<img width="578" height="193" alt="Screenshot 2025-08-11 113101" src="https://github.com/user-attachments/assets/2e622882-da3f-4599-a522-c3686d2c0db6" />


---

Hmm, this sets up and then calls `VirtualProtect`.

After reading the info on `VirtualProtect`, you will see that it is basically used to change the attributes of a section of memory.

For example, the section of a binary that contains the executable code has its attributes generally set to **execute**, but **not writable**, as there really isn’t much point in writing to the code section — that’s what the data section is for.

If you wanted to change a part of the code section to **‘writable’** in addition to **‘executable’**, you would use this function.

Then you could write to this memory section, effectively changing the code ‘on the fly’.

This is how **self-modifying code** works — it calls `VirtualProtect` on a section of memory in the code section, adds the **‘writable’** attribute, changes the code (perhaps XORing it with a number), and then calls `VirtualProtect` again to change the attributes back to **executable only**.

Now, the code has been changed on the fly.

---

It appears that this app is doing something similar.

The last argument to `VirtualProtect` is the memory location you want to change the attributes for, and the third value is the length in bytes of the section you want to alter.

In this case we can see:

- The starting address is `401407`
- The length is `0x1F4` (500 bytes)
- The second argument is `PAGE_READWRITE`, making this section writable as well as readable

Let’s look at this section of memory, starting at `401407`, and see what is going to change.

---

**What does that mean??**

``` c
VirtualProtect(
    lpAddress = 0x401407,         // Start of memory to make writable
    dwSize    = 0x1F4,            // 500 bytes
    flNewProtect = PAGE_READWRITE,
    lpflOldProtect = &OldProtect  // store the old state
)
```
This means:  
The code section from `0x401407` to `0x401407 + 0x1F4 = 0x4015FB` is temporarily made writable. So the program can self-modify this section of code.

This is extremely common in:  
1. Packers  
2. Anti-debugging  
3. Polymorphic/mutating malware  
4. Or even crackmes meant to teach you this exact thing.

---

### What Might Be Happening?

Here’s what this almost certainly indicates:

- The code decrypts or decodes itself at runtime.  
- OllyDbg or any other debugger might show you dummy bytes until this memory is modified.  
- After modification, the real code or logic will exist only at runtime.

<img width="439" height="395" alt="Screenshot 2025-08-11 113226" src="https://github.com/user-attachments/assets/f8747852-e83a-449a-9e42-fdd110135f4d" />

Hmmmmm. That looks really suspicious. It doesn’t look like code at all. Let’s keep going and see what the app changes in this section of memory. Step just past the call to `VirtualProtect`:

<img width="460" height="247" alt="Screenshot 2025-08-11 113340" src="https://github.com/user-attachments/assets/f1dfabd8-7d05-4560-ba2f-f3a3308e4c1f" />


Now, the first thing we do is move the contents of memory location `403038` into `EAX` and XOR it with memory location `401407`, storing the result back into address `401407`.

Wait a minute! `401407` was the first address of the memory section we changed the attributes for so that we could write to it. And `403038` began as `0xDEAD` but was changed depending on which buttons we pressed (and in what order).

So this sequence of instructions is changing that memory space based on what buttons and in which order they were pressed.

Step over until we get to the `JNZ` at address `401475` and then let’s look at address `401407`:

### Why 401407?

As you correctly noted earlier:  
`0x401407` is the first byte in the memory region we made writable with `VirtualProtect`. This is a code location, meaning this XOR is literally altering the app's own instructions at runtime.

---

### Interpretation

- You are watching the self-decryption routine in action.  
- The program builds a value in `[403038]` (`EAX`) based on user interaction — like button presses.  
- It then XORs that value with the code section to transform or unlock the real logic of the program.  
- This approach can create one-time-use logic paths (like one-time pads), or act as anti-reversing armor.

You will notice that address `401407` was changed and now has a valid instruction in it, a `JECXZ SHORT crackme1.004013E5`. The app just added a conditional jump to its own code! The way it did this was by changing the opcodes, or raw data, at that memory location.  

Going back to our current instruction, the next thing it does is compare the first byte at `401407` with `0x52` and jumps if it is not equal (to address `40148F`).  

Looking at the above picture, we can see the opcode value at `401407` is `E3` which does not equal `52`, so we will jump. The jump is to another setup and call of `VirtualProtect`, this time locking that section of memory back to executable.

<img width="581" height="519" alt="Screenshot 2025-08-11 113622" src="https://github.com/user-attachments/assets/27198fc9-70db-46c5-83fb-24da65af4aed" />


But before this, you may have noticed that memory location `401407` was XOR’ed again at address `40148F`.Looking again at address `401407` we see that it was changed again:



We then push a value (`F08E2`) into the stack and call another routine at address `401403`. Stepping in, we see that function:

Well, well, well. We have jumped to the area of memory that the app changed. We recognize the new `JMP` at address `401407`. Let’s step onto the jump and see where we go:

Odd, it is jumping to a return. So it appears this didn’t really do anything. We are now back at the main program:


The next thing we are going to do is reset our counter from `0x0A` back to zero. We will then increment the counter for the brute force check by one.  
Now we know how the brute force check works: if you enter a (wrong) 10-digit code more than 3 times, the contents of location `403048` will be above 3 and we will jump to the brute force message. If you want to try it, go ahead. Just remove the BP at address `401204` and enter in a 10-digit code three times.

As expected: **"Brute force"**

---

### Why This Is Cool (and Important)

- The app is essentially changing its own flow dynamically depending on user input.  
- First, it tries to create a conditional jump (`JECXZ`) to trick reverse engineers.  
- Then it verifies the instruction and decides if it wants to permanently change it to something else (`JMP`).  
- This is one-time transformation logic — very typical of crackmes, keygens, and malware.  
- This makes static analysis difficult because what you see in the binary isn’t what runs.

Pseudo code
```c
// Dynamic memory-based control flow shaping
EAX = memory[0x403038];       // Influenced by user input
memory[0x401407] ^= EAX;      // First change → Conditional jump
if (memory[0x401407] != 0x52) // Validation
{
    memory[0x401407] ^= EAX;  // Second change → Unconditional jump
    VirtualProtect(..., EXECUTE); // Lock code again
}
```
### So what we know so far:

1. The password is **10 digits**.  
2. If you try more than **three times** with the wrong code, you get a **brute force message** and have to restart.  
3. Every time you hit a button, memory locations `403038`, `40303C` and `403040` get modified in a different way for each button.  
4. After hitting ten buttons, we enter a couple calls that check our code and **change a jump instruction** in the code section of memory at address `401407`.  
5. If the password is **not correct**, the jump that is created points to a **return** that just returns us back to the main loop.  
6. Therefore, entering the correct password must change this jump to something else, either:  
   - a jump to a different memory location where our "good boy" will be, or  
   - changing more of the code in this area to create the "goodboy" at this memory section instead of creating a jump.  

> This sounds more plausible because if it was simply changed into a jump to a new location, what is all this weird looking, non-functioning code for?  
> Knowing all this, we know we must zero in on the section of code that does the self modifying changes, namely the code starting at address `40144`.

---

### Important Insight:

One thing we can gather is that the compare with `0x52` at address `40146e` is pretty important.  
It basically tells the program that the changes to the code that have been made are the correct changes.  

<img width="587" height="330" alt="Screenshot 2025-08-11 113753" src="https://github.com/user-attachments/assets/b4fc347e-2344-4b5d-ba4d-6bd1ad117414" />


But what does an opcode of `0x52` mean?  

> After a rather lengthy Google search, I discovered that opcode `0x52` is **“PUSH EDX”**.  

So, this code checks to see if the first instruction is a **“PUSH EDX”**, and if it isn’t, it bugs out.  

---

### What if we force that instruction to be a PUSH EDX?

- Set a breakpoint (BP) at address `40146E` where the code checks for the push instruction and run the app.  
- When we break at this address, go to address `401407` and change the value to `0x52`.
<img width="573" height="250" alt="Screenshot 2025-08-11 113940" src="https://github.com/user-attachments/assets/d5d407f4-9363-4f8b-8331-36fa40b6b37c" />

Also read:  
- [Understanding Opcodes in Solidity](https://medium.com/@hardikksavaliya/understanding-opcodes-in-solidity-a-deep-dive-into-ethereums-low-level-operations-c2cbb4ab788a)  
- [Alphanumeric Opcode Reference](https://nets.ec/Shellcode/Appendix/Alphanumeric_opcode)  


---

### Continuing After Changing PUSH EDX

- Single stepping, we should bypass the `JNZ` on the `0x52` check.  
- Now, the code moves the value at address `40303C` into `EAX` and XORs it with memory location `40143B`.  

> What is that address? Let’s look: It is just a memory location toward the end of our self-modified code section.  

After XORing it, we then have:

<img width="500" height="475" alt="Screenshot 2025-08-11 114511" src="https://github.com/user-attachments/assets/4c234055-a9b3-4f17-833d-051f932a5018" />

---

### Analysis

- Now we know that these locations are **not being changed into the proper code**, so it’s not really helping us, but since this is the last thing that the app changes, it must be important.  
- Let’s keep going, now that we’ve changed the PUSH EDX and see what this section does.  

Step back into the main loop and then into the call at address `401211`.  
Highlight the modified section -> right-click -> Analysis


# MUCH BETTER

<img width="553" height="472" alt="Screenshot 2025-08-11 115227" src="https://github.com/user-attachments/assets/bb5b0ca2-7f57-426b-952c-f0b65374ff49" />


It looks like real code now! Well, except for that bit at the end that we created incorrectly.  
From here, I'm gonna quote the author since I can't grasp it completely even now:

> "Now, this is where things get a little challenging and experience will be helpful here.  
> We must look at this code from a ‘big-picture’ standpoint and think **“What is it trying to do?”**.  
> We have a `PUSH EDX` at the beginning. This, together with the `POP EDX` at the end tells us that EDX will be used in this code locally.  
> We then have some empty NOPs which should probably contain code, though we don’t know yet which code.  
> We then have a bunch of memory locations being XORed with DWORDs. Experience will tell you that this normally means we are decrypting something, and in this case it’s whatever EDX points to.  
> We can deduce that because EDX is `PUSH`ed and then never set, even though it goes on to be changed and referenced.  
> The NOPs are probably the location to set EDX, and EDX will point to something that will be decrypted (or altered) with the XORs.  
> Lastly, we have several memory locations that were incorrectly decrypted starting at address `40143D`. BUT the call to `SetDlgItemTextA` was not one of them, meaning this instruction was not changed.  
> Generally before a call to `SetDlgItemTextA`, we have seen that arguments are pushed onto the stack, so we can assume that when we enter the correct password, the instructions from `40143D` to `401442` will probably contain several push instructions (probably 3).  
> Now the big question is what should EDX point to? We have several choices here, and again, this is where experience comes in.  
> An experienced reverse engineer will probably remember that string **“An error occurred”** and think **“we never used that string. We saw that it was just a decoy and was never used. Maybe that is what will be decrypted…”**.  
> Another hint that tells us that this is a viable solution is that the string is pushed onto the stack but is never used. Why? Here is a picture of the stack when we enter this code"

<img width="382" height="152" alt="Screenshot 2025-08-11 115310" src="https://github.com/user-attachments/assets/4fae0e49-3774-4cf1-aa49-cf5acda9ff02" />


So assuming that we want to test our hypothesis, we want EDX to point to this string.  
The easiest way would be to simply load EDX with the offset in memory that the **“An error occurred”** string is placed, namely address `403000`.  
The problem is that would take up too many bytes. Looking again at our code, there are only **3 NOPs** that we can use to load EDX with a pointer to the error string.  

Well, putting our assembly hat on, and remembering that the string is currently pushed onto the stack, maybe we can load EDX with the pointer to the string from the stack…  

Generally we load a local variable with an instruction like this:  
```assembly
MOV EDX, [EBP + some_#]  

Stack Window
0019F8C0   00000000
0019F8C4   00000065
0019F8C8  /0019F8D8
0019F8CC  |00401216  RETURN to crackme1.00401216 from crackme1.00401403
0019F8D0  |000303CA
0019F8D4  |00403000  ASCII "An error occured"
0019F8D8  ]0019F904
0019F8DC  |769E148B  RETURN to user32.769E148B
```
Never in my mind have I thought before to check the register window. I was assuming that 19F8C0 was the default EBP but no look over here:
Registers Window
```assembly
EAX 00000065
ECX D6620000
EDX 00000000
EBX 00000001
ESP 0019F8C0
EBP 0019F8C8
ESI 000303CA
EDI 00000111
EIP 00401408 crackme1.00401408
```
19F8C8-19F8D4 = 12 !!!!

# Why `MOV EDX, [EBP+0xC]` instead of `MOV EDX, 0x0019F8D4`?

We’re constrained to using **3 NOPs (3 bytes)** to set up this operation, meaning the instruction must be compact.

---

### Option 1: `MOV EDX, 0x0019F8D4`

**What It Does:**  
This would directly load the absolute stack address `0x0019F8D4` into `EDX`.  
The value at `0x0019F8D4` (which is `0x00403000`) would **not** be loaded — only the address itself.

**Assembly Encoding:**  
`MOV EDX, imm32` (where `imm32` is a 32-bit immediate value like `0x0019F8D4`) uses the opcode `BA` followed by 4 bytes for the address.  
- Total size: **5 bytes** (`BA D4 F8 19 00`).

**Problem:**  
- This **exceeds** the 3-byte constraint (5 bytes > 3 bytes).  
- It loads `0x0019F8D4` into `EDX`, **not** the desired `0x00403000`.  
- To get `0x00403000`, you'd need a second instruction like `MOV EDX, [EDX]`, which adds more bytes and violates the NOP limit.  
- Stack addresses like `0x0019F8D4` are **specific to this execution instance**. If the program runs again, the stack might be allocated differently (e.g., `0x0019F9D4`), making the hardcoded address unreliable.

---

### Option 2: `MOV EDX, [EBP + 0Ch]`

**What It Does:**  
This loads the value stored at the memory location calculated as `EBP + 0Ch` into `EDX`.  
Since `0x0019F8D4` (where `0x00403000` resides) is **12 bytes (0Ch) above** the `EBP` base, this retrieves `0x00403000` directly.

**Assembly Encoding:**  
`MOV EDX, [EBP + imm8]` uses the opcode `8B` with a mod-reg-rm byte and a 1-byte immediate offset.  
- Specific encoding: `8B 55 0C` (where `8B` is the move, `55` specifies `EDX` and `[EBP]`, and `0C` is the offset).  
- Total size: **3 bytes**, fitting the NOP constraint.

**Advantages:**  
- **Size:** Fits within 3 bytes, replacing the 3 NOPs (`90 90 90`) perfectly.  
- **Dynamic:** Uses `EBP`, which is the frame pointer set by the function prologue. This makes the instruction relative to the current stack frame, so it works regardless of where the stack is allocated (e.g., `0x0019F8C8` vs. `0x0019F9C8`).  
- **Correct Value:** Loads the contents of `0x0019F8D4` (`0x00403000`) into `EDX`, not the address itself.

---

### Why Not `MOV EDX, 0x0019F8D4`?

- **Byte Constraint:**  
  `MOV EDX, 0x0019F8D4` is **5 bytes**, but you only have 3 bytes to work with (the 3 NOPs). So this option is invalid.

- **Hardcoded Address:**  
  `0x0019F8D4` is a specific stack address from your current run. In a different execution, the stack might be at a different memory location due to **ASLR** (Address Space Layout Randomization) or other factors. Hardcoding it would break portability.

- **EBP + 0Ch is Relative:**  
  Using `EBP + 0Ch` adapts to the stack frame’s base, making it more robust across runs.

- **Intent:**  
  The challenge hints at using the stack value (since the string is pushed onto the stack at `0x0019F8D4`).  
  `MOV EDX, [EBP + 0Ch]` leverages this, while `MOV EDX, 0x0019F8D4` ignores the stack context and loads an address instead of the desired data.

- **Follow-Up:**  
  If you used `MOV EDX, 0x0019F8D4`, you'd need another instruction (e.g., `MOV EDX, [EDX]`) to dereference it and get `0x00403000`, pushing the total beyond 3 bytes.  
  `MOV EDX, [EBP + 0Ch]` does it in **one step**.

---


Let's F8 and step over 
```assembly
EAX 00000065
ECX D6620000
EDX 00403000 ASCII "An error occured"
EBX 00000001
ESP 0019F8C0
EBP 0019F8C8
ESI 000303CA
EDI 00000111
EIP 0040140B crackme1.0040140B
```
### Boom loaded Haa
<br>In Registers window under EBP click on 403000 highlight it and right click-> follow in dump. Now we will see the memory being set in real time as we press `F8`. Now press `F8` and we see it XOR'd to something "Beautiful" 
```assembly
00403000  41 63 63 65 73 72 6F 72  Accesror
00403008  20 6F 63 63 75 72 65 64   occurred
00403010  00 54 72 79 69 6E 67 20  .Trying     
00403018  74 6F 20 62 72 75 74 65  to brute
00403020  66 6F 72 63 65 3F        force?
```
### AGAIN !!!
```assembly
00403000  41 63 63 65 73 73 20 67  Access g
00403008  72 6F 63 63 75 72 65 64  roccured
00403010  00 54 72 79 69 6E 67 20  .Trying
00403018  74 6F 20 62 72 75 74 65  to brute
00403020  66 6F 72 63 65 3F        force?
```
### HELL YEAH !!!!
```assembly
00403000  41 63 63 65 73 73 20 67  Access g
00403008  72 61 6E 74 65 72 65 64  rantered
00403010  00 54 72 79 69 6E 67 20  .Trying
00403018  74 6F 20 62 72 75 74 65  to brute
00403020  66 6F 72 63 65 3F        force?
```
We got the words that we want to display but it can't be displayed yet as `SetDlgItemTextA` isn't set at the position
``` assembly
0040143B  |.  04 66         ADD AL,66
0040143D  |.  28DD          SUB CH,BL
0040143F  |.^ 7D CA         JGE SHORT crackme1.0040140B
00401441  |.  4A            DEC EDX
00401442  |.  C9            LEAVE
00401443  |.  E8 78000000   CALL <JMP.&USER32.SetDlgItemTextA>       ; \SetDlgItemTextA
```
The problem is, since we entered an incorrect password and the last statements were incorrectly decrypted, our message will never be displayed. What we have to do is rebuild the pushing of the argument onto the stack for the `SetDlgItemTextA`. Getting help in Olly on `SetGlgItemTextA`, we can see that there are three arguments that need to be pushed onto the stack (in assembly order):
```c
LPCTSTR lpString // text to set
int nIDDlgItem, // identifier of control
HWND hDlg, // handle of dialog box
```

**<img width="673" height="108" alt="Screenshot 2025-08-11 115750" src="https://github.com/user-attachments/assets/ee9f0814-bb39-4cfb-9b7b-2cddb274b9f9" />**

So according to the logic first we will load the message to be displayed and then we shall load the arguments before calling the winAPI at `40143B` we shall push `[EBP+0xC]` we wont repeat the same mistake as earlier lol
stack register
```assembly
0019F8C8  /0019F8D8
0019F8CC  |00401216  RETURN to crackme1.00401216 from crackme1.00401403
0019F8D0  |000403CE
0019F8D4  |00403000  ASCII "Access granted !"
```
EBP=19F8C8 so `push EBP+0c` means pushing **"Access Granted"**
As this is the pointer to our new text string. 
<br>The second and last options are a little harder, but fortunately we have a reference. There is a  `SetGlgItemTextA` when the **bruteforce message** is displayed
<img width="673" height="108" alt="Screenshot 2025-08-11 115750" src="https://github.com/user-attachments/assets/f2713a35-45cf-4031-9178-48f109a070af" />

We can see that the ControlID equals 3 and the handle to the window is 707AA. The control ID is easy:
```assembly
PUSH 3
```
The handle is a little harder, but looking at the stack again, it’s not that hard. The next one is a bit tricky. Fortunately, the handle is right on the stack:
<img width="378" height="207" alt="Screenshot 2025-08-11 115817" src="https://github.com/user-attachments/assets/6ca0c1ab-1c38-48d8-a46e-e564e48ba241" />

```assembly
PUSH DWORD PTR [EBP + 8]
```
Inserting our code now makes the disassembly look rather nice:
```assembly
0040143B      FF75 0C       PUSH DWORD PTR [EBP+C]
0040143E      6A 03         PUSH 3
00401440      FF75 08       PUSH DWORD PTR [EBP+8]
00401443  |.  E8 78000000   CALL <JMP.&USER32.SetDlgItemTextA>       ; \SetDlgItemTextA
```
Now press F9 and run boom "access granted"

<img width="294" height="263" alt="Screenshot 2025-08-11 115837" src="https://github.com/user-attachments/assets/f8d86d31-5e8a-4ac9-992a-0d3f6575ab93" />


# KISTIMAAT
Now save the patched file
