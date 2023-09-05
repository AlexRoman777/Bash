<br>
<p align="center"><img src="../assets/logo/bash.png" width="200px"></p>
<br>

<h1 align="center">Bash</h1>
<h3 align="center">File Handling</h3>

---

[🔙](../README.md)

---

- [Read a file to a string](#read-a-file-to-a-string)
- [Read a file to an array (*by line*)](#read-a-file-to-an-array-by-line)
- [Get the first N lines of a file](#get-the-first-n-lines-of-a-file)
- [Get the last N lines of a file](#get-the-last-n-lines-of-a-file)
- [Get the number of lines in a file](#get-the-number-of-lines-in-a-file)
- [Count files or directories in directory](#count-files-or-directories-in-directory)
- [Create an empty file](#create-an-empty-file)
- [Extract lines between two markers](#extract-lines-between-two-markers)

---

**CAVEAT:** `bash` does not handle binary data properly in versions `< 4.4`.

## Read a file to a string

Alternative to the `cat` command.

```shell
file_data="$(<"file")"
```

[🔝](#read-a-file-to-a-string)

---

## Read a file to an array (*by line*)

Alternative to the `cat` command.

```shell
# Bash <4 (discarding empty lines).
IFS=$'\n' read -d "" -ra file_data < "file"

# Bash <4 (preserving empty lines).
while read -r line; do
    file_data+=("$line")
done < "file"

# Bash 4+
mapfile -t file_data < "file"
```

[🔝](#read-a-file-to-a-string)

---

## Get the first N lines of a file

Alternative to the `head` command.

**CAVEAT:** Requires `bash` 4+

**Example Function:**

```sh
head() {
    # Usage: head "n" "file"
    mapfile -tn "$1" line < "$2"
    printf '%s\n' "${line[@]}"
}
```

**Example Usage:**

```shell
$ head 2 ~/.bashrc
# Prompt
PS1='➜ '

$ head 1 ~/.bashrc
# Prompt
```

[🔝](#read-a-file-to-a-string)

---

## Get the last N lines of a file

Alternative to the `tail` command.

**CAVEAT:** Requires `bash` 4+

**Example Function:**

```sh
tail() {
    # Usage: tail "n" "file"
    mapfile -tn 0 line < "$2"
    printf '%s\n' "${line[@]: -$1}"
}
```

**Example Usage:**

```shell
$ tail 2 ~/.bashrc
# Enable tmux.
# [[ -z "$TMUX"  ]] && exec tmux

$ tail 1 ~/.bashrc
# [[ -z "$TMUX"  ]] && exec tmux
```

[🔝](#read-a-file-to-a-string)

---

## Get the number of lines in a file

Alternative to `wc -l`.

**Example Function (bash 4):**

```sh
lines() {
    # Usage: lines "file"
    mapfile -tn 0 lines < "$1"
    printf '%s\n' "${#lines[@]}"
}
```

**Example Function (bash 3):**

This method uses less memory than the `mapfile` method and works in `bash` 3 but it is slower for bigger files.

```sh
lines_loop() {
    # Usage: lines_loop "file"
    count=0
    while IFS= read -r _; do
        ((count++))
    done < "$1"
    printf '%s\n' "$count"
}
```

**Example Usage:**

```shell
$ lines ~/.bashrc
48

$ lines_loop ~/.bashrc
48
```

[🔝](#read-a-file-to-a-string)

---

## Count files or directories in directory

This works by passing the output of the glob to the function and then counting the number of arguments.

**Example Function:**

```sh
count() {
    # Usage: count /path/to/dir/*
    #        count /path/to/dir/*/
    printf '%s\n' "$#"
}
```

**Example Usage:**

```shell
# Count all files in dir.
$ count ~/Downloads/*
232

# Count all dirs in dir.
$ count ~/Downloads/*/
45

# Count all jpg files in dir.
$ count ~/Pictures/*.jpg
64
```

[🔝](#read-a-file-to-a-string)

---

## Create an empty file

Alternative to `touch`.

```shell
# Shortest.
>file

# Longer alternatives:
:>file
echo -n >file
printf '' >file
```

[🔝](#read-a-file-to-a-string)

---

## Extract lines between two markers

**Example Function:**

```sh
extract() {
    # Usage: extract file "opening marker" "closing marker"
    while IFS=$'\n' read -r line; do
        [[ $extract && $line != "$3" ]] &&
            printf '%s\n' "$line"

        [[ $line == "$2" ]] && extract=1
        [[ $line == "$3" ]] && extract=
    done < "$1"
}
```

**Example Usage:**

```shell
# Extract code blocks from MarkDown file.
$ extract ~/projects/pure-bash/README.md '```sh' '```'
# Output here...
```

---

[🔝](#read-a-file-to-a-string)[🔙](../README.md)
