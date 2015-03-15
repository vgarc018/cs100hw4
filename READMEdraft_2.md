#The Boost Library Tokenizer

A tokenizer takes a sequence such as a string and turns it into a sequence of tokens, which are meaningful character strings.
Tokenizing makes life easier when you want to break up a string.
Luckily, boost provides a tokenizer library that can do this.
For example, you can take this string

`string str = "Don't panic, too much."`

which will be broken up into the tokens:
```
Don
t
panic
too
much
```
Notice how the `'` `,` and the `.` were not outputted.

##How do we do this?

Let's just go straight into the code:

***ex_1.cpp***
```
#include <iostream>
#include <string>
#include <boost/tokenizer.hpp>

using namespace std;
using namespace boost;

int main() {
    string str = "Don't panic, too much. 12,34";
    tokenizer<> mytok(str);
    for(auto it = mytok.begin(); it != mytok.end(); ++it)
        cout << *it << endl;
    return 0;
}
```

Running this, we get:
```
$ g++ -std=c++11 ex_1.cpp -o ex_1
$ ./ex_1
Don
t
panic
too
much
12
34
```
>*Note:* The `auto` feature is part of the c++11 standard, so check out this tutorial if you need help with that (insert tutorial link in here later).
But if you don't have c++11, then you'll have to replace `auto` with `tokenizer<>::iterator`.

So first, in order to use boost tokenizer we need to include the library.

```
#include <boost/tokenizer.hpp>
using namespace boost;
```
And then in order to call the function we declare:

`tokenizer<> mytok(str);`

where `mytok` is the name of the tokenizer and `str` is the sequence of characters that we want to parse.
And finally, we use iterators to traverse through the tokens.
However, the way this tokenizer is parsing isn't particularly useful.
But we can control how the boost tokenizer parses by defining something which is known as a *delimiter.*

##So what's a delimiter and why is it useful?
***How the tokenizer uses the delimiter***

A delimiter is a sequence of one or more characters that separate text, and a tokenizer looks at what delimiters are passed in so that it can know where to separate the tokens.
So for the previous tokenizer example, we can say that the default delimiters are white space and all non-letter/number characters.
Just for reference, our string was:

`string str = "Don't panic, too much. 12,34";`

To explain in detail how this string got parsed, it should be noted that `tokenizer<>` parses by characters.
So this tokenizer will go through each character in the string and it will check to see if it is in the delimiter set.
Once it finds a match, the tokenizer will know that everything up until that character is a token so `Don` becomes a token since it finds `'`, which is a non-letter/number character.
It will then move on to creating the next token and once it finds the space after `t`, it creates the token `t`.
And so this continues on until the tokenizer has gone through the whole string.

***Defining our own***

But again, we want to know how to pass in our own delimiters, so let's work with this string to see how we can define our own:

`string str = "Thou&&&&art&as     fat&as&&butter";`

The boost tokenizer function will need to know that we're making our own delimiter, so we use `char_separator<char>` from the boost library like this:

`tokenizer< char_separator<char> > mytok(str, delim);`

>*Note:* There are other separators that can be used, but for this particular tutorial we're going to work with `char_separator<char>`.
But check [the other separators](www.boost.org/doc/libs/1_57_0/libs/tokenizer/index.html) out if you want to see what else you can do with boost tokenizer.

Notice how when we declare our `mytok` tokenizer there is an extra parameter, `delim`, which is the name of our delimiter set.
So we'll need to declare and specify what our delimiters will be beforehand and we do this like:

`char_separator<char> delim("&");`

where `&` is the thing the tokenizer will look for to separate the tokens.
It's also very important to note that the `char_separator<char>` tokenizer model is also only working with characters, and we'll see in a moment why this matters.
So let's see an example of this:

***ex_2.cpp***
```
#include<iostream>
#include<string>
#inlcude<boost/tokenizer.hpp>

using namespace std;
using namespace boost;

int main() {
    string str = "Thou&&&&art&as    fat&as&&butter";
    char_separator<char> delim("&");
    tokenizer< char_separator<char> > mytok(str, delim);

    for(auto it = mytok.begin(); it != mytok.end(); ++it)
        cout << "token: " << *it << endl;

    return 0;
}
```

Running this we get:
```
$ g++ -std=c++11 ex_2.cpp -o ex_2
$ ./ex_2
token: Thou
token: art
token: as    fat
token: as
token: butter
```

