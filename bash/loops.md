<br>
<p align="center"><img src="../assets/logo/bash.png" width="200px"></p>
<br>

<h1 align="center">Bash</h1>
<h3 align="center">Loops</h3>

---

[🔙](../README.md)

---

- [Loop over a range of numbers](#loop-over-a-range-of-numbers)
- [Loop over a variable range of numbers](#loop-over-a-variable-range-of-numbers)
- [Loop over an array](#loop-over-an-array)
- [Loop over an array with an index](#loop-over-an-array-with-an-index)
- [Loop over the contents of a file](#loop-over-the-contents-of-a-file)
- [Loop over files and directories](#loop-over-files-and-directories)

---

## Loop over a range of numbers

Alternative to `seq`.

```shell
# Loop from 0-100 (no variable support).
for i in {0..100}; do
    printf '%s\n' "$i"
done
```

[🔝](#loop-over-a-range-of-numbers)

---

## Loop over a variable range of numbers

Alternative to `seq`.

```shell
# Loop from 0-VAR.
VAR=50
for ((i=0;i<=VAR;i++)); do
    printf '%s\n' "$i"
done
```

[🔝](#loop-over-a-range-of-numbers)

---

## Loop over an array

```shell
arr=(apples oranges tomatoes)

# Just elements.
for element in "${arr[@]}"; do
    printf '%s\n' "$element"
done
```

[🔝](#loop-over-a-range-of-numbers)

---

## Loop over an array with an index

```shell
arr=(apples oranges tomatoes)

# Elements and index.
for i in "${!arr[@]}"; do
    printf '%s\n' "${arr[i]}"
done

# Alternative method.
for ((i=0;i<${#arr[@]};i++)); do
    printf '%s\n' "${arr[i]}"
done
```

[🔝](#loop-over-a-range-of-numbers)

---

## Loop over the contents of a file

```shell
while read -r line; do
    printf '%s\n' "$line"
done < "file"
```

[🔝](#loop-over-a-range-of-numbers)

---

## Loop over files and directories

Don’t use `ls`.

```shell
# Greedy example.
for file in *; do
    printf '%s\n' "$file"
done

# PNG files in dir.
for file in ~/Pictures/*.png; do
    printf '%s\n' "$file"
done

# Iterate over directories.
for dir in ~/Downloads/*/; do
    printf '%s\n' "$dir"
done

# Brace Expansion.
for file in /path/to/parentdir/{file1,file2,subdir/file3}; do
    printf '%s\n' "$file"
done

# Iterate recursively.
shopt -s globstar
for file in ~/Pictures/**/*; do
    printf '%s\n' "$file"
done
shopt -u globstar
```

---

[🔝](#loop-over-a-range-of-numbers)[🔙](../README.md)
