Here’s a concise Markdown version of the write-up:

---

# CTF Write-Up: Whitespace Encoding

## Overview

The challenge provided a seemingly blank text file that, upon inspection, contained only spaces and tabs — a clear sign of whitespace encoding.

---

## Process

1. **Identify Hidden Data**

   ```bash
   hexdump txt
   ```

   Revealed only `0x20` (spaces) and `0x09` (tabs).

2. **Convert Whitespace to Binary**

   ```bash
   tr -d '\r\n' < txt | tr ' \t' '01'
   ```

3. **Group & Decode**

   ```bash
   tr -d '\r\n' < txt | tr ' \t' '01' | \
   sed 's/\(........\)/\1\n/g' | \
   while read b; do printf \\$(printf '%03o' "$((2#$b))"); done
   ```

---

## Result

Decoded output revealed the flag:

```
v1t{hidden_whitespace}
```

---

## Summary

Spaces and tabs were mapped to binary (`0` and `1`) to hide data. Grouping every 8 bits and converting to ASCII revealed the flag. Simple yet effective whitespace steganography.

---