Notice how even though there were multiple `&`'s, all of them were not outputted.
But now this doesn't look like something useful since white space isn't being ignored.

***Multiple characters in our delimiter***

The `char_separator<char>` model of boost tokenizer is able to take in multiple characters.
So let's change our delimiter set in ex_2.cpp to this:

`char_separator<char> delim(" &");`

Let's compile the code with this change and then execute it:
```
$ g++ -std=c++11 ex_2.cpp -o ex_2
$ ./ex_2
token: Thou
token: art
token: as
token: fat
token: as
token: butter
```

Now this looks better, but keep in mind that this delimiter set does not contain *all* white space.
Try looking up [ASCII character codes](http://www.petefreitag.com/cheatsheets/ascii-codes/) for other types of white space (and other miscellaneous characters that you might need).

***Repeated characters in our delimiter***

What if our delimiter set contains repeats of the same character?
We explained earlier how a tokenizer will match the text with the delimiters to know where to separate tokens, and because this boost tokenizer deals with only characters, the tokenizer will look in the set and match a character to a character.
So if a character showed up one time in the set, then the tokenizer will know that this marks the separation of tokens.
Any other subsequent appearances of this character won't mean anything since the tokenizer would have already parsed the token after seeing the first one.
This is why we keep referring to the `char_separator<char>` as the delimiter set, since it acts like a set.

So if you wanted to parse a string like,

`ls dir || cat file | tr a-z A-Z`

and you didn't want to include the pipe `|` in your delimiter, having it contain `||` will *not* be recognized as something distinct to look for in parsing.
Here's code to show what happens:

***ex_3.cpp***
```
#include <iostream>
#include <string>
#include <boost/tokenizer.hpp>

using namespace std;
using namespae boost;

int main() {
    string str = "ls dir || cat file | tr a-z A-Z";
    char_separator<char> delim(" ||");
    tokenizer< char_separator<char> > mytok(str, delim);

    for(auto it = mytok.begin(); it != mytok.end(); ++it)
        cout << *it << " ";
    cout << endl;

    return 0;
}
```
```
$ g++ -std=c++11 ex_3.cpp -o ex_3
$ ./ex_3
ls dir cat file tr a-z A-Z
```

***Default delimiters***

What happens though if we didn't put anything into our delimiter?
The default delimiter set for this type of tokenizer will contain only white space.
Also, the non-letter/number characters are each treated as a token.
Notice that this is different from `tokenizer<>`.

Here's an example demonstrating this point:

***ex_4.cpp***
```
#include <iostream>
#include <string>
#include <boost/tokenizer.hpp>

using namespace std;
using namespace boost;

int main() {
    string str = "12*34 I, declare a&&thumb    !war";
    char_separator<char> delim;
    tokenizer< char_separator<char> > mytok(str, delim);

    //This also behaves the same
    //tokenizer< char_separator<char> > mytok(str);

    for(auto it = mytok.begin(); it != mytok.end(); ++it)
        cout << *it << " ";
    cout << endl;

    return 0;
}
```
```
$ g++ -std=c++11 ex_4.cpp -o ex_4
$ ./ex_4
12 * 34 I, declare a & & thumb ! war
```

##Going beyond with char_separator<char>
Here are the basics of making a tokenizer with your own delimiters:

 - Create your own delimiter:

  `char_separator<char> delim("/*delimiters*/");`

 - Make a tokenizer function:

  `tokenizer< char_separator<char> > mytok(str, delim);`

 - Your delimiter set can take in multiple characters as delimiters.
 - Repeated characters do not make a difference in parsing.

But it might be useful to know that `char_separator<char>` has options for outputting delimiters and empty tokens.
This is where boost tokenizer really differentiates from the `strtok` function since it can do neither of these.

>*Note:* If you're not familiar with `strtok`, check out this [tutorial](https://github.com/mikeizbicki/ucr-cs100/tree/2015winter/textbook/assignment-help/strtok).

***Kept delimiters***

So far, we have been working with the *dropped delimiters* of `char_separator<char>`, which are the delimiters that do not become tokens.
`char_separator<char>` actually has another parameter to specify the *kept delimiters*, which will be the delimiters that do become tokens.
This will look like:

`char_separator<char> delim(DROPPED_DELIM, KEPT_DELIM);`

For example, we could have:

`char_separator<char> delim("^| ", ";");`

which will separate tokens by the delimiters `^| ;` and will also make `;` a token.
