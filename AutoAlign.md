# AutoAlign

## 1. How is **J** calculated?
- $J$ is **not a concatenation**, but a **weighted sum of loss terms**:  

$$
J = J_{\text{predicate}} + J_{\text{attribute}} + J_{\text{structure}} + J_{\text{similarity}}
$$

- **$J_{\text{predicate}}$:** alignment loss for predicates, using the predicate-proximity graph (based on aligned types from the LLM).  
- **$J_{\text{attribute}}$:** alignment loss for attributes (string/character embeddings of entity attributes).  
- **$J_{\text{structure}}$:** translational loss (TransE-like), ensuring that embeddings respect KG structure ($h + r \approx t$). Predicates that are aligned (from LLM/type step) get higher weights here.  
- **$J_{\text{similarity}}$:** forces embeddings of equivalent entities/predicates (when inferred) to be close in vector space.  

👉 $J$ is an **additive joint loss**, not concatenation. Each module contributes gradients to entity/predicate embeddings, ensuring everything lives in the *same semantic space*.  

---

## 2. How is the training done?
- Training is performed as **joint KG embedding learning**.  
- **Initialization:** entity, relation, and attribute embeddings are initialized randomly (or with pre-trained embeddings, depending on the experiment).  
- **Optimization:**  
  - Negative sampling is used (like in TransE/ComplEx) — corrupted triples are generated to contrast against real triples.  
  - Stochastic Gradient Descent (SGD) or Adam is used to minimize $J$.  
  - The three modules (predicate, structure, attribute) are trained *together* in each batch.  
- **End result:** embeddings for all entities and predicates in both KGs, in a shared space, aligned without supervision.  

---

## 3. How is the threshold selected?
- After training, entity pairs are compared using **cosine similarity** in embedding space.  
- Alignment decision rule:  

$$
\text{aligned}(e_i, e_j) =
\begin{cases} 
1 & \text{if sim}(e_i, e_j) > \beta \\
0 & \text{otherwise}
\end{cases}
$$

- The threshold $\beta$ is tuned on a **validation set** (splitting available ground truth pairs into train/validation/test).  
- In practice:  
  - $\beta$ is set to maximize **Hits@k** or **F1 score** on validation.  
  - Once selected, it’s fixed during testing.  

---

## 4. How is the result evaluated?
- **Ground-truth aligned pairs** are provided by benchmark datasets.  
- **Datasets:**  
  - DBpedia–Wikidata  
  - DBpedia–YAGO  
- These datasets already contain **reference alignment pairs** (human-curated, widely used in entity alignment tasks).  
- **Metrics:**  
  - **Hits@k:** fraction of test entities whose correct match is ranked in the top-k candidates.  
  - **MRR (Mean Reciprocal Rank):** measures ranking quality.  
  - **Precision / Recall / F1:** sometimes used as secondary metrics.  

👉 Evaluation is possible because **Wikidata and YAGO are already aligned at some level** in these benchmark datasets.  

---

### ✅ Summary
1. $J$ = weighted sum of multiple module losses (predicate, attribute, structure, similarity).  
2. Training = joint embedding learning with negative sampling + gradient descent.  
3. Threshold $\beta$ = tuned on validation set for best alignment performance.  
4. Evaluation = Hits@k, MRR, etc., using **ground-truth entity pairs** from DBpedia–Wikidata/YAGO benchmark datasets.  