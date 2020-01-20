## SANS Holiday Hack Challenge Write-Ups

2019: [write-up website](https://n00.be/HolidayHackChallenge2019/) or [YouTube playlist](https://www.youtube.com/playlist?list=PLkC9YoWVx3xKJgL7TrBsjmy8triY9RDjC)  
2018: [download](files/CraHan%20-%20KringleCon%202018%20writeup.pdf) PDF write-up

```python
#!/usr/bin/env python3
"""SANS Holiday Hack Challenge 2019 - Recover Cleartext Document."""
from Crypto.Cipher import DES

seed = 0


def rand():
    """Generate random value."""
    # 1. get seed value
    # 2. multiply seed by 214013
    # 3. add 2531011 (new seed value)
    # 4. right shift
    # 5. bitwise AND with 32767
    global seed
    seed = (214013 * seed + 2531011)
    val = seed >> 16
    return (val & 32767)


def generate_key(val):
    """Generate encryption key."""
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
    # File names
    encinfile = 'ElfUResearchLabsSuperSledOMaticQuickStartGuideV1.2.pdf.enc'
    pdfoutfile = 'ElfUResearchLabsSuperSledOMaticQuickStartGuideV1.2.pdf'

    # Friday, December 6, 2019 7:00:00 PM
    start = 1575658800

    # Loop over a 2-hour time frame
    for x in range(7200):
        keyseed = start + x
        key = generate_key(keyseed)
        bytekey = bytearray.fromhex(key)

        # Prep for decrypting DES-CBC
        cipher = DES.new(
            bytekey,
            DES.MODE_CBC,
            iv=bytearray.fromhex('0000000000000000')
        )

        # Read encrypted data
        f = open(encinfile, 'rb')
        encrypted = f.read()

        # Decrypt using the current key
        msg = (cipher.iv + cipher.decrypt(encrypted))

        # Check if decryption was successful
        if msg[9:12] == b'PDF':
            # Yes, we got a PDF!
            print(f'Pass {x}: {key} decrypts to a PDF!')
            f = open(pdfoutfile, 'wb')
            f.write(msg)
            break
        else:
            # Womp womp! On to the next.
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
