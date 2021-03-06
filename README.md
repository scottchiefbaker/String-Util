# NAME

String::Util -- String processing utility functions

# DESCRIPTION

String::Util provides a collection of small, handy functions for processing
strings in various ways.

# INSTALLATION

    cpanm String::Util

# USAGE

No functions are exported by default, they must be specified:

    use String::Util qw(trim eqq contains)

alternately you can use `:all` to export **all** of the functions

    use String::Util qw(:all)

# FUNCTIONS

## collapse($string)

`collapse()` collapses all whitespace in the string down to single spaces.
Also removes all leading and trailing whitespace.  Undefined input results in
undefined output.

**Note:** `crunch()` is an alias to this function. It is considered deprecated.
It may be removed in future versions.

    $var = collapse("  Hello     world!    "); # "Hello world!"

## hascontent($scalar), nocontent($scalar)

hascontent() returns true if the given argument is defined and contains
something besides whitespace.

An undefined value returns false.  An empty string returns false.  A value
containing nothing but whitespace (spaces, tabs, carriage returns, newlines,
backspace) returns false.  A string containing any other characters (including
zero) returns true.

`nocontent()` returns the negation of `hascontent()`.

    $var = hascontent("");  # False
    $var = hascontent(" "); # False
    $var = hascontent("a"); # True

    $var = nocontent("");   # True
    $var = nocontent("a");  # False

## trim($string), ltrim($string), rtrim($string)

Returns the string with all leading and trailing whitespace removed.
Trim on undef returns "".

    $var = trim(" my string  "); # "my string"

ltrim() trims **leading** whitespace only.

rtrim() trims **trailing** whitespace only.

## nospace($string)

Removes **all** whitespace characters from the given string. This includes spaces
between words.

    $var = nospace("  Hello World!   "); # "HelloWorld!"

## htmlesc($string)

Formats a string for literal output in HTML.  An undefined value is returned as
an empty string.

htmlesc() is very similar to CGI.pm's escapeHTML.  However, there are a few
differences. htmlesc() changes an undefined value to an empty string, whereas
escapeHTML() returns undefs as undefs.

## jsquote($string)

Escapes and quotes a string for use in JavaScript.  Escapes single quotes and
surrounds the string in single quotes.  Returns the modified string.

## unquote($string)

