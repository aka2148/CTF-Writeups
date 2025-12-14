# Antakshari

## AI

The challenge models actors as nodes embedded in a latent vector space, where proximity represents semantic similarity. The provided edges are sparse and are red herrings.

To solve it, treat the problem as a k-nearest-neighbors task. For each node, take its 5 closest neighbors in latent space to form a 6-node candidate set. Compute the average pairwise distance within each set and select the set with the minimum average distance. This represents the six cast members most tightly associated with a single movie.

Finally, output the node IDs of this set in descending numeric order:


Script:
```
import numpy as np
from itertools import combinations

vecs = np.load("latent_vectors.npy")

cluster = [108, 189, 134, 37, 29, 177]

def avg_dist(group):
    return sum(
        np.linalg.norm(vecs[a] - vecs[b])
        for a, b in combinations(group, 2)
    ) / len(list(combinations(group, 2)))

def avg_dist_to_others(x, group):
    return sum(
        np.linalg.norm(vecs[x] - vecs[y])
        for y in group if y != x
    ) / (len(group) - 1)

print("[*] Average distance of each node to others:")
scores = {n: avg_dist_to_others(n, cluster) for n in cluster}
for n, d in scores.items():
    print(f"Node {n}: {d:.4f}")

worst = max(scores, key=scores.get)
print("\n[!] Worst-fitting node:", worst)

reduced = [n for n in cluster if n != worst]

best_group = None
best_score = float("inf")

for candidate in range(len(vecs)):
    if candidate in reduced or candidate == worst:
        continue

    test = reduced + [candidate]
    score = avg_dist(test)

    if score < best_score:
        best_score = score
        best_group = test

print("\n[+] Refined cluster:")
print(best_group)
print("Avg intra-distance:", best_score)

print("\n[+] Descending order:")
print(sorted(best_group, reverse=True))

```
Final sequence:
189, 177, 134, 108, 37, 29

flag:
