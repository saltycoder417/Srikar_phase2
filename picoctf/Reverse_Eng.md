# 1. GDB Baby step 1:

Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.

## Solution:

They gave a file that was in binary. Based on the challenge description, they told we have find what is in the eax registrar at the end of main function. So i disassembled the file using IDA, and searched for eax, there was a hexadecimal number over there. We have to convert it to decimal and then kept that in the flag. 

![screenshot of solution](/images/Screenshot 2025-10-24 233007.png)

## Flag:

```
picoCTF{549698}
```

## Concepts learnt:

I learned we can use IDA to disassemble files to assembly language and use it to understand how the code works. I learned how to slightly infer from assemble language.

## Notes:

I first tried using online disassemblers, but they didnt work properly. 

## Resources:

IDA to disassemble the file, and also youtube videos given in JTP-2 pdf. https://youtube.com/watch?v=1d-6Hv1c39c, https://youtube.com/watch?v=gh2RXE9BIN8. 
