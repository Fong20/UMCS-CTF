# Gist of Samuel - 216
> Samuel is gatekeeping his favourite campsite. We found his note.

flag: umcs{the_name_of_the_campsite}
> 
- `gist_of_samuel.txt` – Text file to be decoded

# Solution

### Examining the File Contents
We are provided with a text file which contains the content to be decrypted. The file’s content consists of various emojis, with a combination of 3 types of train emojis.

![Screen Shot 04-13-25 at 01.05 PM.PNG](attachment:ab04c392-d516-423b-8eea-29ddd8f6f8d8:Screen_Shot_04-13-25_at_01.05_PM.png)
![Screen Shot 04-13-25 at 01.05 PM.PNG](attachment:ab04c392-d516-423b-8eea-29ddd8f6f8d8:Screen_Shot_04-13-25_at_01.05_PM.png)

We’re able to identify that each of these train emojis represent a different symbol in *morse code* by utilizing some good old pattern recognition.

The dots are most common in morse and make up majority of the letters. They can be seen here represented by this emoji: 🚂.

The separators can be seen wedged in between words and not found at the start nor end. In here, they are represented by this emoji: 🚆

Through the power of elimination, we can assume that 🚋 is used to represent the dashes in morse.

There are a few other nods and hints to morse code within the challenge itself, one being the name “Samuel” - which hints at Samuel Morse, the inventor of Morse Code. Another is the invisible characters inside the challenge hint.

![image.png](attachment:e47d6497-3c7b-4e5f-b787-93d25f8f6a3d:image.png)

### Decoding the File Contents
Once we have analyzed and obtained crucial clues, the emojis are parsed into Cyberchef and decoded utilizing the Find/Replace Recipe, which returned a potential morse code.

![Cyberchef result.png](attachment:78a348e0-777d-4636-b983-9043fcca068c:Cyberchef_result.png)

### Decoding the Morse Code
The morse code is then decoded to reveal the hidden message behind it. In this case, there is a gist code!

**Gist code: e012d0a1fffac42d6aae00c54078ad3e**

Besides that, the decoded morse code also provided additional information such as Samuel’s favourite transportation, which is a train as well as his favourite number which is 8. These information would come in  handy in the later sections.

![Morse code translator.png](attachment:892505ad-65b6-411c-95ae-061f4ff26133:Morse_code_translator.png)

### Searching for the Gist
Utilizing the provided gist code, we entered in the URL:

```bash
https://gist.github.com/E012D0A1FFFAC42D6AAE00C54078AD3E
```

in hopes of finding additional information to obtain the flag. This had led us to a gist on umcybersec’s account.

Once the search was done, we stumbled upon a text file, named **gistfile1.txt** which could potentially lead us to the flag. 

![Github Gist.png](attachment:23c298e6-e8f9-421c-9623-cfc795fa4af4:Github_Gist.png)

### Analyze the Gist Text File
The gist text file is downloaded and upon opening the file, we are presented with new content to be decrypted. 

![image.png](attachment:be490fda-a5bd-4089-8612-3919e5207f62:image.png)

### Decoding the Gist Text File Contents
As mentioned above, the decoded morse code provided information which stated that Samuel likes trains very much and his favourite number is 8. By utilizing this information, we can deduce that:

- **trains = A reference that the content is encoded using Rail Rence Cipher**
- **favourite number is 8 = The key of the cipher is 8**

Hence, we parsed in the file’s content into Cyberchef and decrypted it utilizing the Rail Fence Cipher Decode Recipe with the key value of 8, which returned an output.

![Cyberchef cipher decode.PNG](attachment:2b36f907-7066-4b5e-9360-15cffc1a1f34:Cyberchef_cipher_decode.png)

The output is then copied to an empty file to be inspected. Upon zooming out the file’s content, we can clearly see the message of the flag!

![Flag.png](attachment:29b889ed-0501-4880-9b63-4a8c99831e47:Flag.png)

```
umcs{willow_tree_campsite}
```
# Conclusion
This challenge involves decoding a .txt file containing a sequence of train emojis into a morse code sequence. The morse code sequence contains a string of numbers and letters that provides us with several crucial information such as Samuel’s favourite number, Samuel’s favourite transportation which is a train and a gist code which leads us to obtaining the gist text file. The text file is then analyzed and decoded using the Rail Cipher Recipe to obtain the desired output.
