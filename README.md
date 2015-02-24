# Boost Tokenizer Tutorial

The boost libraries are a set of libraries that are designed to provide various helpful applications for things such as 

The boost library `tokenizer` , takes a sequence such as a string, and parses it into tokens or substrings.  For example, you can take this string,

    string str = "Don't panic, too much."
 which will be broken up into the tokens:
 

    Don
    t
    panic
    too
    much
 Notice how the `'` `,`and the `.` were ignored.

**So how do we do this?** 
 First, in order to use the tokenizer we'll have to declare a few things:
 

    #include <boost/tokenizer.hpp>
    using namespace boost;
    
So now that we have what we need, let's start coding.

    #include <iostream>
    #include <string>
    #include <boost/tokenizer.hpp>
    using namespace std;
    using namespace boost;
    
    int main() {
	    string str = "Don't panic, too much.";
	    tokenizer<> tok(str);
	    for(auto it = tok.begin(); it != tok.end(); ++it)
		    cout << *it << endl;
		return 0;
	}
Once compiled we will get the output that was shown above where the `'` `,` and the `.` were ignored. 

> Note: When compiling, you'll want to compile with `g++ -std=c++11 <src code>` since we're using the `auto` feature that is included in the c++11 standard.  `auto`will automatically decide what data type your variable should be.  Since we have `auto it = tok.begin()`, then it will become an iterator since `tok.begin() `returns an iterator.  
> 
> If your compiler doesn't handle c++11 then you would have to replace `auto` with `tokenizer::iterator it`. 
 
