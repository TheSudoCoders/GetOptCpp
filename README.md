[![Open Source Love](https://badges.frapsoft.com/os/v2/open-source.svg?v=103)](https://github.com/TeamSudoCoders/GetOptCpp) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
# GetOptC++
GetOptC++ is a C++11 header file that parses command line arguments. It's inspired mostly by Golang's `flag` package, although you shouldn't expect the same quality from here.
It's a monolithic header file, so the only thing you really need to use GetOptC++ is the `getoptc++.hpp` file in this repository. That's it. Really.

## Usage
Simply download `getoptc++.hpp`, and add it into your project. Include the file, and you are off to the races!

By default, GetOptC++ operates in `STRICT` mode, meaning only commands like these are valid:
```
./my-executable -a 0 --bee="Hello!" -c "hmm" --delta=90 OPTION1 OPTION2 OPTION3 OPTION4
```
Commands in the following form will be rejected:
```
./my-executable -a=0 --bee "Hello!" -c="hmm" --delta 90
```

GetOptC++ throws errors, but most of the time, all errors are related to the parsing of the user's flags. Hence, it might be wise to test your application thoroughly if you are using GetOptC++, so that you can catch specific errors related to the user's flags. Other than standard library errors, GetOptC++ has the following errors:
- `no_argv`
- `impossible_case_reached`

The errors are pretty self-explanatory. Known standard library errors thrown:
- `std::out_of_range` - Happens when the user places many flags that require furthur input, but does not provide them
- `std::invalid_argument` - Happens when a user places a string into an input that requires a number

If there are more errors, please open an issue to inform us.

## Examples
```cpp
#include "getoptc++.hpp"
#include <iostream>

// Example main function to use with getoptc++
int main(int argc, char** argv) {
    GetOpt go(argc, argv); //main arguments argc and argv.
    //A third argument, isFromSystem, also exists. That will
    //set whether argv comes from a caller-generated function
    //or system-generated function. Specifically, it controls
    //whether getoptc++ skips the first argument, which is
    //usually the executable of the program.
    //GetOpt is a type alias of __impl_GetOpt<int, char, double, std::string>
    //You can also do:
    //GetOpt go;
    //go.initialize(argc, argv);
    //They are equivalent statements.

    //The flagBoolean function is usually called like this.
    //The string argument is separated by commas, and they
    //state the different names the flag can have.
    //Note that the last name that is parsed will have precedence
    //over all the previous names. This means that calling
    //-b --boolean --alias at the same time will give --alias
    //the precedence.
    bool& a_bool_variable = *go.flagBoolean("-b,--boolean,--alias");

    //By providing a second argument, the caller can control
    //the default value that the flag will contain. This is also
    //the value of the int should there be no such flag in existance.
    //If not set, the default values are: 0 or "".
    int& an_int_variable = *go.flagInt("-i,--integer", 10);
    double& a_double_variable = *go.flagFloating("-f,--floating,--float,--decimal", 0.0);
    std::string& a_str_variable = *go.flagString("-s,--string", "");

    //Once you are done setting the flags, you call:
    go.readArgs();
    //This function returns a std::vector<std::string>, which contins the
    //flags that getoptc++ failed to parse. It is ok to ignore it.
    //Pass an argument (either STRICT or AS_IS) to change it's behaviour. By default, it is on STRICT.

    //This function gets the options parsed by the previous call to go.readArgs
    //The type it returns is std::vector<std:string>
    auto options = go.getOptions();

    std::cout << "a_bool_variable: " << std::boolalpha << a_bool_variable << std::noboolalpha << std::endl;
    std::cout << "an_int_variable: " << an_int_variable << std::endl;
    std::cout << "a_double_variable: " << a_double_variable << std::endl;
    std::cout << "a_str_variable: " << a_str_variable << std::endl;

    std::cout << "Options: ";
    for (auto&& it : options) std::cout << it << " ";
    std::cout << std::endl;

    return 0;
}
```
and a version without comments because I know I write too much:
```cpp
#include "getoptc++.hpp"
#include <iostream>

int main(int argc, char** argv) {
    GetOpt go(argc, argv);

    bool& a_bool_variable = *go.flagBoolean("-b,--boolean,--alias");
    int& an_int_variable = *go.flagInt("-i,--integer", 10);
    double& a_double_variable = *go.flagFloating("-f,--floating,--float,--decimal", 0.0);
    std::string& a_str_variable = *go.flagString("-s,--string", "");

    go.readArgs();

    auto options = go.getOptions();

    std::cout << "a_bool_variable: " << std::boolalpha << a_bool_variable << std::noboolalpha << std::endl;
    std::cout << "an_int_variable: " << an_int_variable << std::endl;
    std::cout << "a_double_variable: " << a_double_variable << std::endl;
    std::cout << "a_str_variable: " << a_str_variable << std::endl;

    std::cout << "Options: ";
    for (auto&& it : options) std::cout << it << " ";
    std::cout << std::endl;

    return 0;
}
```

Trying it with the flags: `-bi 100 option another_one? "quotes work too" --string="string"` will yield the output:
```
a_bool_variable: true
an_int_variable: 100
a_double_variable: 0
a_str_variable: string
Options: option another_one? quotes work too
```

## License
Licensed under [MIT License](https://github.com/TeamSudoCoders/GetOptCpp/blob/master/LICENSE.md).

## Contributing
Sure! Fork the repository, modify it, and then submit a pull request! The aim of this project is to be as quick to use as possible, so that developers won't need to build a giant library just to use a single feature. Hence, if possible, all contributions should stick to the theme of only using header (`.h` or `.hpp`) files!
