# Hotline Miami (Stegonography) - 138

> "You’ve intercepted a mysterious floppy disk labeled 50 BLESSINGS, left behind by a shadowy figure in a rooster mask. The disk contains a cryptic image and a garbled audio file. Rumor has it the message reveals the location of a hidden safehouse tied to the 1989 Miami incident. Decrypt the clues before the Russians trace your signal.”
> 
- [`iamthekidyouknowwhatimean.wav`](https://github.com/umcybersec/umcs_preliminary/blob/main/stego-Hotline_Miami/iamthekidyouknowwhatimean.wav) – Audio File
- [`readme.txt`](https://github.com/umcybersec/umcs_preliminary/blob/main/stego-Hotline_Miami/readme.txt) - How to concatenate the flag
- `rooster.jpg` - Image FIle

# Solution

### Read Me
This is a text file that contains instructions on how to construct the flag, and a little *Hotline Miami* reference.

```bash
DO YOU LIKE HURTING OTHER PEOPLE?

Subject_Be_Verb_Year
```

![image1](https://github.com/user-attachments/assets/8fddcbea-e2dd-4b89-9e2d-6a1dc98d7cef)

### Examining the Image File
First, we’ll start with `rooster.jpg` - running strings on the file will yield a name:

> **RICHARD**
> 

![image2](https://github.com/user-attachments/assets/6900aa58-9d39-4ddf-80a0-3c221113ce49)

This will be the “SUBJECT” in our final flag. 

### Examining the Audio File
Using Audacity to open the `.wav` file and view the waveform, we’re able to spot a hidden message within the waveforms:

![image3](https://github.com/user-attachments/assets/e0b8461f-a843-4902-ac42-efd21c0e7b13)

> **WATCHING 1989**
> 

“WATCHING” will be the verb, and “1989” will be the year.

### Constructing the Flag
umcs{RICHARD_?_WATCHING_1989}

The “Be” in this is likely “is” or “was”, which was what we tried, and found that the final flag was:

```bash
umcs{RICHARD_IS_WATCHING_1989}
```

### Red Herrings
Running zsteg on `rooster.jpg` will bring this:

```bash
b2,bgr,lsb,xy .. text: "QEUUUUUU"
b2,rgba,lsb,xy .. text: "SSSSSSSSWWWWWWWW"
```

Using the password “RICHARD” on steghide, you’re able to unlock this:


![image4](https://github.com/user-attachments/assets/4b87e770-e541-4bfb-af57-d2a90e8d7795)

Decoding this using the A1Z26 cipher brings this:

![image5](https://github.com/user-attachments/assets/362888db-e7b3-48f8-b7b9-2973ca84c667)

As far as I know, both of these have no major role in the flag solution.

# Conclusion
This challenge involves analysing two files - a .jpg and a .wav file, to unlock clues to the location of a hidden safehouse. Displaying the strings of the .jpg will yield the name “RICHARD”, while analysing the waveforms of the .wav file will show a message containing the words “WATCHING” and “1989”. By bringing those together, we can arrange the flag according to `readme.txt`.
