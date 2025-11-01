## Crypto Challenge Write-up — Shamir’s Secret Sharing

### Challenge Description
The hint stated that a secret was **split into 6 shares** and that **any 3 shares** could reconstruct it.  
Each participant (Bob, Sang, Khoi, Long, Dung, Steve) held a unique hexadecimal string.

This clearly pointed to **Shamir’s Secret Sharing (SSS)** — a cryptographic scheme that divides a secret into multiple parts, requiring a threshold number of shares to reconstruct it.

### Analysis
Each share was 48 hex characters (24 bytes).  
Treating the shares as **Y-values** and assigning **X-values 1–6**, interpolation was performed in a large finite field (a 193-bit prime).

Using **Lagrange interpolation** at \( x = 0 \) modulo the chosen prime successfully reconstructed the same 24-byte secret from every 3-share combination.

### Result
The recovered secret decoded cleanly to ASCII:

