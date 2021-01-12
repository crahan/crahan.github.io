## SANS Holiday Hack Challenge Write-Ups

2020: [write-up website](https://n00.be/HolidayHackChallenge2020/)
2019: [write-up website](https://n00.be/HolidayHackChallenge2019/) or [YouTube playlist](https://www.youtube.com/playlist?list=PLkC9YoWVx3xKJgL7TrBsjmy8triY9RDjC)  
2018: [download](files/CraHan%20-%20KringleCon%202018%20writeup.pdf) PDF write-up

```python
#!/usr/bin/env python3
"""SANS Holiday Hack Challenge 2020 - Naughty/Nice List Part 1"""
from mt19937 import mt19937, untemper
from mt19937predictor import MT19937Predictor
from naughty_nice import Block, Chain


def extract_number_64(tliston):
    # grab 2 32-bit random values
    next1 = tliston.extract_number()
    next2 = tliston.extract_number()
    # transform them into a 64-bit value
    bytes = bytearray()
    bytes += next2.to_bytes(4, byteorder='big')
    bytes += next1.to_bytes(4, byteorder='big')
    return int.from_bytes(bytes, byteorder='big')


if __name__ == '__main__':
    # load the blockchain file
    c2 = Chain(load=True, filename='blockchain.dat')

    # print some blockchain stats
    print(f'The chain contains {len(c2.blocks)} blocks')
    print(f'First block has index {c2.blocks[0].index}')
    print(f'Last block has index {c2.blocks[-1].index}')
    
    # get the nonce values from the blocks
    nonce_list = []
    for block in c2.blocks:
        nonce_list.append(block.nonce)

    # create a kmyk MT19937 PRNG predictor 
    # https://github.com/kmyk/mersenne-twister-predictor
    kmyk = MT19937Predictor()

    # create a Tom Liston MT19937 PRNG predictor 
    # https://github.com/tliston/mt19937
    tliston = mt19937(0)

    # use all but the final 5 nonces for kmyk
    for nonce in nonce_list[:-5]:
        kmyk.setrandbits(nonce, 64)

    # use 312 64-bit nonces, excluding the final 5, for tliston
    idx = 0
    for nonce in nonce_list[-317:-5]:
        # least significant 32-bit
        tliston.MT[idx] = untemper(nonce & 0xFFFFFFFF)
        # most significant 32-bit
        tliston.MT[idx+1] = untemper((nonce >> 32) & 0xFFFFFFFF)
        idx += 2

    # generate the next 5 values and compare them to the final 5 in the blockchain
    print('\nVerifying correctness using the last 5 blockchain nonces:')
    print('\nIndex   kmyk              tliston           Blockchain        Check')
    for i in range(5):
        # blockchain verification value
        nonce_next = nonce_list[-5+i]
        # next kmyk value
        kmyk_next = kmyk.getrandbits(64)
        # next tliston value
        tliston_next = extract_number_64(tliston)
        # comparison table
        print('%i  %16.16x  %16.16x  %16.16x  %r' % (
            c2.blocks[-5+i].index,
            kmyk_next,
            tliston_next,
            nonce_next,
            (kmyk_next == tliston_next == nonce_next)
        ))
        assert(kmyk_next == tliston_next == nonce_next)

    # predict the next 4 random values
    print('\nGenerating the next values:')
    print('\nIndex   kmyk              tliston')
    for i in range(4):
        # kmyk answer
        kmyk_next = kmyk.getrandbits(64)
        # tliston answer
        tliston_next = extract_number_64(tliston)
        # prediction table
        print('%i  %16.16x  %16.16x' % (
            c2.blocks[-1].index + i + 1,
            kmyk_next,
            tliston_next,
        ))
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
