# fpattern
Filename pattern matching library functions for DOS, Windows, and Unix.

Functions for matching filename patterns to filenames.
Written in C.
Provided as an object file (<code>fpattern.obj</code>)
and a header include source file (<code>fpattern.h</code>).

Filename <i>patterns</i> are composed of regular (printable) characters which
may comprise a filename, as well as special <i>pattern matching</i> characters.
A filename patterns is a special kind of <i>regular expression</i>, except that
it is not as general and is designed to match only file and directory names instead
of arbitrary text strings.
The syntax is borrowed from Unix, and is a superset of the MS-DOS and Windows syntax.

<b>Special pattern characters</b>

<pre>
    .           Matches a period (.).
                Note that a period in a filename is not treated any
                differently than any other character.

    ?           Any.
                Matches any single character except '/' or '\'.

    *           Closure.
                Matches zero or more occurences of any characters other
                than '/' or '\'.  Leading '*' characters are allowed.

    SUB         Substitute (control-Z).
                Similar to '*', this matches zero or more occurences of
                any characters other than '/', '\', or '.'.  Leading
                '^Z' characters are allowed.

    [ab]        Set.
                Matches the single character 'a' or 'b'.
                If the dash '-' character is to be included, it must
                immediately follow the opening bracket '['.  If the
                closing bracket ']' character is to be included, it must
                be preceded by a quote '`'.

    [a-z]       Range.
                Matches a single character in the range 'a' to 'z'.
                Ranges and sets may be combined within the same set of
                brackets.

    [!R]        Exclusive range.
                Matches a single character not in the range 'R'.
                If range 'R' includes the dash '-' character, the dash
                must immediately follow the caret '!'.

    !           Not.
                Makes the following pattern (up to the next '/') match
                any filename except those what it would normally match.

    /           Path separator (UNIX and DOS).
                Matches a '/' or '\' pathname (directory) separator.
                Multiple separators are treated like a single separator.
                A leading separator indicates an absolute pathname.

    \           Path separator (DOS).
                Same as the '/' character.  Note that this character
                must be escaped if used within string constants ("\\").

    \           Quote (UNIX).
                Makes the next character a regular (nonspecial)
                character.  Note that to match the quote character
                itself, it must be quoted.  Note that this character
                must be escaped if used within string constants ("\\").

    `           Quote (DOS).
                Makes the next character a regular (nonspecial)
                character.  Note that to match the quote character
                itself, it must be quoted.
</pre>

On DOS and Windows (Win32) systems,
upper and lower case alphabetic characters are considered identical,
i.e., 'a' and 'A' match each other.  (What constitutes a lowercase
letter depends on the current locale settings.)
On Unix, upper and lower case characters are different, i.e.,
'foo' and 'FOO' are different filenames.

Spaces and control characters are treated as normal characters.

<b>Examples</b>

The following patterns in the left column will match the filenames in
the middle column and will not match filenames in the right column:
<pre>
    Pattern     Will Match                      Will Not Match
    -------     ----------                      --------------
    a           a (only)                        (anything else)
    a.          a. (only)                       (anything else)
    a?c         abc, acc, arc, a.c              a, ac, abbc
    a*c         ac, abc, abbc, acc, a.c         a, ab, acb, bac
    a*          a, ab, abb, a., a.b             b, ba
    *           a, ab, abb, a., .foo, a.foo     (nothing)
    *.          a., ab., abb., a.foo.           a, ab, a.foo, .foo
    *.*         a., a.b, ah.bc.foo              a
    ^Z          a, ab, abb                      a., .foo, a.foo
    ^Z.         a., ab., abb.                   a, .foo, a.foo
    ^Z.*        a, a., .foo, a.foo              ab, abb
    *2.c        2.c, 12.c, foo2.c, foo.12.c     2x.c
    a[b-z]c     abc, acc, azc (only)            (anything else)
    [ab0-9]x    ax, bx, 0x, 9x                  zx
    a[-.]b      a-b, a.b (only)                 (anything else)
    a[!a-z]b    a0b, a.b, a@b                   aab, azb, aa0b
    a[!-b]x     a0x, a+x, acx                   a-x, abx, axxx
    a[-!b]x     a-x, a!x, abx (only)            (anything else)
    a[`]]x      a]x (only)                      (anything else)
    a``x        a`x (only)                      (anything else)
    oh`!        oh! (only)                      (anything else)
    is`?it      is?it (only)                    (anything else)
    !a?c        a, ac, ab, abb, acb, a.foo      abc, a.c, azc
</pre>
