# Hidden in Plain Graphic (Forensics) - 100

> Agent Ali, who are secretly a spy from Malaysia has been communicate with others spy from all around the world using secret technique . Intelligence agencies have been monitoring his activities, but so far, no clear evidence of his communications has surfaced. Can you find any suspicious traffic in this file?
> 
- `plain_zight.pcap` – PCAP capture

# Solution

### Examining Traffic
While examining the captured traffic packets in Wireshark, we're able to see that most requests are quite normal - including queries such as [um.edu.my](http://um.edu.my/), [github.com](http://github.com/), etc. Sorting by length allows us to find a rather interesting TCP stream that is much larger in length than the rest of the packets.

![image](https://github.com/user-attachments/assets/927bb843-1f65-4451-b075-f0c456bbd777)

Following this stream will bring us to a client packet with a `.PNG` header, indicating an image.

![image1](https://github.com/user-attachments/assets/6956ad03-f435-41aa-80aa-50d3ad2e55bc)

### Converting the Image
We'll convert the captured data to raw bytes in Wireshark and create a .png file with them.

![image2](https://github.com/user-attachments/assets/e710db65-4105-4b2c-aeda-f7122dae3037)

```bash
vi image.png
mimeopen image.png

```

As a result, we are left with this logo:

![image3](https://github.com/user-attachments/assets/709adc56-1eec-4fb5-a19b-98741a0e8c88)

Since there's nothing *visually* hidden inside the image itself, we'll use zsteg to detect other hidden data within the .png. Doing this successfully yields the flag:

```bash
zsteg image.png
  "b^~SyY[ww"
  "24:umcs{h1dd3n_1n_png_st3g}"
  "A3tgA#tgA"
```

# Conclusion
This challenge involves investigating network traffic within a .pcap capture. Using Wireshark, we found an abnormally large TCP stream that contained data from a .png file. The solution is to restore the file by copying the image data retrieved from Wireshark and saving it as a .png. Afterwards, we utilized zsteg to analyse the image further and revealed hidden data embedded within the image's LSB to successfully uncover the flags.
