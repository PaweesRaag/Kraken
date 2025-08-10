# Part-III (If the password entered is wrong enter another and another and another Million Times until it's gaped open)

> "It's time to cook"

Remember the last part I told you we'd do something interesting with the "meat" section???? well now's the time, first of all understand what's happening I've simplified the complex Assembly to basic arithmetic just go through it once and diligently

```assembly
004012A9  |.  8B0D 40304000      MOV   ECX, DWORD PTR [403040]                ; variable 'c'
004012AF  |.  8B1D 3C304000      MOV   EBX, DWORD PTR [40303C]                ; variable 'b'
004012B5  |.  A1 38304000        MOV   EAX, DWORD PTR [403038]                ; variable 'a'
004012BA  |.  807D 08 01         CMP   BYTE PTR [EBP+8], 1                    ; *********** button pressed 1
004012BE  |.  75 10              JNZ   SHORT crackme1.004012D0
004012C0  |.  81C1 4B050000      ADD   ECX, 54B                               ; c += 54B
004012C6  |.  0FAFD8             IMUL  EBX, EAX                               ; b *= a
004012C9  |.  33C1               XOR   EAX, ECX                               ; a ^= c
004012CB  |.  E9 17010000        JMP   crackme1.004013E7

004012D0  |>  807D 08 02         CMP   BYTE PTR [EBP+8], 2                    ; *********** button pressed 2
004012D4  |.  75 12              JNZ   SHORT crackme1.004012E8
004012D6  |.  81E9 33020000      SUB   ECX, 233                               ; c -= 233
004012DC  |.  6BDB 14            IMUL  EBX, EBX, 14                           ; b *= 14
004012DF  |.  03C8               ADD   ECX, EAX                               ; c += a
004012E1  |.  23D8               AND   EBX, EAX                               ; b &= a
004012E3  |.  E9 FF000000        JMP   crackme1.004013E7

004012E8  |>  807D 08 03         CMP   BYTE PTR [EBP+8], 3                    ; *********** button pressed 3
004012EC  |.  75 0F              JNZ   SHORT crackme1.004012FD
004012EE  |.  05 82050000        ADD   EAX, 582                               ; a += 582
004012F3  |.  6BC9 16            IMUL  ECX, ECX, 16                           ; c *= 16
004012F6  |.  33D8               XOR   EBX, EAX                               ; b ^= a
004012F8  |.  E9 EA000000        JMP   crackme1.004013E7

004012FD  |>  807D 08 04         CMP   BYTE PTR [EBP+8], 4                    ; *********** button pressed 4
00401301  |.  75 0F              JNZ   SHORT crackme1.00401312
00401303  |.  23C3               AND   EAX, EBX                               ; a &= b
00401305  |.  81EB 22121100      SUB   EBX, 111222                            ; b -= 111222
0040130B  |.  33C8               XOR   ECX, EAX                               ; c ^= a
0040130D  |.  E9 D5000000        JMP   crackme1.004013E7

00401312  |>  807D 08 05         CMP   BYTE PTR [EBP+8], 5                    ; *********** button pressed 5
00401316  |.  75 0C              JNZ   SHORT crackme1.00401324
00401318  |.  99                 CDQ                                         ; converts DWORD to QWORD for idiv
00401319  |.  F7F9               IDIV  ECX                                    ; a /= c , div remainder --> r
0040131B  |.  2BDA               SUB   EBX, EDX                               ; b -= r
0040131D  |.  03C1               ADD   EAX, ECX                               ; a += c
0040131F  |.  E9 C3000000        JMP   crackme1.004013E7

00401324  |>  807D 08 06         CMP   BYTE PTR [EBP+8], 6                    ; *********** button pressed 6
00401328  |.  75 0F              JNZ   SHORT crackme1.00401339
0040132A  |.  33C1               XOR   EAX, ECX                               ; a ^= c
0040132C  |.  23D8               AND   EBX, EAX                               ; b &= a
0040132E  |.  81C1 79685400      ADD   ECX, 546879                            ; c += 546879
00401334  |.  E9 AE000000        JMP   crackme1.004013E7

00401339  |>  807D 08 07         CMP   BYTE PTR [EBP+8], 7                    ; *********** button pressed 7
0040133D  |.  75 12              JNZ   SHORT crackme1.00401351
0040133F  |.  81E9 F55F0200      SUB   ECX, 25FF5                             ; c -= 25FF5
00401345  |.  33D9               XOR   EBX, ECX                               ; b ^= c
00401347  |.  05 00104000        ADD   EAX, crackme1.<ModuleEntryPoint>       ; a += entrypoint(401000h)
0040134C  |.  E9 96000000        JMP   crackme1.004013E7

00401351  |>  807D 08 08         CMP   BYTE PTR [EBP+8], 8                    ; *********** button pressed 8
00401355  |.  75 10              JNZ   SHORT crackme1.00401367
00401357  |.  33C1               XOR   EAX, ECX                               ; a ^= c
00401359  |.  6BDB 14            IMUL  EBX, EBX, 14                           ; b *= 14
0040135C  |.  81C1 89250100      ADD   ECX, 12589                             ; c += 12589
00401362  |.  E9 80000000        JMP   crackme1.004013E7

00401367  |>  807D 08 09         CMP   BYTE PTR [EBP+8], 9                    ; *********** button pressed 9
0040136B  |.  75 0B              JNZ   SHORT crackme1.00401378
0040136D  |.  2D 87215400        SUB   EAX, 542187                            ; a -= 542187
00401372  |.  2BD8               SUB   EBX, EAX                               ; b -= a
00401374  |.  33C8               XOR   ECX, EAX                               ; c ^= a
00401376  |.  EB 6F              JMP   SHORT crackme1.004013E7

00401378  |>  807D 08 0A         CMP   BYTE PTR [EBP+8], 0Ah                   ; *********** button pressed A (10)
0040137C  |.  75 0C              JNZ   SHORT crackme1.0040138A
0040137E  |.  99                 CDQ
0040137F  |.  F7FB               IDIV  EBX                                    ; a /= b, remainder --> r
00401381  |.  03DA               ADD   EBX, EDX                               ; b += r
00401383  |.  0FAFC2             IMUL  EAX, EDX                               ; a *= r
00401386  |.  33CA               XOR   ECX, EDX                               ; c ^= r
00401388  |.  EB 5D              JMP   SHORT crackme1.004013E7

0040138A  |>  807D 08 0B         CMP   BYTE PTR [EBP+8], 0Bh                   ; *********** button pressed B
0040138E  |.  75 13              JNZ   SHORT crackme1.004013A3
00401390  |.  81C3 FE341200      ADD   EBX, 1234FE                            ; b += 1234FE
00401396  |.  81C1 DE452300      ADD   ECX, 2345DE                            ; c += 2345DE
0040139C  |.  05 9B43A49C        ADD   EAX, 9CA4439B                          ; a += 9CA4439B
004013A1  |.  EB 44              JMP   SHORT crackme1.004013E7

004013A3  |>  807D 08 0C         CMP   BYTE PTR [EBP+8], 0Ch                   ; *********** button pressed C
004013A7  |.  75 09              JNZ   SHORT crackme1.004013B2
004013A9  |.  33C3               XOR   EAX, EBX                               ; a ^= b
004013AB  |.  2BD9               SUB   EBX, ECX                               ; b -= c
004013AD  |.  6BC9 12            IMUL  ECX, ECX, 12                           ; c *= 12
004013B0  |.  EB 35              JMP   SHORT crackme1.004013E7

004013B2  |>  807D 08 0D         CMP   BYTE PTR [EBP+8], 0Dh                   ; *********** button pressed D
004013B6  |.  75 10              JNZ   SHORT crackme1.004013C8
004013B8  |.  25 78563412        AND   EAX, 12345678                          ; a &= 12345678
004013BD  |.  81E9 75580600      SUB   ECX, 65875                             ; c -= 65875
004013C3  |.  0FAFD9             IMUL  EBX, ECX                               ; b *= c
004013C6  |.  EB 1F              JMP   SHORT crackme1.004013E7

004013C8  |>  807D 08 0E         CMP   BYTE PTR [EBP+8], 0Eh                   ; *********** button pressed E
004013CC  |.  75 0D              JNZ   SHORT crackme1.004013DB
004013CE  |.  35 55550500        XOR   EAX, 55555                             ; a ^= 55555
004013D3  |.  81EB 51735800      SUB   EBX, 587351                            ; b -= 587351
004013D9  |.  EB 0C              JMP   SHORT crackme1.004013E7

004013DB  |>  807D 08 0F         CMP   BYTE PTR [EBP+8], 0Fh                   ; *********** button pressed F (15)
004013DF  |.  75 06              JNZ   SHORT crackme1.004013E7
004013E1  |.  03D8               ADD   EAX, EBX                               ; a += b
004013E3  |.  03CB               ADD   EBX, ECX                               ; b += c
004013E5  |.  03C1               ADD   ECX, EAX                               ; c += a

```

