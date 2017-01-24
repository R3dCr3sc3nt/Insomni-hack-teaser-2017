# cryptoquizz

## Challenge

> Hello, young hacker. Are you ready to fight rogue machines ? Now, you'll have to prove us that you are a genuine cryptographer.
>
> Running on quizz.teaser.insomnihack.ch:1031

## Solution

    $ nc quizz.teaser.insomnihack.ch 1031

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    ~~ Hello, young hacker. Are you ready to fight rogue machines ?    ~~
    ~~ Now, you'll have to prove us that you are a genuine             ~~
    ~~ cryptographer.                                                  ~~
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

    ~~ What is the birth year of Lars Knudsen ?

After `nc`-ing a few more times, it becomes clear that the prompt always asks for a birthyear of a notable cryptographer and/or mathematician, and then promptly disconnects. Since the connection only stays open for <= 2 seconds, this is challenge requires scripting.

I noticed that there was name repetition every ~15 times that I netcat-ed, so the list of cryptographers must be finite. I ran netcat 200 times and collected all of the unique names in a text file.

```
for i in {1..200}
do
  nc quizz.teaser.insomnihack.ch 1031 | grep year | cut -d " " -f 8- > names.txt
done
cat names.txt | sort | uniq > unique_names.txt
```

`unique_names.txt` ended up having 64 names. While a dynamic solution with API calls might have been more elegant, the fatest solution is to just use Google/Wikipedia. With a few minutes of googling, I had the birth year for all the cryptographers in the [list](unique_names.txt).

The final step requires setting up `stdin` and `stdout` pipes for netcat. Since this is cubersome in bash, I wrote a python script leveraging the [socket](https://docs.python.org/3/library/socket.html) module.

```
import socket
import time

def netcat(hostname, port, ppl):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((hostname, port))

    while 1:
        data = s.recv(8000)
        print(data)
        if data == "":
            break

        if not (data.startswith("\n\n~~ What") or data.startswith("\n~~ What")):
            continue

        time.sleep(0,1)
        person = data.strip().split(" ")
        person = " ".join(person[7:-1])        
        year = ppl[person]
        s.send(year)

    s.close()

hostname = "quizz.teaser.insomnihack.ch"
port = 1031
ppl = {}
with open("unique_names.txt", "r") as f:
    for line in f:
        name, year = line.rsplit(" ", 1)
        ppl[name] = year

netcat(hostname, port, ppl)
```

After 8 rounds in the loop, the flag is revealed.

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    ~~ OK, young hacker. You are now considered to be a                ~~
    ~~ INS{GENUINE_CRYPTOGRAPHER_BUT_NOT_YET_A_PROVEN_SKILLED_ONE}     ~~
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