If the given string starts and ends with quotes, removes them. Recognizes
single quotes and double quotes.  The value must begin and end with same type
of quotes or nothing is done to the value. Undef input results in undef output.
Some examples and what they return:

    unquote(q|'Hendrix'|);   # Hendrix
    unquote(q|"Hendrix"|);   # Hendrix
    unquote(q|Hendrix|);     # Hendrix
    unquote(q|"Hendrix'|);   # "Hendrix'
    unquote(q|O'Sullivan|);  # O'Sullivan

**option:** braces

If the braces option is true, surrounding braces such as \[\] and {} are also
removed. Some examples:

    unquote(q|[Janis]|, braces=>1);  # Janis
    unquote(q|{Janis}|, braces=>1);  # Janis
    unquote(q|(Janis)|, braces=>1);  # Janis

## repeat($string, $count)

Returns the given string repeated the given number of times. The following
command outputs "Fred" three times:

    print repeat('Fred', 3), "\n";

Note that repeat() was created a long time based on a misunderstanding of how
the perl operator 'x' works.  The following command using 'x' would perform
exactly the same as the above command.

    print 'Fred' x 3, "\n";

Use whichever you prefer.

## randword($length, %options)

Returns a random string of characters. String will not contain any vowels (to
avoid distracting dirty words). First argument is the length of the return
string. So this code:

    foreach my $idx (1..3) {
        print randword(4), "\n";
    }

would output something like this:

    kBGV
    NCWB
    3tHJ

If the string 'dictionary' is sent instead of an integer, then a word is
randomly selected from a dictionary file.  By default, the dictionary file
is assumed to be at /usr/share/dict/words and the shuf command is used to
pull out a word.  The hash %String::Util::PATHS sets the paths to the
dictionary file and the shuf executable.  Modify that hash to change the paths.
So this code:

    foreach my $idx (1..3) {
        print randword('dictionary'), "\n";
    }

would output something like this:

    mustache
    fronds
    browning

**option:** alpha

If the alpha option is true, only alphabetic characters are returned, no
numerals. For example, this code:

    foreach my $idx (1..3) {
        print randword(4, alpha=>1), "\n";
    }

would output something like this:

    qrML
    wmWf
    QGvF

**option:** numerals

If the numerals option is true, only numerals are returned, no alphabetic
characters. So this code:

    foreach my $idx (1..3) {
        print randword(4, numerals=>1), "\n";
    }

would output something like this:

    3981
    4734
    2657

**option:** strip\_vowels

This option is true by default.  If true, vowels are not included in the
returned random string. So this code:

    foreach my $idx (1..3) {
        print randword(4, strip_vowels=>1), "\n";
    }

would output something like this:

    Sk3v
    pV5z
    XhSX

## eqq($scalar1, $scalar2)

Returns true if the two given values are equal.  Also returns true if both
are undef.  If only one is undef, or if they are both defined but different,
returns false. Here are some examples and what they return.

    $var = eqq('x', 'x');     # True
    $var = eqq('x', undef);   # False
    $var = eqq(undef, undef); # True

**Note:** equndef() is an alias to this function. It is considered deprecated.
It may be removed in future versions.

## neqq($scalar1, $scalar2)

The opposite of neqq, returns true if the two values are \*not\* the same.
Here are some examples and what they return.

    $var = neqq('x', 'x');     # False
    $var = neqq('x', undef);   # True
    $var = neqq(undef, undef); # False

**Note:** neundef() is an alias to this function. It is considered deprecated.
It may be removed in future versions.

## ords($string)

Returns the given string represented as the ascii value of each character.

    $var = ords('Hendrix'); # {72}{101}{110}{100}{114}{105}{120}

**options**

- convert\_spaces=>\[true|false\]

    If convert\_spaces is true (which is the default) then spaces are converted to
    their matching ord values. So, for example, this code:

        $var = ords('a b', convert_spaces=>1); # {97}{32}{98}

    This code returns the same thing:

        $var = ords('a b');                    # {97}{32}{98}

    If convert\_spaces is false, then spaces are just returned as spaces. So this
    code:

        ords('a b', convert_spaces=>0);        # {97} {98}

- alpha\_nums

    If the alpha\_nums option is false, then characters 0-9, a-z, and A-Z are not
    converted. For example, this code:

        $var = ords('a=b', alpha_nums=>0); # a{61}b

## deords($string)

Takes the output from ords() and returns the string that original created that
output.

    $var = deords('{72}{101}{110}{100}{114}{105}{120}'); # 'Hendrix'

## contains($string, $substring)

Checks if the string contains substring

    $var = contains("Hello world", "Hello");   # true
    $var = contains("Hello world", "llo wor"); # true
    $var = contains("Hello world", "QQQ");     # false

    # Also works with grep
    @arr = grep { contains("cat") } @input;

## startswith($string, $substring)

Checks if the string starts with the characters in substring

    $var = startwith("Hello world", "Hello"); # true
    $var = startwith("Hello world", "H");     # true
    $var = startwith("Hello world", "Q");     # false

    # Also works with grep
    @arr = grep { startswith("X") } @input;

## endswith($string, $substring)

Checks if the string ends with the characters in substring

    $var = endswith("Hello world", "world");   # true
    $var = endswith("Hello world", "d");       # true
    $var = endswith("Hello world", "QQQ");     # false

    # Also works with grep
    @arr = grep { endswith("z") } @input;

## crunchlines($string)

Compacts contiguous newlines into single newlines.  Whitespace between newlines
is ignored, so that two newlines separated by whitespace is compacted down to a
single newline.

    $var = crunchlines("x\n\n\nx"); # "x\nx";

## sanitize($string, $separator = "\_")

Sanitize all non alpha-numeric characters in a string to underscores.
This is useful to take a URL, or filename, or text description and know
you can use it safely in a URL or a filename.

**Note:** This will remove any trailing or leading '\_' on the string

    $var = sanitize("http://www.google.com/") # http_www_google_com
    $var = sanitize("foo_bar()";              # foo_bar
    $var = sanitize("/path/to/file.txt");     # path_to_file_txt
    $var = sanitize("Big yellow bird!", "."); # Big.yellow.bird

## file\_get\_contents($string, $boolean)

Read an entire file from disk into a string. Returns undef if the file
cannot be read for any reason. Can also return the file as an array of
lines.

    $str   = file_get_contents("/tmp/file.txt");    # Return a string
    @lines = file_get_contents("/tmp/file.txt", 1); # Return an array

# COPYRIGHT AND LICENSE

Copyright (c) 2012-2016 by Miko O'Sullivan.  All rights reserved.  This program
is free software; you can redistribute it and/or modify it under the same terms
as Perl itself. This software comes with **NO WARRANTY** of any kind.

# AUTHORS

Miko O'Sullivan <miko@idocs.com>

Scott Baker <scott@perturb.org>