So, now we know what operations are performed by pressing each key.  
The next thing we need is the **inputs** and **outputs**.  

These we already know — remember, the code in the self-modifying section started as one thing:  
a group of **SHIKAKAAA**, and was later XORed into legitimate instructions.  

They were XORed with the three memory locations **`a`**, **`b`**, and **`c`**.  

Therefore:  
- **Input** → The code prior to being XORed  
- **Output** → The same locations after being XORed and modified against `a`, `b`, and `c`  

---

**Example transformation:**

| Address   | Before XOR (original) | After XOR (modified) | XOR Key |
|-----------|-----------------------|----------------------|---------|
| 401407    | EB 3F 90 90           | B9 B4 C5 9C          | a       |
| 40143B    | 04 66 E7 BB           | FF 75 0C 6A          | b       |
| 40143F    | 4D BD 08 8B           | 03 FF 75 08          | c       |

---

What we’re going to do is **try every combination** of these modifications,  
mimicking trying every possible combination manually by clicking on the buttons.  

When — after performing **10 operations** on a set —  
we have the correct values in the three variables,  
we’ll know we have the **correct password**.

---

The crackme author also provided the **first two characters** of the password: 7 & 9
The reason:  
- If you have a slow computer, brute forcing can take a long time.  
- On an 8-core machine, it took **about an hour** to crack starting with no characters known.  
- Starting with the first two characters known took **about a minute**.  

