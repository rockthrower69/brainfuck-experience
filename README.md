# Pushing the limits of brainfuck
I knew before that these weird programming languages like brainfuck, whitespace, malbolge, etc. existed, but never had the guts to experiment with them. Today I was bored and was reading into these languages again. I quickly gave up on malbolge and whitespace, but after reading the brainfuck documentation I had to give that a try. I learned it consisted of only 8 instructions. By just reading what these instructions did I didn't get any smarter on how to write a program with them.

## Great brainfuck-tool
I found [this](https://fatiherikli.github.io/brainfuck-visualizer/) great site visualising how brainfuck worked. It shows exactly how Brainfuck went trough the instructions and updated the memory. Every site that I visited contained an "Hello world" example in Brainfuck. [Wikipedia](https://en.wikipedia.org/wiki/Brainfuck) even explained how this Hello world example works. After reading this explenation a few times I was sure of myself that I could write my own program

## First attempt
After fiddling around a bit with the Hello world example I decided to write my own program. It shall print "Brainfuck!". First I had to search for all the values of the letters in an ASCII table. Since I'm pretty good with Javascript I pressed *F12* to open the Javascript debugconsole in my browser. In less than a minute I wrote this simple program to return all the charcodes of the letters.

    var x = 'Brainfuck!', y = [];
    for(var i = 0; i < x.length; i++) {
        y.push(x.charCodeAt(i));
    }
    console.log(y); // [66, 114, 97, 105, 110, 102, 117, 99, 107, 33]

Now I could get started. I just had to count to the numbers and print them. I figured the best way to do this was to put 3 numbers in the memory to begin with. 64 (8x8), 100 (10x10) and 110 (10x11). This is the whole program I came up with.

    ++++++++[>++++++++<-]>>>      1: 64
    ++++++++++[<++++++++++>-]>    2: 100
    ++++++++++[<+++++++++++>-]    3: 110
    <<<++.       1: 66  = B
    >>++++.      3: 114 = r
    <---.        2: 97  = a
    ++++++++.    2: 105 = i
    >----.       3: 110 = n
    <---.        2: 102 = f
    >+++++++.    3: 117 = u
    <---.        2: 99  = c
    ++++++++.    2: 107 = k
    <[--<+>]<.   0: 33 = !
*I commented the values of the memory at the end of the code. First the memory location, then the value.*

It worked! Damn was I happy. Now I'd like to minify this program. Since I'd take longer to select and remove the 13 lines of commentary I also wrote a Javascript program for this.

    var x = document.getElementById('source').value, y = '';
    for(var i = 0; i < x.length; i++) {
        y += ('+-<>[],.'.split('').indexOf(x.charAt(i))!==-1)?x.charAt(i):'';
    }
    console.log(y);

This returned this code.

    ++++++++[>++++++++<-]>>>++++++++++[<++++++++++>-]>++++++++++[<+++++++++++>-]<<<++.>>++++.<---.++++++++.>----.<---.>+++++++.<---.++++++++.<[--<+>]<.
Which are only 147 bytes. Not bad I think. I found a [text-to-brainfuck tool](https://copy.sh/brainfuck/text.html) online which generated this code.

    ++++[++++>---<]>-.---[----->+<]>-.+++[->+++<]>++.++++++++.+++++.--------.-[--->+<]>--.+[->+++<]>+.++++++++.+[++>---<]>-.
With only 120 bytes it's 20% shorter. This is because the tool uses more loops which makes its shorter but more complicated. Another [another brainfuck interpreter](https://copy.sh/brainfuck/) showed me my script used way less instructions to achieve the same result. By using only 4 loops my script counted only 684 instructions whilst the generated script uses 4.213 instructions. That are 516% times as much instructions! I think it is a good tradeoff to have a program a little bit longer using way less CPU resources.

## My next step
Now I was ready for more challenging programs. First, a simple alphabet program

    +++++[>+++++<-]>+                  1: 26 = length of alphabet
    >+++++ +++++ +[>+++++ ++++<-]>--   3:97 = a
    <<[->>.+<<]                        Loops over all the letters by incrementing and displaying 3, and decreasing 1
Minified this code is only 60 bytes. In this case the generator returns a code of 69 bytes. This is because it doesnt use a loop to go trough the alphabet but prints the letters one by one.

## The ultimate challenge
The first thing you learn in class, studying whatever kind of programming language, is printing custom-width christmas trees to the output. Let's see if I can pull this off...

    >+++++                     1: 5 // This number of plusses represents the width of the tree
    >>+<<                      3: 0
    >>>>>                      just moving the pointer
    +++++                      6: 5
    [<                         loop 5 times
        <++>+++++ ++           4: add2 / 5: add7 / 6: min1
        >-]                    end loop
                               memory now is 0/5/0/1/10/35
    <<<<<                      moving the pointer again to 1
    [-                         start newline loop
        [>+<<+>-]              1: min1 / 2: add1 / 0: plus1
        <[->+<]>               0: min1 / 1: add1
        >[                     start space loop
            >>>---.+++<<<      print space (charcode 32)
        -]<                    end space loop
        >>
        [>>>+>+<<<<-]          4: add1 / 5: add1 / 1: min1
        >>>>[-<<<<+>>>>]<<<<   5: min1 / 1: add1
        >>>
        [                      start hashtag loop
            <.>                print hashtag ( charcode 35)
            -]                 end hashtag loop
        <<<
        ++                     3: add2
        <<
        >>>.<<<                print a newline (10)
    ]                          end newline loop

This program took me more than an hour of debugging and thinking, and it isn't even done! It does work, but only prints the top triangle. The only thing left is to create the tree trunk.

    >>-
    [<+>--]<--
    >+++[-<                    loop 3 times for the height of the trunk
        [<<+>+>-]              copy trunk distance from 2 to 0 part 1
        <[->+<]>               copy trunk distance from 2 to 0 part 2
        <<
        [                      start space loop
            >>>>>---.+++<<<<<  print space
        -]                     end space loop
        >>>>>...<.<<<<         print hashtag and newline
        >>
    >]

The complete program of the tree and trunk together are this 191-byte code when minified.

    >+++++>>+>>>+++++[<<++>+++++++>-]<<<<<[-[>+<<+>-]<[->+<]>>[>>>---.+++<<<-]>[>>>+>+<<<<-]>>>>[-<<<<+>>>>]<[<.>-]<<<++>.<<<]>>-[<+>--]<-->+++[-<[<<+>+>-]<[->+<]<[>>>>>---.+++<<<<<-]>>>>>...<.<]
These 191 bytes took me over an hour of thinking to code.

## What now?
I really wanted to push the limits. Maybe I could even create a program to a [Project Euler](https://projecteuler.net/) problem. After looking into the first problem my hope disappeared. All the problems require numbers bigger than 255, which can't be stored in 1 byte. Since Brainfuck uses 1-byte memory the only solution to this problem is to calculate with 2 memory cells. This is a step too far for me. This would result in a extremely complex program for a really simple problem. I did write the code for the first problem in javascript again tough, but I won't release that program since everyone should solve the project euler's problems himself in my opinion.

What problems can I solve then? I just made a Fibionacci-program showing all the numbers (until 255).

    >+
    [>
    <<[>>+>+<<<-]>>
    >[-<<<+>>>]<
    <[>+>+<<-]>
    >[-<<+>>]<
        These 3 lines are from some site to turn the bytes into readable numbers.
        >>++++++++++<<[->+>-[>+>>]>[+[-<+>]>+>>]<<<<<<]>>[-]>>>++++++++++<[->-[>+>>]>[+[-
        <+>]>+>>]<<<<<]>[-]>>[>++++++[-<++++++++>]<.<<+>+>[-]]<[<[->-<]++++++[->++++++++
        <]>.[-]]<<++++++[-<++++++++>]<.[-]<<[-<+>]<
    >>++++++++++.[-]<<
    ]
##Conclusion
After this I had given up on Brainfuck. Altough I haven't completed my goal of solving a project euler's problem with Brainfuck, I am still proud of what I did achieve. I learned a lot creating this programs which was worth the experience in my opinion. At least I can say I manage to code Brainfuck now...
