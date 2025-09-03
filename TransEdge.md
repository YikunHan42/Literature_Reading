# TransEdge: Translating Relation-contextualized Embeddings for Knowledge Graphs

## 🔹 Motivation
- Standard translational models (like TransE) represent each **relation $r$** with a **single embedding vector**.  
- Problem: This fails when the same relation appears in **different contexts**.  
  - Example: `(From Dusk Till Dawn, starring, Quentin Tarantino)` vs `(From Dusk Till Dawn, starring, Cheech Marin)`.  
  - In TransE, both Quentin Tarantino and Cheech Marin end up very close in the embedding space → misleading (they are not the same).  
- Similarly, different relations with similar distributions (e.g., `starring` vs `writer`) may collapse into indistinguishable embeddings.  

👉 TransEdge solves this by **contextualizing relation embeddings into edge embeddings**.

---

## 🔹 Core Idea: Edge-Centric Embeddings
- Instead of one embedding per relation, TransEdge creates a **contextualized edge embedding** $\psi(h_c, t_c, r)$.  
- This embedding depends on:
  - Head entity embedding $h_c$  
  - Tail entity embedding $t_c$  
  - Relation embedding $r$  

- The edge embedding acts as a **translation vector**:  

$$
f(h, r, t) = \| h + \psi(h_c, t_c, r) - t \|
$$

- Two ways to compute $\psi$ (contextualization operator):
  1. **Context Compression (CC):** Use MLPs to nonlinearly combine $h_c$, $t_c$, and $r$.  
  2. **Context Projection (CP):** Project $r$ onto a hyperplane defined by $(h_c, t_c)$ (better geometric interpretation).  

---

## 🔹 Training Objective
- Uses **limit-based loss** (instead of margin ranking loss):  

$$
L = \sum_{(h,r,t) \in T} [f(h,r,t) - \gamma_1]_+ + \alpha \sum_{(h',r',t') \in T^-} [\gamma_2 - f(h',r',t')]_+
$$

- $T$ = positive triples (from KG).  
- $T^-$ = negative triples (sampled by replacing head/tail with random neighbors).  
- $\gamma_1, \gamma_2$ = margin thresholds; $\alpha$ = balancing factor.  

---

## 🔹 Entity Alignment with TransEdge
- Given two KGs $K_1$ and $K_2$, seed entity pairs (aligned entities) are provided.  
- **Parameter sharing:** aligned entities share the same embedding → merges two KGs into one embedding space.  
- **Semi-supervised bootstrapping:** iteratively add high-confidence alignments $D = \{(e_1, e_2) | \cos(e_1, e_2) > s\}$, where $s$ is a similarity threshold.  
- Minimizes an extra loss:  

$$
L_{semi} = \sum_{(e_1, e_2) \in D} \| e_1 - e_2 \|
$$

- At test time: given an entity in $K_1$, rank all entities in $K_2$ by cosine similarity.  

---

## 🔹 Link Prediction
- Same as TransE: for $(h,r,?)$ or $(?,r,t)$, rank candidate entities by energy function $f(h,r,t)$.  
- Uses **filtered evaluation** (ignores other true triples from training/validation/test when ranking).  
- Metrics: Hits@k, MRR, MR.  

---

## 🔹 Results
- **Entity Alignment:**  
  - Evaluated on **DBP15K (cross-lingual)** and **DWY100K (monolingual)**.  
  - Outperforms prior models (MTransE, IPTransE, BootEA, JAPE, GCN-Align).  
  - Context Projection (CP) variant performs best.  
- **Link Prediction:**  
  - Benchmarks: **FB15K-237** and **WN18RR**.  
  - Beats TransE, TransH, TransR, TransD.  
  - Achieves **best Hits@1** among all models (including bilinear ones like RotatE).  

---

## 🔹 Key Takeaways
- **Innovation:** relation embeddings are contextualized into **edge embeddings**, sensitive to both head and tail.  
- **Strength:** avoids collapsing different entities under the same relation, preserves relational structure better.  
- **Performance:** state-of-the-art on both **entity alignment** and **link prediction** at the time.  
- **Trade-off:** slightly higher complexity ($O(2n_e d + n_r d)$) vs TransE, but still efficient compared to neural models.  

---

✅ In summary:  
**TransEdge = TransE + context-awareness.**  
It generalizes translation-based KGE by making relation embeddings **dynamic (edge-specific)** instead of static, which helps both cross-KG alignment and KG completion.