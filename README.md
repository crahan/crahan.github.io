## SANS Holiday Hack Challenge Write-Ups

2018: [download](files/CraHan%20-%20KringleCon%202018%20writeup.pdf) PDF write-up  
2019: [write-up website](https://n00.be/HolidayHackChallenge2019/) or [YouTube playlist](https://www.youtube.com/playlist?list=PLkC9YoWVx3xKJgL7TrBsjmy8triY9RDjC)


```python
#!/usr/bin/env python3
from Crypto.Cipher import DES

seed = 0


def rand():
    global seed
    seed = (214013 * seed + 2531011)
    val = seed >> 16
    return (val & 32767)


def generate_key(val):
    global seed
    seed = val
    encrypted = []
    for _x in range(8):
        tmp = hex(rand())
        if len(str(tmp)) == 6:
            encrypted.append(str(tmp)[4:])
        elif len(str(tmp)) == 5:
            encrypted.append(str(tmp)[3:])
        elif len(str(tmp)) == 4:
            encrypted.append(str(tmp)[2:])
        elif len(str(tmp)) == 3:
            encrypted.append(f"0{str(tmp)[-1]}")
    return ''.join(encrypted)


def main():
    encinfile = 'ElfUResearchLabsSuperSledOMaticQuickStartGuideV1.2.pdf.enc'
    pdfoutfile = 'ElfUResearchLabsSuperSledOMaticQuickStartGuideV1.2.pdf'
    start = 1575658800

    for x in range(7200):
        keyseed = start + x
        key = generate_key(keyseed)
        bytekey = bytearray.fromhex(key)
        cipher = DES.new(
            bytekey,
            DES.MODE_CBC,
            iv=bytearray.fromhex('0000000000000000')
        )

        f = open(encinfile, 'rb')
        encrypted = f.read()
        msg = (cipher.iv + cipher.decrypt(encrypted))

        if msg[9:12] == b'PDF':
            print(f'Pass {x}: {key} decrypts to a PDF!')
            f = open(pdfoutfile, 'wb')
            f.write(msg)
            break
        else:
            print(f'Pass {x}: {key} is no bueno!')


if __name__ == "__main__":
    main()
```

## Random

> Folks that throw dirt on you aren't always trying to hurt you, and folks that pull you out of a jam aren't always trying to help you. But the main point is when you're up to your nose in shit, keep your mouth shut.
> 
> -- <cite>Jack Beauregard</cite>

## Elsewhere

- [Twitter](https://www.twitter.com/crahan)
- [Instagram](https://instagram.com/crahan)
- [Github](https://github.com/crahan)
- [Pinboard](https://pinboard.in/u:crahan)
