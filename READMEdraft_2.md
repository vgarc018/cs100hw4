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
```
#include <iostream>
#include <string>
#include <boost/tokenizer.hpp>

using namespace std;
using namespace boost;

int main() {
    string str = "Don't panic, too much.";
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
```
So first, in order to use boost tokenizer we need to include the library.
And then in order to call the function we declare:
```
tokenizer<> mytok(str);
```
where `mytok` is the name of the tokenizer and `str` is the sequence of characters that we want to parse.

>*Note*: The `auto` feature is part of the c++11 standard, so check out this tutorial if you need help with that (insert tutorial link in here later).
But if you don't have c++11, then you'll have to replace `auto` with `tokenizer<>::iterator`.

However, the way this tokenizer is parsing isn't particularly useful.
By default, this tokenizer will not output white space and non-letter/number characters.
But we can control this by defining something which is known as the *delimiter.*

##So what's a delimiter and why is it useful?
A delimiter is a set of one or more characters that separate text and a tokenizer looks at these delimiters so that it can tell where to separate the tokens.

So let's work with this string:
```
string str = "Thou&&&&art&as     fat&as&&butter";
```
In order to make our own delimiter, the boost tokenizer function will need to know that we're making our own.
So we pass in `char_separator<char>` like this:
```
tokenizer< char_separator<char> > mytok(str, delim);
```
There are other separators that can be used, but for this particular tutorial we're going to work with `char_separator<char>`.
But check [the other separators](www.boost.org/doc/libs/1_57_0/libs/tokenizer/index.html) out if you want to see what else you can do with boost tokenizer.

Notice how when we declare our `mytok` tokenizer, there is an extra parameter, `delim`, which is the name of our delimiter.
So we'll need to declare and specify what our delimiter will contain beforehand and we do this like:
```
char_separator<char> delim("&");
```
where `delim` is the name of our delimiter and for this particular example, `&` is the thing the tokenizer will look for to separate the tokens.
