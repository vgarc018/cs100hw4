#The Boost Library Tokenizer

A tokenizer takes a sequence such as a string and turns it into a sequence of tokens, which are meaningful character strings.
Tokenizing makes life easier when you want to break up a string.
Luckily, boost provides a tokenizer library that can do this.
For example, you can take this string
```
string str = "Don't panic, too much."
```
which will be broken up into the tokens:
```
Don
t
panic
too
much
```
Notice how the `'` `,` and the `.` were not outputted.

##So how do we do this?

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
```
tokenizer<> mytok(str);
```
where `mytok` is the name of the tokenizer and `str` is the sequence of characters that we want to parse.
And finally, we use iterators to traverse through the tokens.
However, the way this tokenizer is parsing isn't particularly useful.
But we can control how the boost tokenizer parses by defining something which is known as the *delimiter.*

##So what's a delimiter and why is it useful?
***How the tokenizer uses the delimiter***

A delimiter is a set of one or more characters that separate text, and a tokenizer looks at what characters are in the delimiter so that it can know where to separate the tokens.
So for the previous tokenizer example, we can say that the delimiter contains white space and all non-letter/number characters.
Just for reference, our string was:
```
string str = "Don't panic, too much. 12,34";
```
To explain in detail how this string got parsed, the tokenizer will go through each character in the string and it will check to see if it is in the delimiter set.
Once it finds a match, the tokenizer will know that everything up until that character is a token so `Don` becomes a token since it finds `'`, which tokenizer knows is in the delimiter.
It will then move on to creating the next token and once it finds `,` it creates the token `panic`.
And so this continues on until the tokenizer has gone through the whole string.

***Defining our own***

But again, we don't want this as our delimiter, so let's work with this string to see how we can define our own:
```
string str = "Thou&&&&art&as     fat&as&&butter";
```
The boost tokenizer function will need to know that we're making our own delimiter, so we pass in the separator from the boost tokenizer library `char_separator<char>`:
```
tokenizer< char_separator<char> > mytok(str, delim);
```
>*Note:* There are other separators that can be used, but for this particular tutorial we're going to work with `char_separator<char>`.
But check [the other separators](www.boost.org/doc/libs/1_57_0/libs/tokenizer/index.html) out if you want to see what else you can do with boost tokenizer.

Notice how when we declare our `mytok` tokenizer, there is an extra parameter, `delim`, which is the name of our delimiter.
So we'll need to declare and specify what our delimiter will contain beforehand and we do this like:
```
char_separator<char> delim("&");
```
where `&` is the thing the tokenizer will look for to separate the tokens in this particular example.

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

Recall that we defined the delimiter as being a set of one or more characters, so obviously we can add more characters into this set.
So let's change our delimiter to this:
```
char_separator<char> delim(" &");
```
Now, let's compile the code with this change and then execute it:
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

Now this looks like something that could be useful, but keep in mind that this is not ignoring *all* white space.
Try looking up [ASCII character codes](http://www.petefreitag.com/cheatsheets/ascii-codes/) for other types of white space (and other miscellaneous characters).

***Repeated characters in our delimiter***

What if we had our delimiter contain repeats of the same character?
Well since it's a set, multiple characters only count as one.
So nothing much changes, but here's an example to show what happens:

***ex_3.cpp***
```
#include <iostream>
#include <string>
#include <boost/tokenizer.hpp>

using namespace std;
using namespae boost;

int main() {
    string str = "Get*&**thee to*a * nun-nery";
    char_separator<char> delim("* &&*---");
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
Get thee to a nun nery
```

What happens though if we didn't put anything into our delimiter?
The default delimiter for this type of tokenizer will contain only white space, i.e. white space will not become a token.
Also, the non-letter/number characters are each treated as a token.
Notice that this is different from `tokenizer<>`.

Here's an example demonstrating this point:

***ex_3.cpp***
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

    //Also the same as not even declaring delim and writing
    //tokenizer< char_separator<char> > mytok(str);

    for(auto it = mytok.begin(); it != mytok.end(); ++it)
        cout << *it << " ";
    cout << endl;

    return 0;
}
```
```
$ g++ -std=c++11 ex_3.cpp -o ex_3
$ ./ex_3
12 * 34 I, declare a & & thumb ! war
```
