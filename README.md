## SANS Holiday Hack Challenge Write-Ups

2021: [write-up website](https://n00.be/HolidayHackChallenge2021/)
2020: [write-up website](https://n00.be/HolidayHackChallenge2020/)  
2019: [write-up website](https://n00.be/HolidayHackChallenge2019/) or [YouTube playlist](https://www.youtube.com/playlist?list=PLkC9YoWVx3xKJgL7TrBsjmy8triY9RDjC)  
2018: [PDF write-up](files/CraHan%20-%20KringleCon%202018%20writeup.pdf) 

```python
#!/usr/bin/env python
"""SANS Holiday Hack Challenge 2021 - Bruteforce Frostavator."""
import asyncio
import itertools
import random
from typing import Optional, Tuple

import aiohttp
import click
import tqdm

GATES = ['AND', 'NAND', 'OR', 'NOR', 'XOR', 'XNOR']


def create_ascii_board(layout: list) -> str:
    """Create an ASCII panel layout."""
    g0 = f"{GATES[layout[0]]:^6}"
    g1 = f"{GATES[layout[1]]:^6}"
    g2 = f"{GATES[layout[2]]:^6}"
    g3 = f"{GATES[layout[3]]:^6}"
    g4 = f"{GATES[layout[4]]:^6}"
    g5 = f"{GATES[layout[5]]:^6}"

    return (
        "Logic board layout:\n\n"
        "  +------+   +------+   +------+\n"
        f"  |{g0}|   |{g1}|   |{g2}|\n"
        "  +------+   +------+   +------+\n"
        "  +------+   +------+   +------+\n"
        f"  |{g3}|   |{g4}|   |{g5}|\n"
        "  +------+   +------+   +------+"
    )


async def fetch(
        session: aiohttp.ClientSession,
        perm: Tuple[int, ...]) -> Tuple[Optional[str], Tuple[int, ...]]:
    """Have the Frostavator check a single permutation."""
    url = "https://frostavator21.kringlecastle.com/check"
    headers = {'dataType': 'json', 'contentType': 'application/json'}
    data = {'id': "abc-123", 'config': perm}

    async with session.post(url, headers=headers, json=data) as response:
        resp = await response.json()
        return resp.get('hash', None), perm


async def bruteforce(pick_one: bool):
    """Bruteforce the Frostavator."""
    async with aiohttp.ClientSession() as session:
        # Calculate all possible permutations for the 6 logic gates
        perms = itertools.permutations([0, 1, 2, 3, 4, 5])
        tasks = []

        # Send out a '/check' request for each permutation
        for perm in perms:
            tasks.append(asyncio.create_task(fetch(session, perm)))

        # Configure the progressbar
        bar_format = "{l_bar}{bar:30}| {n_fmt}/{total_fmt} [{elapsed}]"
        progressbar = tqdm.tqdm(
            asyncio.as_completed(tasks),
            total=len(tasks),
            bar_format=bar_format
        )
        progressbar.set_description("Frostavating")
        results = []

        # Gather the results
        for step in progressbar:
            results.append(await step)

        # Filter out invalid values
        valids = [x[1] for x in results if x[0]]

        if pick_one:
            # Print one random entry
            winner = list(valids[random.randrange(0, len(valids))])
            print(f"frostavatorData: {winner}")
            print(create_ascii_board(winner))
        else:
            # Print all entries
            print(f"All {len(valids)} valid 'frostavatorData' values:")

            for valid in valids:
                print(f"{list(valid)} or {[GATES[x] for x in valid]}")


@click.command()
@click.option('--pick-one', is_flag=True, help='Show a single solution.')
def cli(pick_one: bool):
    asyncio.run(bruteforce(pick_one))


if __name__ == '__main__':
    cli()
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
