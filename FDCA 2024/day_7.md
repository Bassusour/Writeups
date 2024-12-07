# Dag 7
Vi fik givet følgende problembeskrivelse:

> Cyber-Operations Command Centeret er efter flere dages hårdt arbejde gået fuldstændig sukkerkold, så kokken Andrimner finder dokumentet med æbleskivernes lokation frem fra skuffen - men hov! Jætterne har tilsyneladende krypteret teksten!? Heldigvis finder Andrimner krypteringsrunerne, som skurkene i deres hast ud af køkkenets server har tabt i filsystemet. <br/>
Kan du dekryptere æbleskiver.runes?

## Solution
Der blev udleveret en zip mappe, som indeholdte en `.dll` fil og en `.runes` fil. `.runes` filen indeholdte en base64 encoded streng, som ikke gav noget læseligt hvis man prøvede at afkode det. 
Ved at bruge dnSpy til at kigge på `.dll` filen, fandt man frem til noget meget interresant kode, som kan ses forneden.

```C#
public static string encrypt(string ymir)
{
    byte[] array = new byte[32];
    Array.Copy(Encoding.UTF8.GetBytes(ymir), array, ymir.Length);
    byte[] array2 = new byte[32];
    for (int i = 0; i < 8; i++)
    {
        byte[] array3 = new byte[4];
        Array.Copy(array, i * 4, array3, 0, 4);
        Array.Copy(BitConverter.GetBytes(-BitConverter.ToInt32(array3, 0)), 0, array2, i * 4, 4);
    }
    return Convert.ToBase64String(array2);
}
```

En meget simpel krypteringsmetode. Vi kan lave vores egen dekrypteringsmetode, der gør det modsatte et trin ad gangen.

```C#
static string decrypt(string encrypted){
    // Først afkod fra base64
    byte[] array2 = Convert.FromBase64String(encrypted);

    // Lav samme for-loop, der laver de modsatte handlinger
    byte[] array = new byte[32];
    for (int i = 0; i < 8; i++)
    {
        byte[] array3 = new byte[4];
        Array.Copy(array2, i * 4, array3, 0, 4); // Vigtigt at bruge array2
        Array.Copy(BitConverter.GetBytes(-BitConverter.ToInt32(array3, 0)), 0, array, i * 4, 4); 
        // Det modsatte af at konvertere til et negativt tal, er at gøre det igen. 
    }
    
    return Encoding.UTF8.GetString(array);
}
```

Ved at køre `decrypt` med strengen fra `.runes` filen, får vi flaget, som er `FDCA{D3-L1gG3r-i-mU5p3Lh31M}`. 