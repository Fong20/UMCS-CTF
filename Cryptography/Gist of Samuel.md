# Gist of Samuel (Crypto) - 216
> Samuel is gatekeeping his favourite campsite. We found his note.

flag: umcs{the_name_of_the_campsite}
> 
- `gist_of_samuel.txt` – Text file to be decoded

# Solution

### Examining the File Contents
We are provided with a text file which contains the content to be decrypted. The file’s content consists of various emojis, with a combination of 3 types of train emojis.

![Screen Shot 04-13-25 at 01 05 PM PNG](https://github.com/user-attachments/assets/6fbd632f-b561-4abe-8a91-0ffaff53f34f)

We’re able to identify that each of these train emojis represent a different symbol in *morse code* by utilizing some good old pattern recognition.

The dots are most common in morse and make up majority of the letters. They can be seen here represented by this emoji: 🚂.

The separators can be seen wedged in between words and not found at the start nor end. In here, they are represented by this emoji: 🚆

Through the power of elimination, we can assume that 🚋 is used to represent the dashes in morse.

There are a few other nods and hints to morse code within the challenge itself, one being the name “Samuel” - which hints at Samuel Morse, the inventor of Morse Code. Another is the invisible characters inside the challenge hint.

![image](https://github.com/user-attachments/assets/467ed202-9e41-4506-8fbc-d674c4b6e040)

### Decoding the File Contents
Once we have analyzed and obtained crucial clues, the emojis are parsed into Cyberchef and decoded utilizing the Find/Replace Recipe, which returned a potential morse code.

![Cyberchef result](https://github.com/user-attachments/assets/d969c85b-5cdc-4f02-af2c-ab7195765771)

### Decoding the Morse Code
The morse code is then decoded to reveal the hidden message behind it. In this case, there is a gist code!

**Gist code: e012d0a1fffac42d6aae00c54078ad3e**

Besides that, the decoded morse code also provided additional information such as Samuel’s favourite transportation, which is a train as well as his favourite number which is 8. These information would come in  handy in the later sections.

![Morse code translator](https://github.com/user-attachments/assets/82cc184a-54a7-46d5-accf-dc20e4cf241d)

### Searching for the Gist
Utilizing the provided gist code, we entered in the URL:

```bash
https://gist.github.com/E012D0A1FFFAC42D6AAE00C54078AD3E
```

in hopes of finding additional information to obtain the flag. This had led us to a gist on umcybersec’s account.

Once the search was done, we stumbled upon a text file, named **gistfile1.txt** which could potentially lead us to the flag. 

![Github Gist](https://github.com/user-attachments/assets/998c3e61-8be9-4afd-806e-e1872d1cfcb9)

### Analyze the Gist Text File
The gist text file is downloaded and upon opening the file, we are presented with new content to be decrypted. 

![image](https://github.com/user-attachments/assets/e1677d10-b667-4664-adcf-01dc691043df)

### Decoding the Gist Text File Contents
As mentioned above, the decoded morse code provided information which stated that Samuel likes trains very much and his favourite number is 8. By utilizing this information, we can deduce that:

- **trains = A reference that the content is encoded using Rail Rence Cipher**
- **favourite number is 8 = The key of the cipher is 8**

Hence, we parsed in the file’s content into Cyberchef and decrypted it utilizing the Rail Fence Cipher Decode Recipe with the key value of 8, which returned an output.

![Cyberchef cipher decode PNG](https://github.com/user-attachments/assets/1532d85d-1221-4adf-aadd-b635a1994468)

The output is then copied to an empty file to be inspected. Upon zooming out the file’s content, we can clearly see the message of the flag!

![Flag](https://github.com/user-attachments/assets/cd3c49a0-e5e8-40db-99dd-b34563d9d5e1)

```
umcs{willow_tree_campsite}
```
# Conclusion
This challenge involves decoding a .txt file containing a sequence of train emojis into a morse code sequence. The morse code sequence contains a string of numbers and letters that provides us with several crucial information such as Samuel’s favourite number, Samuel’s favourite transportation which is a train and a gist code which leads us to obtaining the gist text file. The text file is then analyzed and decoded using the Rail Cipher Recipe to obtain the desired output.
