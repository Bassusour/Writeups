# Dag 6

Vi fik givet følgende problembeskrivelse:

> Vi har modtaget kritisk efterretning fra Asgård: Jætterne har stjålet hemmelige dokumenter fra gudernes arkiver, og de planlægger at bruge informationerne til at ødelægge Yggdrasil én gang for alle. Det eneste spor vi har, er et billede fundet efterladt i en af Jætternes skjulte lejr. Odin mener, at dette billede kan indeholde skjulte data, der kan føre os til de stjålne dokumenter. <br>
Din opgave er at undersøge billedet for skjulte informationer. Kan du finde ud af, hvad Jætterne har gemt? Jætterne har været snedige og sat flere forhindringer på vejen. Held og lykke, for Asgårds skæbne afhænger af dig!

Billedet kan ses herunder
![Yggdrasil](Yggdrasil.jpeg)

## Solution
Problembeskrivelsen hintede rigtig meget til steganography, som er kunsten at skjule information inde i et object, som fx et billede. 

Jeg brugte kommandoen `steghide extract -sf Yggdrasil.jpeg` uden at angive en passphrase, som lykkedes i at finde en zip mappe, kaldt `SuperHemmeligt.zip`. Denne zip-mappe indeholdte nogle knap så vigtige filer, såsom `Drømmekage opskrift.docx`, men den indeholdte også filer i en mappe kaldt `HemmeligePlaner`. 

Dog var filerne under `HemmeligePlaner` krypterede, så det var ikke muligt at unzippe det normalt. Derfor brugte jeg `zip2john` for at lave en hash som john the ripper kunne bruge til at bruteforce koden til de krypterede filer.

>zip2john SuperHemmeligt.zip > hash.txt
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

Det fandt hurtigt frem til at `ragnarok` var passphrasen til de krypterede filer. Nu skulle man blot unzippe med den rigtige passphrase, og så havde man alle de filer man har brug for. 

Under `HemmeligePlaner` mappen fandtes nogle `.jpeg` filer og en `.docx` fil. I word filen stod der hints til, at jætterne kommunikerede gennem metadata i billedefiler, hvorefter jeg fandt en `Author` property i `.jpeg` billderne som var `base64` encoded. Ved at afkode dem, indeholdte en af billederne flaget, som var `FDCA{my_pr3c1ous_d4t4}`