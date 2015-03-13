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


