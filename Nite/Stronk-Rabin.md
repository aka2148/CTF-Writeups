# Stronk Rabin – Short Writeup

## Overview

The challenge uses a Rabin encryption scheme with a modulus made of four primes:

- \( N = p \cdot q \cdot r \cdot s \), each 256 bits and \( \equiv 3 \pmod{4} \)
- Encryption:  
  \[
  \text{ENC}(m) = m^2 \bmod N
  \]
- A broken decryption oracle `DEC` that returns the **sum of a random subset** of valid square roots.

## Script

```


import socket
import ssl
import json
import math
import random
import time
from Crypto.Util.number import long_to_bytes, inverse

# Configuration
HOST = 'stronk.chals.nitectf25.live'
PORT = 1337

def get_crt_roots(c, primes):
    """
    Standard Rabin decryption to get all 16 roots.
    """
    p, q, r, s = primes
    # Calculate modular square roots for each prime (p = 3 mod 4)
    rps = [pow(c, (k + 1) // 4, k) for k in primes]

    N = p * q * r * s
    roots = []

    # Iterate through all 16 sign combinations
    for i in range(16):
        b = bin(i)[2:].zfill(4)
        parts = []
        moduli = []
        for j, bit in enumerate(b):
            root_val = rps[j] if bit == '0' else -rps[j]
            parts.append(root_val)
            moduli.append(primes[j])

        # CRT: x = sum(ai * Ni * yi) mod N
        x = 0
        for idx, (ai, ni) in enumerate(zip(parts, moduli)):
            Ni = N // ni
            yi = inverse(Ni, ni)
            x += ai * Ni * yi

        roots.append(x % N)
    return roots

def solve():
    print(f"[*] Connecting to {HOST}:{PORT} via SSL...")

    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    context = ssl.create_default_context()
    context.check_hostname = False
    context.verify_mode = ssl.CERT_NONE

    s = context.wrap_socket(sock, server_hostname=HOST)
    s.connect((HOST, PORT))

    f = s.makefile('rw', buffering=1)

    print("[*] Connected. Waiting for parameter generation...")

    flag_enc = None

    while True:
        try:
            line = f.readline()
            if not line: break

            clean_line = line.strip()
            print(f"Server: {clean_line}")

            if clean_line.startswith('{') and '"C"' in clean_line:
                data = json.loads(clean_line)
                flag_enc = int(data['C'])
                break
        except Exception as e:
            print(f"[-] Error parsing line: {e}")
            continue

    if flag_enc is None:
        print("[-] Fatal: Could not retrieve C.")
        return

    print(f"[+] Retrieved Target Ciphertext C.")

    def query_oracle(func, args):
        req = json.dumps({"func": func, "args": args})
        try:
            f.write(req + "\n")
            f.flush()
            resp_line = f.readline()
            return json.loads(resp_line)
        except:
            return {}

    # --- Step 1: Recover N ---
    print("[*] Step 1: Recovering Modulus N...")
    m1 = 2**512 + 3
    m2 = 2**513 + 7
    r1 = query_oracle("ENC", [m1])['retn']
    r2 = query_oracle("ENC", [m2])['retn']
    N = math.gcd(m1**2 - r1, m2**2 - r2)
    print(f"[+] Recovered N: {N}")

    # --- Step 2: Factor N ---
    print("[*] Step 2: Factoring N (Statistical Attack)...")
    factors = [N]
    attempts = 0

    while len(factors) < 4:
        attempts += 1
        x = random.randint(2, N-1)
        C_probe = pow(x, 2, N)
        resp = query_oracle("DEC", [C_probe])

        if 'retn' not in resp: continue
        S = int(resp['retn'])
        g = math.gcd(S, N)

        if g > 1 and g < N:
            new_factors = []
            hit = False
            for fac in factors:
                common = math.gcd(g, fac)
                if common > 1 and common < fac:
                    new_factors.append(common)
                    new_factors.append(fac // common)
                    hit = True
                else:
                    new_factors.append(fac)
            if hit:
                factors = sorted(list(set(new_factors)))
                print(f"    [+] Split found! Current factors: {factors}")

        if attempts % 50 == 0:
            print(f"    ... {attempts} queries. Factors: {len(factors)}/4")

    print(f"[+] All 4 Factors Found.")

    # --- Step 3: Decrypt Flag ---
    print("[*] Step 3: Decrypting Flag...")

    roots = get_crt_roots(flag_enc, factors)

    print(f"[*] Filtering {len(roots)} candidates...")

    for r in roots:
        # Constraints: flag < n AND 2*flag > n
        if r < N and (2 * r) > N:
            try:
                msg = long_to_bytes(r)
                # Look for the flag header in the BYTES, do not decode yet
                if b'nite{' in msg:
                    print(f"\n[!!!] FLAG FOUND: {msg}\n")
                    return
            except Exception as e:
                print(f"[-] Error checking candidate: {e}")
                continue

    print("[-] Flag not found in candidates.")

if __name__ == "__main__":
```

## Final Flag

**nite{rabin_stronk_no_r4bin_brok3n}**
