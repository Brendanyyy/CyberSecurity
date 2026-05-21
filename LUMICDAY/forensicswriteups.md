John Brendan V. Lumicday								BSIT-3  

 

 

PROBLEM 1 - PICOCTF "LIKE 1000" CHALLENGE WRITEUP 

Step 1: Extracting the 1000.tar Archive 

The first thing I encountered was a file named 1000.tar. This is a tar archive, which basically holds multiple files inside one package. I knew that my first task would be to extract all the contents of the archive to see what files I was working with. 

 

I ran the following command to extract the contents: 

tar -xvf 1000.tar 

tar is the tool I used to work with the tar archive. 

-xvf flags specify that I wanted to extract the files (x), see detailed output (v), and specify the file (f) to extract from. 

  

Once I ran the command, I saw that the archive contained a few files: 

disk.img: A disk image file, possibly containing additional data or file systems. 

rockyou.txt: A famous wordlist used in password cracking. 

shark1.pcapng, shark2.pcapng: Packet capture files that could contain network traffic data. 

wpa-ing_out.pcap: Another packet capture file, which likely contains data from a wireless network. 

At this point, I knew I had to look at each of these files carefully because they might hold clues to the flag. 

 

Step 2: Analyzing the Extracted Files 

Now that I had all the extracted files, I started by examining them to look for any clues. Here's how I tackled the analysis: 

rockyou.txt 

This file immediately stood out because it’s a well-known password wordlist. It’s typically used in brute-force attacks to guess passwords. The challenge might involve cracking a password, so I kept this file in mind, thinking it might be useful later on, especially when dealing with the packet capture files. 

shark1.pcapng and shark2.pcapng 

These are Packet Capture (PCAP) files, which store network traffic data. I knew that these files could contain valuable information like plaintext passwords, tokens, or other hidden messages transmitted over the network. To inspect the contents of these files, I used the strings command, which extracts readable text from binary files. 

strings shark1.pcapng 

Running strings on the file helped me uncover some readable data that might be useful. However, I didn’t find any immediate flag, so I moved on to the next step. 

 

Step 4: Inspecting the PNG Files 

The real discovery came when I opened the 00001000.png image file. At first glance, the file looked like an ordinary image, but I had a hunch that it might contain hidden information, a CTF challenges often use steganography to hide flags in images. 

  

I opened the image using the eog (Eye of GNOME) image viewer: 

eog 00001000.png 

To my surprise, when I opened the file, I saw a string of text: 

picoCTF{l0t5_0f_TAR5} 

 

 

 

PROBLEM 2 - PICOCTF "SHARK_ON_WIRE_2" CHALLENGE WRITEUP 

I started by looking at a set of ASCII codes that were provided or discovered during the challenge. These codes needed to be translated into readable text. Here's how I did it: 

I used an ASCII-to-text converter (like the one shown in the first image) where I entered the following ASCII codes: 

097 095 115 116 051 103 048 125 

After converting these codes, I got the following output: 

picoCTF{p1llf3r3d_data_v1a_st3g0} 

It was clear that this was the flag, wrapped in the typical picoCTF{}format. However, I could see that the flag had some obfuscation — certain characters were replaced with numbers to make it look likep1llf3r3dinstead ofpillf3red`. This was just a simple technique to hide the true flag. 

 

Step 2: Analyzing the Network Capture with Wireshark 

Once I had the flag, I moved on to the network capture analysis. The challenge involved working with a .pcap file, which contains network traffic. I used Wireshark to analyze the captured packets and find any relevant data that might lead to additional clues or a deeper understanding of the flag’s context. 

  

Following the UDP Stream: 

 

In the second part of the challenge, I used Wireshark's "Follow UDP Stream" feature. This is a powerful tool that allows you to view the data transmitted between two devices in a captured UDP stream. By following the stream, I could isolate the specific conversation that was taking place between the two IP addresses. 

Once I selected the UDP stream, I noticed that one of the packets contained the word "start". This immediately signaled to me that this might be the beginning of the data or message I needed to analyze further. 

 

Step 3: Identifying Key Messages and Extracting Data 

By continuing to follow the stream and filtering the packets using the IP address filter (e.g., ip.addr==10.0.0.66), I was able to focus on the relevant traffic and locate a small 5-byte payload that seemed interesting. The packet data looked like this: 

ff ff ff ff ff  

This part seemed like some form of encoded data or maybe even fragmented information, possibly a clue hidden within the raw network data. At this point, I realized that I needed to continue decoding or analyzing the conversation to uncover more clues. 

 

Step 4: Decoding the Full Message 

After extracting the key message (the "start" signal), I continued inspecting the conversation and followed more of the network traffic. I looked at the captured data in detail, hoping to find a continuation or further clues that could help me unlock the entire message. 

In doing this, I discovered that the communication revealed more information and helped me confirm the flag, which was a hidden piece of data embedded in the UDP stream. As I continued with my analysis, I found the full flag hidden in the data. 

 

 

 

PROBLEM 3 - PICOCTF "CANYOUSEE" CHALLENGE WRITEUP 

 

Step 1: Analyzing the ukn_reality.jpg Image 

The first thing I did was inspect the ukn_reality.jpg file. I used ExifTool to gather metadata from the image, which provided some useful details. 

File Information: 

The file size is 2.3 MB, which is typical for image files. 

The ExifTool output also shows that the file was last modified in February 2026. 

The image has a JPEG format and contains some embedded data. Notably, there’s an Attribution URL field with a Base64-encoded string, which is a common way to hide information. 

From this output, I understood that the image was carrying some embedded Base64-encoded data, so the next step was to decode that information. 

 

Step 2: Decoding the Base64 String 

I found the Base64 string in the ExifTool output under the "Attribution URL" field. I copied the string and used CyberChef to decode it. 

Using CyberChef: 

CyberChef is a powerful tool for data manipulation, and it makes it easy to decode Base64 strings. Here's how I did it: 

I selected the "From Base64" operation in CyberChef. 

I pasted the Base64 string (cGxqYb0NRntNR...) into the input field. 

I clicked Bake! to decode the string. 

After decoding, the output was: 

picoCTF{ME74D47A_HIDD3N_deca06fb} 

This is the flag for the challenge! It's in the format picoCTF{} and contains hidden characters inside the string. 

 

Step 3: Flag Extraction 

The final flag that I extracted from the Base64 decoding process is: 

picoCTF{ME74D47A_HIDD3N_deca06fb} 

This flag was hidden in the Exif metadata of the image, and after decoding it, I was able to submit it as the solution for the challenge. 

 

 

PROBLEM 4 - PICOCTF "CORRUPTED FILE" CHALLENGE WRITEUP 

Step 1: Hex Dump with xxd 

In the first image, I used the xxd command to display the hex dump of the file. This is useful for seeing the raw bytes of a file and understanding its structure. 

From the hex dump, we can spot several things: 

The beginning of the file looks like it starts with JFIF (FF D8 FF E0), which is the JPEG file signature. This indicates that the file is likely an image file, despite being corrupted. 

The rest of the bytes contain a mix of readable characters and non-printable ones, which may hint at some encoded information or corruption. 

 This hex dump gives us important insight into the file's content, and it's useful when dealing with file corruption, as we can often see where things have gone wrong or identify hidden data. 

 

Step 2: Using Hex Editors 

In the next step, I used a hex editor (like HexEd.it, shown in the second image) to open and further investigate the file. Hex editors allow for a more intuitive analysis of the raw data. 

Here’s what I found: 

I could confirm the presence of the JPEG signature (FF D8 FF), but there seems to be a mix of non-JPEG data after that. 

I searched for any ASCII strings in the raw hex view, which could be clues or hidden messages. I also noticed that part of the text in the hex dump, such as CDEF... and some encoded strings, could be meaningful if decoded. 

 

Step 3: Base64 Decoding 

One of the things I observed in the hex editor was the Base64-encoded data. This was a clear indicator that some data in the file might be hidden and could be decoded. 

To decode the Base64 string, I used CyberChef (as shown in the second image). CyberChef is a great tool for performing operations like Base64 decoding, and I used it to turn the encoded data into readable text. 

After decoding, I found the following flag: 

picoCTF{r3st0r1ng_th3_by73s_efd8c6c0} 

Step 4: Extracting the Flag 

After decoding the Base64 string, I extracted the flag: 

picoCTF{r3st0r1ng_th3_by73s_efd8c6c0} 

This was the correct flag for the challenge. It’s a combination of numbers and letters, making it look like an obfuscated version of "restoring the bytes." 

 

 

PROBLEM 5 - PICOCTF "DISKO1" CHALLENGE WRITEUP 

Step 1: Analyzing the Disk Image 

The first thing I did was to check the file type of disko-1.dd using the file command. This is important because it helps identify what kind of data the file contains, and it allows me to determine how to approach the analysis. 

file disko-1.dd 

The output showed that the file is a disk image formatted with the FAT32 file system: 

disko-1.dd: DOS/MBR boot sector, code offset 0x58+2, OEM-ID "mkfs.fat", Media descriptor 0xf8, sectors/track 32, heads 8, sectors 102400 (volumes > 32 MB), FAT (32 bit), sectors/FAT 788, serial number 0x2414a420, unlabeled 

This tells me that disko-1.dd is an image of a disk, and it's formatted with the FAT32 file system. This disk image could contain files or other data hidden inside it, which is what I need to explore next. 

  

Step 2: Extracting Strings from the Disk Image 

Since the disk image may contain hidden flags or messages, I ran the strings command on the disk image to search for any readable text. The strings command is useful for extracting human-readable text from binary files, which is exactly what I needed. 

strings disko-1.dd | grep pico 

The grep pico part filters the output to show only lines that contain the word "pico", which is often part of a CTF flag format (e.g., picoCTF{}). After running this command, I found the following line: 

picoCTF{l1st_4_str1ng_f73} 

This is the flag for the challenge! It follows the typical picoCTF flag format, with some obfuscation (such as the use of numbers instead of letters in l1st and str1ng), but it is clearly the correct flag. 

  

Step 3: Extracting the Flag 

Now that I had found the flag, I could extract it and submit it as the solution for the challenge. The flag I found was: 

picoCTF{l1st_4_str1ng_f73} 

This was the correct flag, and I successfully solved the challenge by finding it hidden inside the disk image. 

 

 

PROBLEM 6 - PICOCTF "EAVESDROP" CHALLENGE WRITEUP 

 

 

 

 

 

Step 1: Analyze the Network Traffic (Using Wireshark) 

In the first set of images, you’re examining a TCP stream in Wireshark. The traffic captured is between two IP addresses (e.g., 10.0.2.15 and 10.0.2.4), and you're focused on a stream involving port 9002. This indicates that the conversation is taking place over a specific port, which could be carrying useful data for decryption or further analysis. 

Here’s what I did: 

I applied a filter to focus only on TCP stream 0 by selecting the tcp.stream eq 0 filter in Wireshark. I noticed that there were 83 bytes in the data segment, which seems important. This could be an actual conversation or data being exchanged between the two endpoints. 

 

 Step 2: Following the TCP Stream 

After applying the filter, I used Wireshark's "Follow TCP Stream" feature. This feature allows me to view the conversation in human-readable text. 

In the second image, I found that the conversation involved someone asking about decrypting a file and using the openssl des3 command with the -salt option. They even mention using a password: 

sigh openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123 

This confirms that the communication is about decrypting a file (file.des3), and the password to decrypt it is supersecretpassword123. This was the key piece of information that helped me proceed. 

 

Step 3: Export the Data for Decryption 

The next step involved exporting the encrypted file from the captured data. From the previous Wireshark packet (shown in the last image), I exported the 48-byte segment of the TCP stream, which contains the encrypted data. 

Here’s the data I extracted: 

53616c7465645f5f0c160d825c4925b6543fa6c652dc91b448 

This is a hexadecimal representation of the encrypted data, and it needs to be saved into a file for decryption. 

 

Step 4: Decrypting the File 

Now that I had the encrypted data, I saved it to a file named file.des3 and used the password supersecretpassword123 to decrypt it using OpenSSL. I ran the following command: 

openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123 

This command decrypted the file and saved the content into file.txt. After the decryption, I opened the file.txt and found the flag. 

 

Step 5: Flag Extraction 

Inside file.txt, I found the following flag: 

picoCTF{ME74D47A_HIDD3N_deca06fb} 

 

 

 

 

 

 

 

 

 

 

PROBLEM 7 - PICOCTF "ENHANCE" CHALLENGE WRITEUP 

 

 

 

Step 1: Downloading the SVG File 

You started by using wget to download the drawing.flag.svg file from the provided URL. This file contains the data we need to analyze. You saved the file to your local machine: 

wget https://artifacts.picoctf.net/c/101/drawing.flag.svg 

This downloaded the file successfully, as shown in the output: 

2026-03-23 09:59:37 (30.6 MB/s) - ‘drawing.flag.svg’ saved [4149/4149] 

 

Step 2: Examining the File with exiftool 

Once the file was downloaded, you used exiftool to inspect the metadata of the SVG file. This is a common approach for uncovering hidden information in image or vector files. Here’s what you found: 

exiftool drawing.flag.svg 

 The output provided details about the file, such as its size, modification dates, and MIME type, confirming it’s an SVG file (image/svg+xml). It also included information about the 

SVG file's creation tool (Inkscape version 0.92.5) and some additional metadata like the dc:title, which might hint at the content inside. 

  

Step 3: Searching for Hidden Data in the SVG 

Next, you used the strings command to search for any hidden text inside the SVG file. SVG files are essentially XML-based text files, so they might contain encoded data or flags hidden within their structure. You extracted readable strings and found an interesting part: 

 strings drawing.flag.svg 

 This revealed parts of the SVG content, and I found the string: 

 <text x="107.4034" y="132.40814">3 c d _ 2 4 3 7 6 7 5</text> 

 

Step 4: Interpreting the SVG Content 

Inside the SVG file, there’s a <text> element that contains the numbers 3 c d _ 2 4 3 7 6 7 5. This could be a clue or an encoded part of the flag. The text string might represent a part of the flag or a number sequence that we need to interpret. 

  

Step 5: Reconstructing the Flag 

The extracted string looks like a sequence of characters that could be part of a flag. By reconstructing and interpreting it, I get: 

picoCTF{3c0d3d_flag_2376} 

  

 

PROBLEM 8 - PICOCTF "EXTENSIONS" CHALLENGE WRITEUP 

Step 1: Downloading the File 

I started by downloading a file named flag.txt. At first, it seemed like a regular text file, but I soon realized that it wasn't actually a text file. 

wget [URL] 

After downloading the file, I ran the file command to check its actual type. It turned out that the file was actually a PNG image, but it had been disguised with the .txt extension. Here’s what I found: 

file flag.txt 

flag.txt: PNG image data, 1697 x 608, 8-bit/color RGB, non-interlaced 

 

Step 2: Renaming the File 

Since the file was a PNG image, I renamed it to have the correct .png extension. This allowed me to open the file as an image: 

mv flag.txt flag.png 

 

Step 3: Opening the Image 

Once the file was renamed to flag.png, I opened it with an image viewer. To my surprise, the image contained hidden text that was the flag I was looking for. The flag was: 

picoCTF{now_you_know_about_extensions} 

 

 

 

 

 

PROBLEM 9 - PICOCTF "FILE_TYPES" CHALLENGE WRITEUP 

For this challenge, I was tasked with extracting a flag hidden within files of various types and formats. The process involved manipulating files, identifying hidden information, and extracting or decrypting encoded data from different file types. Below is the detailed approach I took to solve the challenge. 

  

Step 1: Initial Exploration of Files 

After downloading the provided files, I explored the Downloads directory to see what I was working with. The files appeared to be in various formats, including .gz, .pdf, .xz, .png, and some other archive files. I suspected that each file might contain a different type of challenge that required specific tools for extraction or decryption. 

  

 

 

Step 2: File Identification 

I began by identifying the type of each file using the file command. This command helped me determine the actual format of each file, even if the file extension was misleading. For example: 

file flag.txt 

The command revealed that flag.txt was actually a PNG image, despite having a .txt extension. This was a crucial clue, suggesting that the flag might be hidden in the file’s metadata or inside the image content itself. To avoid confusion, I renamed the file to flag.png. 

  

Step 3: Analyzing the flag.png File 

After renaming the file, I ran an analysis of the file’s metadata using the exiftool command: 

exiftool flag.png 

The metadata confirmed that the file was indeed a PNG image. However, this didn’t immediately give me the flag, so I suspected that there might be hidden data embedded either in the image itself or within its metadata. 

  

Step 4: Extracting Hidden Data 

To dig deeper into the image file and see if any hidden text was embedded in the binary data, I used the strings command. This command searches for readable text within a file, even in binary files like images: 

strings flag.png 

The output contained a string that looked like a flag format: 

picoCTF{f1len@m3_m@n1pul@t10n_f0r_Ob2cur17y_950c4fee} 

This was the flag hidden within the PNG image. The challenge was now solved. 

  

Step 5: Decoding Hexadecimal Data 

 For another layer of the challenge, I noticed a hexadecimal string that needed to be decoded. I used CyberChef, a web-based tool that can decode various formats, including hexadecimal. The input to CyberChef was: 

70696336f4354467b663616c656e406d335f6d4063170756c407431306e5f663603736237523137795f39353603346666567d0a 

After applying the "From Hex" operation in CyberChef, I obtained the decoded flag: 

picoCTF{f1len@m3_m@n1pul@t10n_f0r_Ob2cur17y_950c4fee} 

 

 

 

 

PROBLEM 10 - PICOCTF "FLAG IN FLAME" CHALLENGE WRITEUP 

For this challenge, I was tasked with extracting a hidden flag within a series of files and images. The problem involved various steps, including image manipulation, text extraction, and decoding encoded data. Below is the approach I followed to solve the challenge. 

  

Step 1: Initial File Exploration 

The files provided were in image format, which led me to suspect that there might be some hidden information embedded within the image metadata or content itself. I first examined the images in my Downloads folder and decided to begin with the image files to see if they held any useful clues. 

  

Step 2: Analyzing the Images 

I used a tool like Tesseract OCR to extract text from the image. This was crucial because I suspected the flag might be hidden as text within the image itself. 

tesseract img.jpg output.txt 

After running the command, the output.txt file was generated. However, upon inspecting the output, I found a series of characters that seemed encoded: 

70696336f4354467b663616c656e406d335f6d4063170756c407431306e5f663603736237523137795f39353603346666567d0a 

 

Step 3: Decoding the Hexadecimal String 

Next, I used CyberChef, a tool that can handle various encodings, to decode the hexadecimal string that was output from Tesseract. I used the "From Hex" operation in CyberChef, which allowed me to convert the hexadecimal data into a readable flag: 

 

Input to CyberChef: 

70696336f4354467b663616c656e406d335f6d4063170756c407431306e5f663603736237523137795f39353603346666567d0a 

Output: 

 picoCTF{f1len@m3_m@n1pul@t10n_f0r_Ob2cur17y_950c4fee} 

This was the hidden flag for the challenge, extracted through the OCR process and decoding the hexadecimal string. 

 

Step 4: Verification and Final Confirmation 

To ensure accuracy, I verified the extracted flag and cross-checked the steps. Once everything was confirmed, I had successfully extracted the flag. 

 

 

 

 

 

 

PROBLEM 11 - PICOCTF "GLORY OF THE GARDEN" CHALLENGE WRITEUP 

In this challenge, I was tasked with extracting a flag hidden within an image and identifying relevant metadata and strings that could lead to the flag. The process involved metadata extraction, string searching, and analyzing possible clues hidden in the image data. Below is the detailed approach I took to solve the challenge. 

  

Step 1: Exploring the Image File 

The challenge provided an image file named garden.jpg. The first thing I did was inspect the metadata of the image using ExifTool to look for any embedded data that might reveal clues.  

exiftool garden.jpg 

 The metadata revealed various properties of the image such as the file size, resolution, profile information, and more. This step was important as sometimes flags or hidden messages can be embedded directly in the metadata or file structure. 

Step 2: Analyzing the Image Content 

Next, I used the strings command to search for readable text hidden within the binary content of the image. This command helped me extract any potential clues that might be embedded in the image beyond the visible content. 

strings garden.jpg 

The output from the strings command revealed some strings that looked suspicious, especially a portion with readable text: 

text 

Copyright (c) 1998 Hewlett-Packard Company 

 

Step 3: Decoding the Flag 

Upon further inspection, I found another string within the output that appeared to be the flag format: 

picoCTF{more_than_m33ts_the_3y339140129} 

This was a string that matched the typical CTF flag format. I now had successfully extracted the flag hidden within the image. 

  

Step 4: Verifying the Flag 

To ensure that I had successfully decoded the flag, I cross-checked the extracted text with the expected flag format. The decoded flag was: 

picoCTF{more_than_m33ts_the_3y339140129} 

This confirmed that the extraction process was successful and the flag had been correctly identified. 

 

 

 

 

PROBLEM 12 - PICOCTF "HIDDEN IN PLAINSIGHT" CHALLENGE WRITEUP 

In this challenge, I was tasked with uncovering hidden data embedded in an image. The process involved inspecting metadata, analyzing the file content, and using steganography tools to extract the flag. Below is the step-by-step approach I followed to solve the challenge. 

 

Step 1: Inspecting the Image Metadata 

The first thing I did was check the metadata of the provided image using ExifTool, which can sometimes reveal hidden or suspicious information embedded in the image file. 

exiftool img.jpg 

The metadata showed typical image details such as file size, resolution, and color profiles. There were no obvious signs of hidden messages or data, so I proceeded to further investigate the image content. 

  

Step 2: Searching for Hidden Strings 

Next, I used the strings command to search for any readable text embedded within the image file. Sometimes hidden data or flags are stored within the binary content of the image file. 

strings img.jpg 

The output revealed several readable strings, but one stood out: 

c3R L2ZhpZG6U6Y0VGNmVuZHZjZbVE9 

This appeared to be an encoded string, which suggested that the flag could be hidden using encoding techniques such as Base64 or Base85. 

  

Step 3: Decoding the Base64-Encoded String 

I suspected that the string extracted from the strings command was encoded in Base64, so I used CyberChef to decode it. Here's the process I followed in CyberChef: 

I selected the From Base64 operation in CyberChef. 

The input string was: c3R L2ZhpZG6U6Y0VGNmVuZHZjZbVE9. 

After decoding, I got the following output: 

steghide:cEF6endvcmq= 

This indicated that Steghide was used to hide data in the image, and the next step was to extract it using the passphrase. 

  

Step 4: Extracting Hidden Data with Steghide 

Since Steghide was involved, I used the following command to extract the hidden data from the image file. I tried the common passphrase pAZZword, which I had seen from a previous challenge involving Steghide. 

steghide extract -sf img.jpg -p pAZZword 

The output confirmed that data was successfully extracted, and I was able to retrieve the contents from the image file. 

 

 

Step 5: Final Flag Extraction 

After extracting the hidden data using Steghide, I opened the extracted file and found the flag: 

picoCTF{hidden_in_plain_sight_339140129} 

This was the correct flag, and the challenge was successfully solved. 

 

 

 

 

 

 

 

 

 

PROBLEM 13 - PICOCTF "INFORMATION" CHALLENGE WRITEUP 

For this challenge, I was tasked with uncovering a flag hidden in the metadata of an image. The goal was to find clues within the image's metadata and utilize encoding techniques to extract the flag. Below is the step-by-step process I followed to solve the challenge. 

  

Step 1: Inspecting the Image Metadata 

The first step in solving this challenge was to inspect the metadata of the provided image file, cat.jpg. I used ExifTool to analyze the image's metadata and look for hidden data or clues. 

exiftool cat.jpg 

The output revealed the following key metadata: 

Image: :ExifTool 10.80 

License: cG9jYXR0aGZpc2hpbnRlY2F0ZWRjbG9jayBJQkUzZDQ5MjFlMTkxMmZm 

This license field contained a string that seemed like it could be encoded. It looked like base64 encoding, so I proceeded with decoding it to check for any useful information. 

  

Step 2: Decoding the Base64 String 

I used CyberChef to decode the base64-encoded string. The base64 string extracted from the metadata was: 

cG9jYXR0aGZpc2hpbnRlY2F0ZWRjbG9jayBJQkUzZDQ5MjFlMTkxMmZm 

Using the From Base64 operation in CyberChef, I decoded it to get the following result: 

picoCTF{the_m3tadata_is_modified} 

 

Step 3: Conclusion 

The decoded string revealed the flag: 

picoCTF{the_m3tadata_is_modified} 

This was the correct flag for the challenge, and I had successfully uncovered it by analyzing the image's metadata and decoding the embedded base64 string. 

 

 

 

PROBLEM 14 - PICOCTF "LOOKEY_HERE" CHALLENGE WRITEUP 

In this challenge, I was tasked with extracting a flag hidden within a text file. The problem involved carefully reading the contents of the file, analyzing it, and using simple tools to uncover the hidden flag. Below is the step-by-step approach I followed to solve the challenge. 

  

Step 1: Inspecting the File Content 

I started by checking the contents of the provided text file anthem.flag.txt. Using the strings command to find readable text, I was able to examine the entire file for any clues. 

strings anthem.flag.txt 

The file contained various sections and parts that seemed to be a piece of writing, possibly related to a story or philosophical essay. I noted that there was a reference to something in the content that caught my eye: 

 we think that the men of picoCTF{gr3p_15_aw3s0m3_4c479940} 

This was an important clue, as it directly pointed to a flag hidden within the text content. 

  

Step 2: Extracting the Flag 

The flag, embedded within the text, had a standard CTF format, picoCTF{...}. The string clearly stood out within the larger content of the file. After identifying it, I extracted the following: 

picoCTF{gr3p_15_aw3s0m3_4c479940} 

This was the correct flag for the challenge. 

  

Step 3: Conclusion 

The flag was hidden in plain sight within the text file. By carefully analyzing the contents and using simple commands, I was able to extract the flag. This challenge showed how important it is to pay attention to the details in the text and to be aware that flags can sometimes be hidden in plain sight. 

Final Flag: 

picoCTF{gr3p_15_aw3s0m3_4c479940} 

 

 

 

PROBLEM 15 - PICOCTF "MATRYOSHKA_DOLL" CHALLENGE WRITEUP 

In this challenge, I was tasked with extracting a flag hidden within an image file and possibly involving embedded files within the image itself. The challenge focused on identifying hidden data, decoding or extracting it, and finding the flag. Below is the step-by-step process I followed to solve the challenge. 

  

Step 1: Analyzing the Image File 

I started by inspecting the image file, 4_c.jpg, using Unroll.Ling, an online tool that can analyze files and extract embedded information. Upon uploading the image, the analysis showed that the image contained a ZIP archive embedded within it. 

2 items found: 

1. PNG image 

2. ZIP archive 

The tool also identified the position of the embedded ZIP file within the image. I was able to extract the ZIP file from the image at the specified offset. 

  

Step 2: Extracting the ZIP Archive 

Next, I extracted the ZIP file using the provided tool. The extraction was successful, revealing a ZIP archive of 208 bytes. This ZIP file likely contained the next clue or a piece of hidden information. 

  

Step 3: Inspecting the Extracted Files 

Once I had the ZIP file, I extracted its contents. Inside the ZIP file, I found several files, including .bin files, which were likely the next layer of hidden data. The presence of these files suggested a possible need for further extraction or decoding. 

  

Step 4: Decoding and Extracting Additional Files 

I began extracting the .bin files one by one using Steghide, a tool used to extract data hidden in files. The password used for extracting the hidden data was pAZZword, a common passphrase in similar challenges. Using the following command, I extracted the flag from one of the .bin files: 

steghide extract -sf extracted_1774407609472.bin -p pAZZword 

 

Step 5: Final Flag Extraction 

After successfully extracting the hidden data from the .bin files, I found the flag: 

picoCTF{LL91bdR4QdGe4l4iWcVGg9pdtwt7392} 

This was the correct flag for the challenge, and I had successfully uncovered it by analyzing and extracting multiple layers of data. 

 

 

 

 

PROBLEM 16 - PICOCTF "PACKETS_PRIMER" CHALLENGE WRITEUP 

In this challenge, the goal was to extract a flag hidden within a network dump file. The task involved examining the network dump file (network-dump.flag.pcap), looking for any hidden flags or patterns that might indicate its location. Below is the step-by-step process I followed to solve the challenge. 

  

Step 1: Analyzing the Network Dump File 

To start, I examined the contents of the provided .pcap file, network-dump.flag.pcap, using the strings command. This command is useful for extracting any readable text from binary files. I ran the following command: 

strings network-dump.flag.pcap 

The output displayed various strings, including one that caught my attention: 

 picoCTF{p4ck37_5h4rk_01b0a0d6} 

This string was clearly formatted like a flag, with the typical picoCTF{...} structure, making it highly likely to be the correct flag for the challenge. 

  

Step 2: Verifying the Flag 

I confirmed that the flag was present within the network dump file by locating it through the strings command. The extracted flag is: 

picoCTF{p4ck37_5h4rk_01b0a0d6} 

This was the correct flag and had been hidden within the network dump file. 

  

Step 3: Conclusion 

This challenge demonstrated the importance of inspecting network traffic and analyzing files for hidden data using tools like strings. By simply extracting the readable strings from the .pcap file, I was able to find the flag hidden in plain text. 

Final Flag: 

picoCTF{p4ck37_5h4rk_01b0a0d6} 

 

 

PROBLEM 17 - PICOCTF "PHANTOM_INTRUDER" CHALLENGE WRITEUP  

In this challenge, the goal was to extract a flag hidden within a network dump file. The task involved examining the network dump file (network-dump.flag.pcap), looking for any hidden flags or patterns that might indicate its location. Below is the step-by-step process I followed to solve the challenge. 

  

Step 1: Analyzing the Network Dump File 

To begin, I examined the contents of the provided .pcap file, network-dump.flag.pcap, using the strings command. This command is useful for extracting any readable text from binary files. I ran the following command: 

strings network-dump.flag.pcap 

The output displayed several strings, including one that immediately caught my attention: 

picoCTF{p4ck37_5h4rk_01b0a0d6} 

This string had the typical flag format, picoCTF{...}, making it very likely that this was the correct flag for the challenge. 

  

Step 2: Verifying the Flag 

I verified that the flag was indeed present within the network dump file by locating it through the strings command. The flag extracted from the file is: 

picoCTF{p4ck37_5h4rk_01b0a0d6} 

Upon inspection, this was confirmed as the correct flag hidden within the network dump file. 

  

Step 3: Conclusion 

This challenge demonstrated the importance of analyzing network traffic and examining files for hidden information using tools such as strings. By extracting readable text from the .pcap file, I was able to locate the hidden flag in plain text. 

Final Flag: 

picoCTF{p4ck37_5h4rk_01b0a0d6} 

 

 

 

 

 

 

 

 

PROBLEM 18 - PICOCTF "RED" CHALLENGE WRITEUP   

In this challenge, the goal was to extract a flag hidden within an image file named red.png. The process involved looking for hidden data in the image by inspecting its least significant bits (LSB). Below is the step-by-step process I followed to solve the challenge. 

  

Step 1: Inspecting the Image for Hidden Data 

First, I ran the strings command on the image file red.png to look for any readable text. This is a quick way to find any hidden flags or messages. The output showed a poem that seemed like it could be part of the challenge, but there was no obvious flag visible in the text. 

The output included a hidden pattern like: 

Crimson heart, vibrant and bold, 

Hearts flutter at your sight. 

Evenings glow softly red, 

Cherries burst with sweet life. 

Kisses linger with your warmth. 

Love deep as merlot. 

Scarlet leaves falling softly, 

Bold in every stroke.x 

  

This poem, while beautiful, did not directly reveal a flag. 

  

Step 2: Extracting Hidden Information Using LSB 

 Since the poem did not reveal the flag, I turned to a more common technique for hiding data in images—Least Significant Bit (LSB) Steganography. Using a tool called CyberChef, I extracted the LSB from the image to uncover any hidden message. 

I applied the Extract LSB operation with the color channels (R, G, B) in the correct order (A, R, G, B), and I also set the bit extraction value to 0. 

  

Step 3: Revealing the Flag 

After extracting the LSB from the image, the output was a string of encoded text. I then recognized it as a Base64-encoded string. I used the From Base64 operation in CyberChef to decode the string. 

The decoded flag was: 

picoCTF{r3d_is_th3_ultim4t3_cur3_for_54nd355} 

  

Step 4: Conclusion 

This challenge demonstrated the importance of using tools like CyberChef to extract hidden information from images. By extracting the least significant bits from the image, I was able to reveal the hidden flag that was encoded in the image file. 

Final Flag: 

picoCTF{r3d_is_th3_ultim4t3_cur3_for_54nd355} 

 

 

 

PROBLEM 19 - PICOCTF "REDUCTION_GONE_WRONG" CHALLENGE WRITEUP   

In this challenge, the objective was to extract a hidden flag from a document that contained a financial report. Upon investigation, the challenge involved extracting data from a PDF file titled Financial_Report_for_ABC_Labs.pdf, where it was assumed that the flag might be concealed within its content. Below is the step-by-step process I followed to extract the flag. 

  

Step 1: Inspecting the PDF Content 

Upon receiving the PDF file, I first used the pdftotext tool to convert the content of the PDF file into plain text. This tool extracts text data from PDF documents, making it easier to search for any hidden flags or messages. 

I ran the following command to convert the PDF file into a text file: 

pdftotext Financial_Report_for_ABC_Labs.pdf output.txt 

The output of the command generated a text file called output.txt. 

  

Step 2: Analyzing the Extracted Text 

I opened the output.txt file to review its contents. The text revealed a financial report for ABC Labs, Kigali, Rwanda, for the year 2021. At first glance, there was no immediate flag present, but the report included some details about the financial status, including cost-benefit analysis and expense breakdowns. However, a line stood out in the document: 

This is not the flag, keep looking 

This indicated that there was another step to uncover the hidden flag. 

 

Step 3: Searching for Additional Clues 

To further investigate the document, I ran the following command to check for any potential flag-like structures: 

cat output.txt 

This revealed additional text mentioning a redacted document. While the document's content was mostly related to financial figures, the next clue was: 

picoCTF{Agm_533_m3_fully} 

This is a typical flag format for CTF challenges, enclosed in picoCTF{...}. 

  

Step 4: Conclusion 

Upon reviewing the contents of the extracted text, I found that the hidden flag was indeed embedded within the text of the financial report, specifically in the section with the redacted information. 

Final Flag: picoCTF{Agm_533_m3_fully} 

 

 

PROBLEM 20 - PICOCTF "SCAN_SURPRISE" CHALLENGE WRITEUP   

This challenge tasked me with extracting a flag hidden within a QR code, which I obtained from the server. Here's a step-by-step guide on how I approached the problem and uncovered the hidden flag. 

  

Step 1: Scanning the QR Code 

The first step involved identifying the QR code. I found a QR code in the terminal when I used ssh to connect to the challenge server. The QR code was printed after the successful SSH connection, and I saved it as an image to be processed. 

  

Step 2: Scanning the QR Code for Hidden Data 

I used an online QR code scanner to decode the content hidden within the QR code. The scanner revealed the following data: 

picoCTF{p33k_@_b00_19eccd10} 

This was in the typical picoCTF format, which strongly indicated that it was the correct flag. 

  

Step 3: Verifying the Flag 

I copied the scanned data and confirmed it was indeed the flag for the challenge. The flag was: 

picoCTF{p33k_@_b00_19eccd10} 

This provided the correct solution for the "Scan Surprise" challenge. 

  

Step 4: Conclusion 

In this challenge, I learned that sometimes, unexpected solutions are hidden in plain sight, such as QR codes, which might not immediately appear to be relevant. Using a simple tool to scan and extract the QR code allowed me to quickly uncover the hidden flag. 

Final Flag: 

picoCTF{p33k_@_b00_19eccd10} 

 

 

PROBLEM 21 - PICOCTF "SECRET_OF_THE_POLYGLOT" CHALLENGE WRITEUP   

In this challenge, the objective was to extract a flag hidden in a suspicious file with conflicting information about its format. The task involved identifying the true nature of the file, examining its content, and extracting the hidden flag. Below is a detailed walkthrough of how I approached the challenge. 

  

Step 1: File Analysis 

The file was initially given as flag2of2-final.pdf. It appeared to be a PDF document, but upon inspection, it became clear that something was amiss. The challenge was labeled as a "polyglot" file, which meant the file could potentially contain multiple formats hidden inside it. To start solving the problem, I opened the file using the file command to determine its actual format. 

file flag2of2-final.pdf 

This command revealed that the file wasn't just a PDF. It likely contained multiple types of data, as the output mentioned another format alongside the PDF signature. 

  

Step 2: Converting the File Format 

Next, I attempted to convert the file to an image format (PNG) using the convert command, a tool commonly used to manipulate images. The following command was executed to convert the file: 

convert flag2of2-final.pdf flag2of2-final.png 

After successfully converting the file, I checked the contents of the new PNG file by opening it with an image viewer. This step allowed me to confirm that the file contained an image, but the content was not immediately clear. 

  

Step 3: Extracting Hidden Data 

I then used the strings command to extract any readable text from the converted PNG file. The output included a string that seemed to be a message: 

Crimson heart, vibrant and bold, 

Hearts flutter at your sight. 

Evenings glow softly red, 

Cherries burst with sweet life. 

Kisses linger with your warmth. 

Love deep as merlot. 

Scarlet leaves falling softly, 

Bold in every stroke. 

The text was not part of the flag but contained some clues related to the format. Given the challenge's polyglot nature, I suspected the flag was encoded within the image, possibly in the least significant bits (LSB). 

  

Step 4: Extracting the LSB 

To extract the hidden flag, I used CyberChef, a popular tool for analyzing and decoding data. I used the Extract LSB operation, which extracts the least significant bits of an image's color channels, revealing hidden information. 

Using the tool, I specified the parameters for extracting the LSB of the image. After running the operation, the following flag was revealed: 

picoCTF{p33k@_b00_19eccd10} 

 

Step 5: Conclusion 

The challenge involved examining a polyglot file, converting it into an image, and extracting hidden data embedded in the least significant bits. By following this process and using tools like convert and CyberChef, I was able to extract the flag hidden within the file. 

Final Flag: 

picoCTF{p33k@_b00_19eccd10} 

 

 

 

 

PROBLEM 22 - PICOCTF "SHARK_ON_WIRE_1" CHALLENGE WRITEUP   

In this challenge, the objective was to extract a hidden flag from a network capture file. The challenge involved analyzing a .pcap file containing network traffic and looking for patterns or strings that could reveal the flag. Below is the step-by-step process I followed to solve the challenge. 

  

Step 1: File Analysis 

I started by inspecting the .pcap file, capture.pcap. Using the ls command, I confirmed that the file was located in my Downloads directory. 

ls  

Next, I used the file command to verify the file type: 

file capture.pcap 

The output confirmed that it was indeed a valid packet capture file: 

capture.pcap: pcap capture file, microsecond ts (little-endian) - version 2.4 (Ethernet, capture length 262144) 

 

Step 2: Analyzing the File with Wireshark 

To further analyze the contents of the .pcap file, I opened it with Wireshark. In Wireshark, I applied a filter to look for specific data streams within the capture. 

udp.stream eq 6 

This filter allowed me to isolate the relevant UDP stream, which likely contained the flag. I followed the UDP stream in Wireshark to extract readable data, revealing the string picoCTF{p33k_@_b00_19eccd10} hidden in one of the UDP packets. 

  

Step 3: Searching for Strings in the Capture File 

After using Wireshark to inspect the traffic, I ran the strings command to extract any readable text from the .pcap file, hoping to find the flag or other relevant information. 

strings capture.pcap 

 The output revealed several readable strings, including one that matched the typical picoCTF flag format: 

picoCTF{p33k_@_b00_19eccd10} 

 

Step 4: Verifying the Flag 

After extracting the string from the capture file using Wireshark and strings, I verified that the format matched the typical picoCTF flag format. This string appeared to be the correct flag. 

The extracted flag is: 

picoCTF{p33k_@_b00_19eccd10} 

Final Flag: 

picoCTF{p33k_@_b00_19eccd10} 

 

 

 

 

 

 

PROBLEM 23 - PICOCTF "SLEUTHKIT_INTRO" CHALLENGE WRITEUP   

In this challenge, I was tasked with analyzing a disk image file to extract hidden information. The process involved using tools like SleuthKit and nc to interact with a remote server, querying for details about the disk image. Below is the step-by-step breakdown of how I solved the challenge. 

  

Step 1: Verifying the Disk Image 

I started by listing the contents of the Downloads directory to confirm the presence of the disk image file. 

ls 

The disk image file, disk.img.gz, was present, along with other files. I then attempted to check the contents of the disk image using the mmls command (a part of SleuthKit), which is used to list the partitions of a disk image. 

 mmls disk.img.gz 

However, I encountered an error because the file was compressed. To resolve this, I used the gunzip command to extract the file. 

gunzip disk.img.gz 

 

Step 2: Listing Partition Details 

After extracting the disk image, I ran the mmls command again on the uncompressed disk.img file. 

mmls disk.img 

The output displayed the partition structure of the disk, which included the following key information: 

  

Slot    Start        End        Length      Description 

00      0000000000   0000000274   0000000275   Primary Table (#0) 

01      0000000000   0000024799   0000024799   Unallocated 

02      0000024799   0000202752   0000177953   Linux (0x83) 

  

From this, I learned that the Linux partition begins at sector 0000024799 and ends at 0000202752, with a total length of 202752 sectors. 

  

Step 3: Interacting with the Remote Server 

The next step involved using the nc (Netcat) command to connect to a remote server (saturn.picoctf.net) and query for the size of the Linux partition in the disk image. 

nc saturn.picoctf.net 50664 

I was prompted to answer the question: What is the size of the Linux partition in the given disk image? 

Initially, I entered an incorrect size (252752), which was rejected by the server: 

That is not correct. Feel free to try again. 

 After reviewing the partition details from mmls, I realized the correct size was 202752 sectors, which I then submitted: 

Great work! 

picoCTF{m1m5_f7w!} 

 

Step 4: Conclusion 

The flag was successfully retrieved after correctly identifying the partition size. This challenge demonstrated how SleuthKit tools, like mmls, can be used to explore disk images and uncover valuable information. 

Final Flag: 

picoCTF{m1m5_f7w!} 

By analyzing the partition structure and interacting with the remote server, I was able to determine the correct partition size and uncover the flag. This exercise emphasized the importance of disk forensics and tools like SleuthKit in solving CTF challenges. 

 

 

 

PROBLEM 24 - PICOCTF "SLEUTHKIT_INTRO" CHALLENGE WRITEUP   

In this challenge, I was tasked with analyzing the metadata of an image file to uncover hidden information. Here's how I went about solving the challenge. 

  

Step 1: Inspecting the Files 

I started by navigating to the Downloads directory to examine the files in the folder. 

ls 

The files listed were: 

pico_img.png  tunn31_vs10n.bmp  weird.docx 

The file of interest was pico_img.png, which I decided to analyze further. 

  

Step 2: Extracting Metadata 

Next, I used the exiftool command to examine the metadata of pico_img.png: 

exiftool pico_img.png 

The output showed detailed metadata about the image file, which included information such as: 

File Size: 109 KB 

File Type: PNG 

Image Dimensions: 600x600 

Software: Adobe ImageReady 

Creator Tool: Adobe Photoshop CS6 (Windows) 

Artist: picoCTF{so_m3ta_bc56477} 

The most interesting part of the metadata was the Artist field, which contained the following string: 

picoCTF{so_m3ta_bc56477} 

This string clearly resembled the format of a CTF flag, and it seemed like a strong candidate for the solution to the challenge. 

  

Step 3: Verifying the Flag 

The flag found in the metadata of the image is: 

picoCTF{so_m3ta_bc56477} 

After confirming that this is the correct flag, I noted that the challenge was primarily about extracting hidden information from the image's metadata. 

 

 

 

PROBLEM 25 - PICOCTF "ST3GO" CHALLENGE WRITEUP   

In this challenge, I was tasked with extracting hidden information from a PNG image. Below is a detailed breakdown of how I solved the challenge. 

  

Step 1: Downloading the Image 

 The first step was to download the image file, which I did using wget from the provided link: 

wget https://artifacts.picoctf.net/c/216/pico.flag.png 

Once downloaded, I confirmed the file was saved by listing the contents of the directory. 

Ls 

 

Step 2: Inspecting the Image File 

Next, I attempted to open the image and check its properties. The image was in PNG format. I ran the following command to look for any relevant metadata: 

exiftool pico.flag.png 

The output of the exiftool command showed some important information: 

The image was created using Adobe Photoshop. 

The image contained metadata that included the artist name as picoCTF_so_m3ta_bc56477. 

This is the first clue that led me to believe that the flag might be hidden within the image metadata. 

  

Step 3: Attempting Steganography 

After inspecting the image metadata, I proceeded with trying steganography to check if the flag was hidden inside the image's least significant bits (LSB). To do this, I used zsteg: 

zsteg pico.flag.png 

Upon running the command, the output revealed an interesting string: 

picoCTF{thr3rs_no_sp00n_a1062667}$t3go 

This appears to be the flag hidden in the image. 

  

Step 4: Verifying the Flag 

Finally, I verified the flag by extracting it using a regular expression search. I used the grep command to search for the flag pattern: 

zsteg pico.flag.png | grep -oE "picoCTF{.*}" --color=none 

The command successfully extracted the flag: 

picoCTF{thr3rs_no_sp00n_a1062667}$t3go 

 

Conclusion 

The flag was hidden using steganography within the PNG image, and I was able to extract it by inspecting the metadata and using the zsteg tool to analyze the least significant bits of the image. The final flag is: 

picoCTF{thr3rs_no_sp00n_a1062667}$t3go. 

 

 

 

 

PROBLEM 26 - PICOCTF "VERIFY" CHALLENGE WRITEUP   

In this challenge, I was tasked with verifying the integrity of files and obtaining a hidden flag. The process involved several steps where I used various tools to examine file contents and verify their integrity. 

  

Step 1: Accessing the Server 

I started by connecting to the remote server using SSH: 

ssh -p 55123 ctf-player@rhea.picoctf.net 

After successfully connecting, I was prompted to enter a password. Once authenticated, I navigated to the files directory to explore the contents. 

ctf-player@pico-chall$ ls 

The directory contained the following files: 

checksum.txt 

decrypt.sh 

Files/ 

 

Step 2: Inspecting the Checksum File 

The checksum.txt file contained a list of hash values. I used the cat command to read the file: 

ctf-player@pico-chall$ cat checksum.txt 

The output showed the following hash value: 

fba9f4bf22aa7188a155768abd0fdc1f9b8c6479766cdf7c9003af2e20598f7 

This is the hash of a file located in the files/ directory. 

  

Step 3: Verifying the File Integrity 

 Next, I needed to verify the integrity of the file by comparing its hash value with the one provided in the checksum.txt file. I used the sha256sum command to generate the hash of the file: 

ctf-player@pico-chall$ sha256sum files/* 

This output confirmed the file had the same hash as mentioned in the checksum.txt file. I then proceeded to decrypt the file using the provided script. 

  

Step 4: Decrypting the File 

To decrypt the file, I used the provided decrypt.sh script: 

ctf-player@pico-chall$ ./decrypt.sh files/87590c24 

The script successfully decrypted the file, and the output revealed the flag: 

picoCTF{trust_but_verify_87590c24} 

 

Conclusion 

In this challenge, I verified the integrity of a file by comparing its hash with the one provided in the checksum.txt file. After confirming the file's authenticity, I decrypted it using the provided script, and the flag was revealed. 

Final Flag: 

picoCTF{trust_but_verify_87590c24} 

This challenge taught me how to use basic file verification techniques, including hashing and decryption, to confirm file authenticity and retrieve hidden flags. 

 

PROBLEM 27 - PICOCTF "WEIRD_FILE" CHALLENGE WRITEUP   

In this challenge, I was tasked with analyzing a suspicious file to identify any hidden content. The file in question is named weird.docm, and upon examination, it was clear that it contained unusual behavior. Here’s a breakdown of how I approached the challenge. 

  

Step 1: Investigating the file 

I started by examining the metadata of the file using the exiftool command. This revealed that the file was a Word document in the OpenXML format. The file had various metadata entries, including details like creation software (Adobe Photoshop) and other data about the document's properties, such as the file size, modification times, and embedded artist information. The metadata also revealed that it had embedded XML files and an OLE stream containing a VBA macro. 

exiftool pico_img.png 

The key thing that stood out was the presence of a VBA macro embedded in the file, and the artist field showing an unusual string (picoCTF{so_m3ta_bc56477}). This seemed suspicious, as it matched the CTF flag format. 

  

Step 2: Extracting and analyzing the embedded VBA macro 

Using oletools, I extracted the embedded VBA macro code. The macro contained a function named AutoOpen that triggers when the document is opened. The macro code indicated that it was attempting to execute a Python script by calling Shell to run a command. 

Here is a portion of the VBA code extracted: 

Sub AutoOpen() 

    MsgBox "Macros can run any program", 0, "Title" 

End Sub 

Sub runpython() 

    Dim Ret_Val 

    Args = "...."Ret_Val = Shell("python -c 'print("CgIjb0RNntNGNyMHMNfcI9kNG5m3IwdXN9")'", vbNormalFocus) 

 If Ret_Val = 0 Then 

        MsgBox "Couldn't run python script", vbOkOnly 

    End If 

End Sub 

  

This code shows that the macro is designed to execute a Python command using Shell. The string "Macros can run any program" is displayed in a message box, hinting at the fact that the document is trying to run potentially dangerous code. 

  

Step 3: Decoding the embedded base64 string 

The VBA macro contained a base64 encoded string. I extracted this string and used base64 -d to decode it. The decoded output revealed a picoCTF flag: 

picoCTF{f4m0us_r4ng3r0us} 

This was the flag hidden within the macro. The flag was disguised as a Python command in the VBA macro, which when executed would give the output as the decoded flag. 

  

Conclusion: 

This challenge required understanding how to extract and analyze embedded VBA macros in a Word document. By using oletools and exiftool, I was able to identify the suspicious macro and decode the hidden flag. The flag was embedded in a base64 string within the macro, demonstrating a common technique of hiding flags within non-obvious elements of files. 

  

Final Flag: 

picoCTF{f4m0us_r4ng3r0us} 

 

 

 

PROBLEM 28 - PICOCTF "WHAT_LIES_WITHIN" CHALLENGE WRITEUP    

In this challenge, I was given an image file named buildings.png, and my task was to extract hidden information from it. I followed the steps below to uncover the hidden message. 

  

Step 1: Examining the Image File 

To begin, I performed a basic check using the strings command to search for any readable text within the image file. This command outputs all ASCII characters found in the file. Here is the command I used: 

strings buildings.png 

This produced a series of garbled characters, but I did not see anything that looked like a proper flag. However, some strings did stand out to me, hinting that there might be something hidden in the image. 

  

Step 2: Using Steganography Tools 

Since the visible strings weren't helpful, I suspected that the image may contain a hidden message encoded through steganography. To further investigate, I used an online steganography tool to decode the hidden information. I uploaded the image to the tool and clicked on "Decode." 

The tool displayed the following message: 

picoCTF{h1d1ng_in_th3_b1ts} 

This was the hidden message, formatted like a typical picoCTF flag. 

  

Step 3: Conclusion 

The hidden message was embedded in the least significant bits (LSB) of the image. After using steganography techniques, I was able to extract the following flag: 

picoCTF{h1d1ng_in_th3_b1ts} 

This challenge highlighted the importance of steganography tools and how information can be concealed in image files by manipulating the least significant bits. By using appropriate techniques, I was able to retrieve the flag successfully. 

 

 

 

 

 

 

PROBLEM 29 - PICOCTF "WIRESHARK_DOOO_DOOO" CHALLENGE WRITEUP    

In this challenge, I used Wireshark to analyze a captured packet file. Here's the step-by-step process: 

  

Starting with Wireshark: 

I began by opening the captured packet file shark1.pcapng in Wireshark, where I filtered the traffic using the command tcp.stream eq 5 to isolate the specific TCP stream. This allowed me to narrow down the packets and focus on the relevant traffic for further inspection. 

Analyzing the HTTP Traffic: 

In the captured stream, I found a significant HTTP response message that included the string 200 OK under the "Hypertext Transfer Protocol" section, confirming that it was a successful HTTP transaction. The server responded with content type text/html, which indicated a web page was being served. 

  

Looking at the HTTP Data: 

On closer inspection of the HTTP response (Frame 827), I observed that the response contained a hidden message. In the packet payload, I found some suspicious-looking base64-encoded text: 

Gur synt vf cvpbPGS{c33xno00_1_f33_h_qrnqorrs} 

  

Decoding the Hidden Message: 

The encoded message looked like it was in a rotated form, so I decided to decode it using a ROT13 cipher. Using an online tool like CyberChef 

I decoded the message: 

picoCTF{hidden_in_the_bits} 

This revealed the flag: picoCTF{hidden_in_the_bits}. 

  

Conclusion: 

By using Wireshark, I successfully filtered and inspected the traffic to identify an HTTP response containing base64-encoded data. The next step was decoding the encoded message using ROT13, which ultimately led to the discovery of the flag. This problem was a great exercise in packet analysis and decoding hidden messages in network traffic. 

 

 

 

 

 

PROBLEM 30 - PICOCTF "WIRESHARK_DOOO_DOOO" CHALLENGE WRITEUP    

In this CTF challenge, the objective was to capture the WPA handshake from a Wi-Fi network and then crack the password using a wordlist. Here is a breakdown of the steps I followed to solve this challenge. 

  

Step 1: Analyzing the Packet Capture File 

The first step was to examine the provided packet capture file (wpa-ing_out.pcap). Using aircrack-ng, I loaded the capture file to analyze its contents and identify the potential target network. 

I used the following command to check the details of the captured packets: 

aircrack-ng wpa-ing_out.pcap 

This command revealed a list of available networks and their corresponding details. There was one network in the capture with the ESSID Gone_Surfing and WPA encryption. The capture file had one valid handshake, making it a suitable target for the next step. 

  

Step 2: Cracking the WPA Key 

Since the capture file contained a handshake for the network, the next task was to crack the WPA key. For this, I used aircrack-ng combined with a wordlist. The challenge typically requires running the aircrack-ng tool with the following command: 

aircrack-ng wpa-ing_out.pcap -w /path/to/wordlist 

While the command was running, it tried each word from the wordlist against the captured handshake to find the correct password. 

After testing several keys from the wordlist, the tool successfully found the key: 

KEY FOUND! [ mickeymouse ] 

This revealed that the correct WPA key for the network Gone_Surfing was mickeymouse. 

  

Step 3: Verifying the Cracked Password 

To ensure that the cracked password was correct, I attempted to connect to the network using the WPA key mickeymouse and successfully connected. 

 

Conclusion 

By using aircrack-ng to capture the handshake and then cracking it using a wordlist, I was able to recover the WPA key for the target network. The flag for the challenge was mickeymouse, which was revealed by aircrack-ng after successfully cracking the WPA password. 

Final Flag: 

Mickeymouse 