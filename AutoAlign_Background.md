# Joint KG Embedding Learning

## 🔹 What is KG Embedding Learning?
- A **knowledge graph (KG)** is a set of triples:  
  - **Relation triple:** $(h, r, t)$ where $h$ = head entity, $r$ = relation (predicate), $t$ = tail entity.  
  - **Attribute triple:** $(h, p, v)$ where $p$ = attribute, $v$ = literal value (e.g., string, number).  
- **Embedding learning**: represent each entity ($h, t$), relation ($r$), and attribute ($p$) as a low-dimensional **vector**.  
- Example: In TransE, we want  
  $$
  h + r \approx t
  $$
  meaning that the relation is modeled as a translation in vector space.  

So KG embedding learning is essentially training a neural or translational model to encode the graph structure into continuous vectors.  

---

## 🔹 What Does “Joint” Mean Here?
AutoAlign doesn’t train embeddings for entities, predicates, and attributes **separately**.  
Instead, it trains them **together under a single objective $J$** so they live in the **same vector space**.  

- **Predicate embeddings**: learn from the **predicate-proximity graph**, where entities are replaced by their types (aligned by LLM).  
- **Attribute embeddings**: learn from textual attribute values (“Barack Obama” vs. “Barack Hussein Obama”), using SUM/LSTM/N-gram over characters.  
- **Structure embeddings**: learn from graph structure (TransE-style).  
- **Similarity alignment**: forces entity embeddings from two different KGs to converge if their attributes/predicates suggest equivalence.  

The training alternates between these components, but gradients update the **same set of embeddings**.  

---

## 🔹 Why Joint Learning?
- If you only use **structure embeddings**, entities from two different KGs end up in **different spaces** (since their graphs are disconnected).  
- If you only use **attribute embeddings**, you capture text similarity, but ignore structure.  
- By training **jointly**, you “tie” the spaces together:  
  - Predicates align across KGs via type similarity.  
  - Attributes align entities via textual similarity.  
  - Structure reinforces relationships so embeddings respect KG connectivity.  

End result: everything (entities + predicates + attributes) ends up in **one unified embedding space**, where cross-KG alignment can be done by simple similarity search.  

---

## 🔹 Analogy
Think of it like teaching two different languages (say French and German) **at the same time**:  
- Grammar rules = **structure embeddings**.  
- Dictionary words = **attribute embeddings**.  
- Common sentence patterns = **predicate embeddings**.  

If you train on only one source, you’ll learn inconsistencies. Joint training forces both languages into a shared bilingual representation — just like AutoAlign forces two KGs into one semantic space.  

---

✅ **Summary**  
Joint KG embedding learning = optimizing a single objective over **structure, predicate, and attribute signals simultaneously**, so entities and relations from different graphs are projected into a **shared vector space**. This makes cross-graph alignment possible without manual seeds.  