Normally, you wouldn’t have any characters (obviously),  
but this is not a tutorial on patience.


This is a code from the Author written in CPP (I don't know CPP if you can go nuts)
```cpp
#include <iostream>
using namespace std;

void brute( void )
{
    char finalAsciiSerial[11] = "";
    int    i, varA, varB, varC, tempVar, currentSerial[10];

    // we know the first number is '7'
    for (currentSerial[0] = 7; currentSerial[0] <= 7; currentSerial[0]++)
    {
     // and we know the second number is '9'
     for (currentSerial[1] = 9; currentSerial[1] <= 9; currentSerial[1]++)
     {
      for (currentSerial[2] = 1; currentSerial[2] <= 15; currentSerial[2]++)
      {
       for (currentSerial[3] = 1; currentSerial[3] <= 15; currentSerial[3]++)
       {
        for (currentSerial[4] = 1; currentSerial[4] <= 15; currentSerial[4]++)
        {
         cout << ".";    // Update display
         for (currentSerial[5] = 1; currentSerial[5] <= 15; currentSerial[5]++)
         {
          for (currentSerial[6] = 1; currentSerial[6] <= 15; currentSerial[6]++)
          {
           for (currentSerial[7] = 1; currentSerial[7] <= 15; currentSerial[7]++)
           {
            for (currentSerial[8] = 1; currentSerial[8] <= 15; currentSerial[8]++)
            {
             for (currentSerial[9] = 1; currentSerial[9] <= 15; currentSerial[9]++)
             {
                // Reset variables
                varA = 0xDEAD;
                varB = 0xDEAD;
                varC = 0x42424242;

                // Apply each digit
                for (i = 0; i < 10; i++)
                {
                    switch (currentSerial[i])
                    {
                    case 1:
                        varC += 0x54B;
                        varB *= varA;
                        varA ^= varC;
                        break;

                    case 2:
                        varC = varC - 0x233 + varA;
                        varB = (varB * 0x14) & varA;
                        break;

                    case 3:
                        varA += 0x582;
                        varC *= 0x16;
                        varB ^= varA;
                        break;

                    case 4:
                        varA &= varB;
                        varB -= 0x111222;
                        varC ^= varA;
                        break;

                    case 5:
                        if (varC != 0)        // Watch divide by zero!
                        {
                            varB -= (varA % varC);
                            varA /= varC;
                            varA += varC;
                        }
                        break;

                    case 6:
                        varA ^= varC;
                        varB &= varA;
                        varC += 0x546879;
                        break;

                    case 7:
                        varC -= 0x25FF5;
                        varB ^= varC;
                        varA += 0x401000;
                        break;

                    case 8:
                        varA ^= varC;
                        varB *= 0x14;
                        varC += 0x12589;
                        break;

                    case 9:
                        varA -= 0x542187;
                        varB -= varA;
                        varC ^= varA;
                        break;

                    case 10:
                        if (varB != 0)        // Watch divide by zero!
                        {
                            tempVar = varA % varB;
                            varA /= varB;
                            varB += tempVar;
                            varA *= tempVar;
                            varC ^= tempVar;
                        }
                        break;

                    case 11:
                        varB += 0x1234FE;
                        varC += 0x2345DE;
                        varA += 0x9CA4439B;
                        break;

                    case 12:
                        varA ^= varB;
                        varB -= varC;
                        varC *= 0x12;
                        break;

                    case 13:
                        varA &= 0x12345678;
                        varC -= 0x65875;
                        varB *= varC;
                        break;

                    case 14:
                        varA ^= 0x55555;
                        varB -= 0x587351;
                        break;

                    case 15:
                        varA += varB;
                        varB += varC;
                        varC += varA;
                        break;
                    }
                }

                // stop if serial equals proper values
                if ((varA == 0x9CC5B4B9) && (varB == 0xD1EB13FB) && (varC == 0x837D424E))
                {
                    // Convert to ASCII
                    for (i = 0; i < 10; i++)
                    {
                        if (currentSerial[i] <= 9)
                        {
                            finalAsciiSerial[i] = currentSerial[i] + 0x30;
                        }
                        else
                        {
                            finalAsciiSerial[i] = currentSerial[i] + 0x37;
                        }
                    }
                    cout << "\n\n*****  Bruteforced serial: " << finalAsciiSerial << "\n";\
                    return;
    }}}}}}}}}}}
}

int main()
{
    cout << "Bruteforcer by R4ndom\n\n";

    brute();

    cout << "\nBruteforcing done...\n";

    return 0;
}
```
## Understanding the Brute Force Logic

See, I can't code in C++ — that doesn’t mean I don’t understand what’s happening.

We know:
- The code is **10 digits long**.
- Each digit can be **1–F** (15 possible values).
- That means total combinations = **15<sup>10</sup>** (a lot!).

Because of the huge search space, a **low-level language like C or C++** is used for brute forcing.

---

### Time Complexity
- **O(15)** — because of 15 possible values at each loop level.
- The brute force is implemented as **nested loops** for each digit position.
- The **innermost loop** contains a **15-way `switch` statement**, executing according to our “Meat Section” logic.
- If you’ve gone through the meat section, you know each case applies a transformation to the state variables.

---

## But… How Do We Know When It’s Correct?

We need **test cases** — or in this case, a runtime check inside the program.

---

### Step-by-Step Reverse Engineering

#### 1️⃣ What the Program Does
Using **OllyDbg**, we discovered:
- On each button press (1–15), the program **stores values** into three memory locations:  
  - `a` → `[403038]`  
  - `b` → `[40303C]`  
  - `c` → `[403040]`
- These values are **modified** according to which button is pressed.
- After **10 presses**, the program **takes (a, b, c)** and XORs a portion of its own executable memory (self-modifying code).

---

#### 2️⃣ Observing the Decryption Behavior
When the correct **10-digit password** is entered:
- The XOR operation transforms gibberish instructions into **valid assembly**.
- This “unlocked” code executes and leads to the success condition (e.g., displaying *Access Granted*).

---


```assembly
MOV EAX, [403038]   ; a
XOR [401407], EAX
...
CMP BYTE PTR [401407], 52h   ; checks if XOR produced opcode for PUSH EDX
You manually observed (via OllyDbg and memory inspection) that:
```
Initially:
```
[401407] = EB 3F 90 90
[40143B] = 04 66 E7 BB
[40143F] = 4D BD 08 8B
```
After 10 correct operations:
```
[401407] = B9 B4 C5 9C
[40143B] = FF 75 0C 6A
[40143F] = 03 FF 75 08
```
That means:
```
a = EB3F9090 ^ B9B4C59C = 0x9CC5B4B9
b = 0466E7BB ^ FF750C6A = 0xD1EB13FB
c = 4DBD088B ^ 03FF7508 = 0x837D424E
```
These are the three magic values we want to obtain at the end of processing 10 inputs. They became our test case.

```sql
[START]
   ↓
User presses 10 GUI buttons (values 1–15)
   ↓
Each button updates variables a, b, c using math ops
   ↓
After 10 digits:
   ↓
Check if a == 0x9CC5B4B9 && b == 0xD1EB13FB && c == 0x837D424E
   ↓                 ↙
[YES]           [NO]
  ↓                ↓
Print password  Try next combo

```
## The XOR “Aha!” Moment

If you still don't understand at this point don't worry even I couldn't. So me an Idiot discovered that XOR is reversible and fixed ie: 
```
10^8=2
2^8=10 
10^2=8
```
---
### so in basic layman's term 
```
[Garbage] XOR [Magic No.] = [Final] 
therefore
[Garbage] XOR [Final] = [Magic No.]
```
### Applying to Our Case
From reverse engineering, we’ve got:
- `varA`, `varB`, and `varC`
- After the self-modifying code runs, these correspond to:
  - `MagicA`
  - `MagicB`
  - `MagicC`
```
varA ^ Password = MagicA
varB ^ Password = MagicB
varC ^ Password = MagicC
```

### Conclusion
Now that we know **everything** — the transformations, the magic numbers, and the check — we can brute force this.

Time to cook… in **C**.

```c
#include <stdio.h>
#include <string.h>

void brute(void)
{
    char finalAsciiSerial[11] = "";
    int i, varA, varB, varC, tempVar, currentSerial[10];

    // we know the first number is '7'
    for (currentSerial[0] = 7; currentSerial[0] <= 7; currentSerial[0]++)
    {
        // and we know the second number is '9'
        for (currentSerial[1] = 9; currentSerial[1] <= 9; currentSerial[1]++)
        {
            for (currentSerial[2] = 1; currentSerial[2] <= 15; currentSerial[2]++)
            {
                for (currentSerial[3] = 1; currentSerial[3] <= 15; currentSerial[3]++)
                {
                    for (currentSerial[4] = 1; currentSerial[4] <= 15; currentSerial[4]++)
                    @<caret>                    {
                        printf(".");    // Update display
                        fflush(stdout); // Ensure output is displayed immediately
                        for (currentSerial[5] = 1; currentSerial[5] <= 15; currentSerial[5]++)
                        {
                            for (currentSerial[6] = 1; currentSerial[6] <= 15; currentSerial[6]++)
                            {
                                for (currentSerial[7] = 1; currentSerial[7] <= 15; currentSerial[7]++)
                                {
                                    for (currentSerial[8] = 1; currentSerial[8] <= 15; currentSerial[8]++)
                                    {
                                        for (currentSerial[9] = 1; currentSerial[9] <= 15; currentSerial[9]++)
                                        {
                                            // Reset variables
                                            varA = 0xDEAD;
                                            varB = 0xDEAD;
                                            varC = 0x42424242;

                                            // Apply each digit
                                            for (i = 0; i < 10; i++)
                                            {
                                                switch (currentSerial[i])
                                                {
                                                case 1:
                                                    varC += 0x54B;
                                                    varB *= varA;
                                                    varA ^= varC;
                                                    break;

                                                case 2:
                                                    varC = varC - 0x233 + varA;
                                                    varB = (varB * 0x14) & varA;
                                                    break;

                                                case 3:
                                                    varA += 0x582;
                                                    varC *= 0x16;
                                                    varB ^= varA;
                                                    break;

                                                case 4:
                                                    varA &= varB;
                                                    varB -= 0x111222;
                                                    varC ^= varA;
                                                    break;

                                                case 5:
                                                    if (varC != 0)        // Watch divide by zero!
                                                    {
                                                        varB -= (varA % varC);
                                                        varA /= varC;
                                                        varA += varC;
                                                    }
                                                    break;

                                                case 6:
                                                    varA ^= varC;
                                                    varB &= varA;
                                                    varC += 0x546879;
                                                    break;

                                                case 7:
                                                    varC -= 0x25FF5;
                                                    varB ^= varC;
                                                    varA += 0x401000;
                                                    break;

                                                case 8:
                                                    varA ^= varC;
                                                    varB *= 0x14;
                                                    varC += 0x12589;
                                                    break;

                                                case 9:
                                                    varA -= 0x542187;
                                                    varB -= varA;
                                                    varC ^= varA;
                                                    break;

                                                case 10:
                                                    if (varB != 0)        // Watch divide by zero!
                                                    {
                                                        tempVar = varA % varB;
                                                        varA /= varB;
                                                        varB += tempVar;
                                                        varA *= tempVar;
                                                        varC ^= tempVar;
                                                    }
                                                    break;

                                                case 11:
                                                    varB += 0x1234FE;
                                                    varC += 0x2345DE;
                                                    varA += 0x9CA4439B;
                                                    break;

                                                case 12:
                                                    varA ^= varB;
                                                    varB -= varC;
                                                    varC *= 0x12;
                                                    break;

                                                case 13:
                                                    varA &= 0x12345678;
                                                    varC -= 0x65875;
                                                    varB *= varC;
                                                    break;

                                                case 14:
                                                    varA ^= 0x55555;
                                                    varB -= 0x587351;
                                                    break;

                                                case 15:
                                                    varA += varB;
                                                    varB += varC;
                                                    varC += varA;
                                                    break;
                                                }
                                            }

                                            // Stop if serial equals proper values
                                            if ((varA == 0x9CC5B4B9) && (varB == 0xD1EB13FB) && (varC == 0x837D424E))
                                            {
                                                // Convert to ASCII
                                                for (i = 0; i < 10; i++)
                                                {
                                                    if (currentSerial[i] <= 9)
                                                    {
                                                        finalAsciiSerial[i] = currentSerial[i] + 0x30;
                                                    }
                                                    else
                                                    {
                                                        finalAsciiSerial[i] = currentSerial[i] + 0x37;
                                                    }
                                                }
                                                finalAsciiSerial[10] = '\0'; // Null-terminate the string
                                                printf("\n\n***** Bruteforced serial: %s\n", finalAsciiSerial);
                                                return;
                                            }
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}

int main()
{
    printf("Bruteforcer by PaweesRaag\n\n");

    brute();

    printf("\nBruteforcing done...\n");

    return 0;
}
```
I willingly didn't compile it so that you can go through it and check for your self. Other than that 
# KISTIMAAT !!!!
