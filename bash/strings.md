<br>
<p align="center"><img src="../assets/logo/bash.png" width="200px"></p>
<br>

<h1 align="center">Bash</h1>
<h3 align="center">Strings</h3>

---

[🔙](../README.md)

---

- [Trim leading and trailing white-space from string](#trim-leading-and-trailing-white-space-from-string)
- [Trim all white-space from string and truncate spaces](#trim-all-white-space-from-string-and-truncate-spaces)
- [Use regex on a string](#use-regex-on-a-string)
- [Split a string on a delimiter](#split-a-string-on-a-delimiter)
- [Change a string to lowercase](#change-a-string-to-lowercase)
- [Change a string to uppercase](#change-a-string-to-uppercase)
- [Reverse a string case](#reverse-a-string-case)
- [Trim quotes from a string](#trim-quotes-from-a-string)
- [Strip all instances of pattern from string](#strip-all-instances-of-pattern-from-string)
- [Strip first occurrence of pattern from string](#strip-first-occurrence-of-pattern-from-string)
- [Strip pattern from start of string](#strip-pattern-from-start-of-string)
- [Strip pattern from end of string](#strip-pattern-from-end-of-string)
- [Percent-encode a string](#percent-encode-a-string)
- [Decode a percent-encoded string](#decode-a-percent-encoded-string)
- [Check if string contains a sub-string](#check-if-string-contains-a-sub-string)
- [Check if string starts with sub-string](#check-if-string-starts-with-sub-string)
- [Check if string ends with sub-string](#check-if-string-ends-with-sub-string)

---

## Trim leading and trailing white-space from string

This is an alternative to `sed`, `awk`, `perl` and other tools. The
function below works by finding all leading and trailing white-space and
removing it from the start and end of the string. The `:` built-in is used in place of a temporary variable.

**Example Function:**

```sh
trim_string() {
    # Usage: trim_string "   example   string    "
    : "${1#"${1%%[![:space:]]*}"}"
    : "${_%"${_##*[![:space:]]}"}"
    printf '%s\n' "$_"
}
```

**Example Usage:**

```shell
$ trim_string "    Hello,  World    "
Hello,  World

$ name="   John Black  "
$ trim_string "$name"
John Black
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Trim all white-space from string and truncate spaces

This is an alternative to `sed`, `awk`, `perl` and other tools. The
function below works by abusing word splitting to create a new string
without leading/trailing white-space and with truncated spaces.

**Example Function:**

```sh
# shellcheck disable=SC2086,SC2048
trim_all() {
    # Usage: trim_all "   example   string    "
    set -f
    set -- $*
    printf '%s\n' "$*"
    set +f
}
```

**Example Usage:**

```shell
$ trim_all "    Hello,    World    "
Hello, World

$ name="   John   Black  is     my    name.    "
$ trim_all "$name"
John Black is my name.
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Use regex on a string

The result of `bash`'s regex matching can be used to replace `sed` for a
large number of use-cases.

**CAVEAT**: This is one of the few platform dependent `bash` features.
`bash` will use whatever regex engine is installed on the user's system.
Stick to POSIX regex features if aiming for compatibility.

**CAVEAT**: This example only prints the first matching group. When using
multiple capture groups some modification is needed.

**Example Function:**

```sh
regex() {
    # Usage: regex "string" "regex"
    [[ $1 =~ $2 ]] && printf '%s\n' "${BASH_REMATCH[1]}"
}
```

**Example Usage:**

```shell
$ # Trim leading white-space.
$ regex '    hello' '^\s*(.*)'
hello

$ # Validate a hex color.
$ regex "#FFFFFF" '^(#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3}))$'
#FFFFFF

$ # Validate a hex color (invalid).
$ regex "red" '^(#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3}))$'
# no output (invalid)
```

**Example Usage in script:**

```shell
is_hex_color() {
    if [[ $1 =~ ^(#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3}))$ ]]; then
        printf '%s\n' "${BASH_REMATCH[1]}"
    else
        printf '%s\n' "error: $1 is an invalid color."
        return 1
    fi
}

read -r color
is_hex_color "$color" || color="#FFFFFF"

# Do stuff.
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Split a string on a delimiter

**CAVEAT:** Requires `bash` 4+

This is an alternative to `cut`, `awk` and other tools.

**Example Function:**

```sh
split() {
   # Usage: split "string" "delimiter"
   IFS=$'\n' read -d "" -ra arr <<< "${1//$2/$'\n'}"
   printf '%s\n' "${arr[@]}"
}
```

**Example Usage:**

```shell
$ split "apples,oranges,pears,grapes" ","
apples
oranges
pears
grapes

$ split "1, 2, 3, 4, 5" ", "
1
2
3
4
5

# Multi char delimiters work too!
$ split "hello---world---my---name---is---john" "---"
hello
world
my
name
is
john
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Change a string to lowercase

**CAVEAT:** Requires `bash` 4+

**Example Function:**

```sh
lower() {
    # Usage: lower "string"
    printf '%s\n' "${1,,}"
}
```

**Example Usage:**

```shell
$ lower "HELLO"
hello

$ lower "HeLlO"
hello

$ lower "hello"
hello
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Change a string to uppercase

**CAVEAT:** Requires `bash` 4+

**Example Function:**

```sh
upper() {
    # Usage: upper "string"
    printf '%s\n' "${1^^}"
}
```

**Example Usage:**

```shell
$ upper "hello"
HELLO

$ upper "HeLlO"
HELLO

$ upper "HELLO"
HELLO
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Reverse a string case

**CAVEAT:** Requires `bash` 4+

**Example Function:**

```sh
reverse_case() {
    # Usage: reverse_case "string"
    printf '%s\n' "${1~~}"
}
```

**Example Usage:**

```shell
$ reverse_case "hello"
HELLO

$ reverse_case "HeLlO"
hElLo

$ reverse_case "HELLO"
hello
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Trim quotes from a string

**Example Function:**

```sh
trim_quotes() {
    # Usage: trim_quotes "string"
    : "${1//\'}"
    printf '%s\n' "${_//\"}"
}
```

**Example Usage:**

```shell
$ var="'Hello', \"World\""
$ trim_quotes "$var"
Hello, World
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Strip all instances of pattern from string

**Example Function:**

```sh
strip_all() {
    # Usage: strip_all "string" "pattern"
    printf '%s\n' "${1//$2}"
}
```

**Example Usage:**

```shell
$ strip_all "The Quick Brown Fox" "[aeiou]"
Th Qck Brwn Fx

$ strip_all "The Quick Brown Fox" "[[:space:]]"
TheQuickBrownFox

$ strip_all "The Quick Brown Fox" "Quick "
The Brown Fox
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Strip first occurrence of pattern from string

**Example Function:**

```sh
strip() {
    # Usage: strip "string" "pattern"
    printf '%s\n' "${1/$2}"
}
```

**Example Usage:**

```shell
$ strip "The Quick Brown Fox" "[aeiou]"
Th Quick Brown Fox

$ strip "The Quick Brown Fox" "[[:space:]]"
TheQuick Brown Fox
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Strip pattern from start of string

**Example Function:**

```sh
lstrip() {
    # Usage: lstrip "string" "pattern"
    printf '%s\n' "${1##$2}"
}
```

**Example Usage:**

```shell
$ lstrip "The Quick Brown Fox" "The "
Quick Brown Fox
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Strip pattern from end of string

**Example Function:**

```sh
rstrip() {
    # Usage: rstrip "string" "pattern"
    printf '%s\n' "${1%%$2}"
}
```

**Example Usage:**

```shell
$ rstrip "The Quick Brown Fox" " Fox"
The Quick Brown
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Percent-encode a string

**Example Function:**

```sh
urlencode() {
    # Usage: urlencode "string"
    local LC_ALL=C
    for (( i = 0; i < ${#1}; i++ )); do
        : "${1:i:1}"
        case "$_" in
            [a-zA-Z0-9.~_-])
                printf '%s' "$_"
            ;;

            *)
                printf '%%%02X' "'$_"
            ;;
        esac
    done
    printf '\n'
}
```

**Example Usage:**

```shell
$ urlencode "https://github.com/AlexRoman777/Linux-I"
https%3A%2F%2Fgithub.com%2FAlexRoman777%2FLinux-I
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Decode a percent-encoded string

**Example Function:**

```sh
urldecode() {
    # Usage: urldecode "string"
    : "${1//+/ }"
    printf '%b\n' "${_//%/\\x}"
}
```

**Example Usage:**

```shell
$ urldecode "https%3A%2F%2Fgithub.com%2FAlexRoman777%2FLinux-I"
https://github.com/AlexRoman777/Linux-I
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Check if string contains a sub-string

**Using a test:**

```shell
if [[ $var == *sub_string* ]]; then
    printf '%s\n' "sub_string is in var."
fi

# Inverse (substring not in string).
if [[ $var != *sub_string* ]]; then
    printf '%s\n' "sub_string is not in var."
fi

# This works for arrays too!
if [[ ${arr[*]} == *sub_string* ]]; then
    printf '%s\n' "sub_string is in array."
fi
```

**Using a case statement:**

```shell
case "$var" in
    *sub_string*)
        # Do stuff
    ;;

    *sub_string2*)
        # Do more stuff
    ;;

    *)
        # Else
    ;;
esac
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Check if string starts with sub-string

```shell
if [[ $var == sub_string* ]]; then
    printf '%s\n' "var starts with sub_string."
fi

# Inverse (var does not start with sub_string).
if [[ $var != sub_string* ]]; then
    printf '%s\n' "var does not start with sub_string."
fi
```

[🔝](#trim-leading-and-trailing-white-space-from-string)

---

## Check if string ends with sub-string

```shell
if [[ $var == *sub_string ]]; then
    printf '%s\n' "var ends with sub_string."
fi

# Inverse (var does not end with sub_string).
if [[ $var != *sub_string ]]; then
    printf '%s\n' "var does not end with sub_string."
fi
```

---

[🔝](#trim-leading-and-trailing-white-space-from-string)[🔙](../README.md)
