## **1. Overview**

**Goal:**
 To present an in-depth description and comprehensive evaluation of **SemRep**, a rule-based **biomedical natural language processing (NLP)** system that extracts **semantic relations** from PubMed abstracts using **linguistic principles** and **UMLS domain knowledge**Broad-coverage biomedical relat….

**Significance:**
 SemRep underpins **SemMedDB**, a large-scale biomedical knowledge graph of ≈ 98 million relations, enabling applications in **clinical decision making, drug repurposing, hypothesis generation,** and **literature-based discovery**Broad-coverage biomedical relat…Broad-coverage biomedical relat….

------

## **2. System Architecture**

### **Pipeline Components**

SemRep’s processing involves five major analysis stagesBroad-coverage biomedical relat…:

1. **Pre-linguistic analysis:**
    Sentence splitting, tokenization, and acronym detection (via **MetaMap**).
2. **Lexical/Syntactic analysis:**
    Uses the **UMLS SPECIALIST Lexicon** and **MedPost tagger** to identify lemmas, POS tags, and shallow noun phrases.
3. **Referential analysis:**
    Maps text spans to **UMLS concepts (CUIs)** or **NCBI Gene IDs** using MetaMap and **ABGene**, with domain-specific extensions to improve coverage and suppress spurious matches (so-called “dysonyms”).
4. **Post-referential analysis:**
   - Empty-head detection (e.g., “CYP2C9 variants”),
   - Coordination handling (“osteosarcoma, melanoma, and breast cancer”),
   - Optional **sortal anaphora resolution** (“these drugs → prostacyclin analogues, …”)Broad-coverage biomedical relat…Broad-coverage biomedical relat….
5. **Relational analysis:**
    Generates **subject–predicate–object triples** (“semantic predications”) such as
    *Aspirin TREATS Headache* or *Diabetes ASSOCIATED_WITH Obesity*.

------

## **3. Relation Types and Ontology**

SemRep extends the **UMLS Semantic Network** with **25 predicates** spanning multiple biomedical domainsBroad-coverage biomedical relat…:

- Clinical: TREATS, DIAGNOSES, PREVENTS
- Molecular: INTERACTS_WITH, INHIBITS, STIMULATES
- Etiologic: CAUSES, ASSOCIATED_WITH, PREDISPOSES
- Structural: ISA, PART_OF, LOCATION_OF
- Comparative: COMPARED_WITH, HIGHER_THAN, etc.

Predicates are triggered via **indicator rules** linking lexical cues (e.g., “treat”, “treatment with”) to relation types, guided by syntactic patterns and ontological constraints.

> → ~1,366 indicator rules are implemented, ensuring interpretable, rule-based inferenceBroad-coverage biomedical relat….

------

## **4. Evaluation**

### **Datasets**

- **SemRep Test Collection** – 500 sentences (1,371 relations)
- **BioCreative V CDR Corpus** – chemical-disease causal relations (500 abstracts)

### **Results**

| Evaluation                | Precision | Recall | F1 Score | Notes                                     |
| ------------------------- | --------- | ------ | -------- | ----------------------------------------- |
| **SemRep Test (Strict)**  | 0.55      | 0.34   | 0.42     | Exact match                               |
| **SemRep Test (Relaxed)** | 0.69      | 0.42   | 0.52     | Allows concept substitution               |
| **CDR (All Relations)**   | 0.90      | 0.24   | 0.38     | Includes cross-sentence pairs             |
| **CDR (Sentence-only)**   | 0.90      | 0.35   | 0.50     | Fair since SemRep operates sentence-level |

SemRep outperforms neural and hybrid baselines in **precision** but lags in recall due to its sentence-bounded, rule-based natureBroad-coverage biomedical relat….

------

## **5. Error Analysis**

Top error categories (from ~ 1,000 cases)Broad-coverage biomedical relat…:

- **NER / Normalization (26.9%)** – MetaMap/UMLS ambiguity.
- **Argument identification (14%)** – syntactic rule limitations.
- **Trigger detection (12.5%)** – missing or deactivated indicators.
- **POS tagging & parsing (≈ 5%)** – mainly gerunds/participles.

Overall, most errors arise from limitations in MetaMap’s concept mapping and underspecified syntactic rules.

------

## **6. Comparative Perspective**

- Competing neural systems (e.g., Peng et al. 2020) achieve higher F1 on CDR but require large annotated corpora.
- SemRep, by contrast, is **knowledge-driven**, **interpretable**, and **generalizable** without retraining, serving as a **strong baseline** and a foundation for **SemMedDB**Broad-coverage biomedical relat….

------

## **7. Impact and Applications**

Through **SemMedDB**, SemRep supports a wide range of downstream tasks:

- **Drug repurposing** (e.g., linking drugs to novel indications)
- **Clinical decision support** and **diagnosis assistance**
- **Adverse event detection** and **drug–drug interaction mining**
- **Literature-based discovery (LBD)** and **hypothesis generation**
- **Biomedical QA** and **semantic summarization**Broad-coverage biomedical relat….

It also powers external platforms such as the **NCATS Biomedical Data Translator**, where SemMedDB relations were key to identifying rare-disease treatmentsBroad-coverage biomedical relat….

------

## **8. Future Directions**

Authors plan a **Java re-implementation** (beyond Prolog) for:

- Modular architecture & easier integration of modern NLP tools (e.g., GNormPlus, Stanford CoreNLP)
- Improved NER and negation handling
- Incorporation of **pronominal anaphora resolution**, **dependency parsing**, and **coordination ellipsis recognition**
- Selective UMLS vocabulary use to reduce ambiguityBroad-coverage biomedical relat….

------

## **9. Key Takeaways**

- **SemRep** is a mature, interpretable, linguistically grounded relation-extraction system for biomedical text.
- It prioritizes **precision and explainability** over recall and learning-based generalization.
- As the engine behind **SemMedDB**, it remains one of the most influential resources for **biomedical knowledge graph construction** and **literature-driven discovery**.