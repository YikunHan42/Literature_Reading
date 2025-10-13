## **1. Motivation**

- **Background:**
  - **SemRep** is a rule-based, broad-coverage biomedical relation extraction (RE) tool used to populate **SemMedDB**, a PubMed-scale repository of semantic subject–predicate–object triples.
  - SemRep’s **precision is moderate (≈0.69), but recall is low (≈0.42)**, meaning many valid relations are missed.
- **Need:**
  - Improving recall is critical for downstream tasks (drug repurposing, literature-based discovery, clinical decision support) that rely on SemMedDB.
  - Modern **Transformer-based RE models** have shown strong performance on narrow tasks but have not been fully leveraged to augment SemRep.

**Objective:** Investigate whether a **relation classification model**, using SemRep-recognized entities as input, can complement SemRep and expand the coverage of SemMedDB.

------

## **2. Approach**

### **2.1 Data**

- **Sources:**
  1. **SemRep Gold Standard (SemRep-GS):** 500 sentences, 1,346 gold triples covering 26 relation types.
  2. **SemRep Accuracy datasets (SemRep-Acc / Acc+):** ~6,492 + 1,400 SemRep-predicted triples manually labeled for correctness.
- **Focus:**
  - Retained **22 positive relation types** (e.g., *affects, causes, predisposes, treats*), excluding rare and negated types.
  - Generated **negative examples** from SemRep false positives and other entity pairs.
- **Dataset size:** 8,255 train / 761 validation / 3,841 test (≈12.9K instances, 53.5% positive).

------

### **2.2 Model**

- **Base encoder:** **PubMedBERT** (domain-specific BERT).
- **Enhancements:**
  - **Contrastive pre-training** with UMLS-EDA augmented data to improve representation of semantic relations.
  - **Entity representation:** compared three approaches —
    1. Raw entity mentions
    2. **Semantic types** (fine-grained, UMLS-based)
    3. Semantic groups (coarse-grained)
  - Best results came from **semantic-type representation**.
- **Classifier:** Uses [CLS] token + two entity embeddings (mean-pooled) → feedforward + softmax.
- **Thresholding:** Optimized per-relation thresholds on validation set for best F1.

------

### **2.3 Evaluation**

- Metrics: precision, recall, F1 (macro/micro), AUC.
- Benchmarked against **SemRep baseline**.
- Large-scale test on **12K PubMed abstracts (2000-2023)** sampled from **SemMedDB v43**.
- Manual quality check of **506 new triples** predicted by the model but missed by SemRep.

------

## **3. Key Results**

### **3.1 Test-Set Performance**

- **Best model:** baseline + semantic-type + contrastive w/ threshold
  - **F1 (micro): 0.70 vs 0.46 for SemRep**
  - **Precision: 0.62 vs 0.61 (≈unchanged)**
  - **Recall: 0.81 vs 0.37 (↑44 percentage points)**.
- **Gains:** especially large in *administered_to (+0.52 F1), affects (+0.48), predisposes (+0.38), interacts_with (+0.38)*.
- SemRep remained better for *compared_with (+0.09 F1)*.
- About **59% of correct model predictions were unique** (missed by SemRep), while 10% of correct SemRep predictions were unique.

------

### **3.2 Large-Scale PubMed Assessment**

- Model predicted **103,999 relations** vs **51,564 by SemRep** (22 relation types) in the 12K abstracts.
- **77,541 relations were novel** (not in SemMedDB).
- Manual check of 506 novel relations → **67% correct**, implying the model can maintain SemRep-like precision while adding substantial new content.
- Extrapolated to entire PubMed (~36M abstracts): **could roughly double SemMedDB’s size** from ≈126M to ≈253M triples.
- Biggest relative gains in *diagnoses (+325%)* and strong gains in key **causal/mechanistic relations** (*affects, causes, augments, stimulates, inhibits*).

------

## **4. Insights**

- **Semantic-type input outperforms entity mentions or semantic groups** → helps generalization by focusing on concept-level patterns.
- **Contrastive pre-training** showed modest improvements, mainly for minority classes.
- **Model and SemRep are complementary:** hybrid approach can maximize coverage.
- **Attention visualization** revealed the model often learned SemRep-like trigger rules but occasionally confused similar relations (*predisposes vs. augments*), pointing to need for finer ontological distinctions.

------

## **5. Limitations & Future Work**

- Relied on SemRep’s entity recognition (MetaMap), which introduces errors; better NER could further improve performance.
- Some relation types and all negated relations were excluded due to limited data → future work to expand coverage.
- Some model predictions were too generic (e.g., *treatment treats syndrome*); post-processing such as SemRep’s novelty filters could improve specificity.
- Future plans: run the model across full SemMedDB, release predictions, explore richer augmentation strategies for rare labels.

------

## **6. Contributions**

1. **Demonstrated that relation classification with PubMedBERT + semantic-type representation substantially boosts recall while retaining precision.**
2. **Model predictions complement SemRep** → combined system could double SemMedDB’s size, enhancing downstream tasks (literature-based discovery, drug repurposing, clinical decision support).
3. Provided **open-source data & code:** [GitHub link](https://github.com/Michelle-Mings/SemRep_RelationClassification).

------

## **7. Take-Home Message**

A **hybrid rule-based + neural relation-classification approach** is practical and effective for scaling biomedical knowledge extraction.

**Improved recall (0.81 vs 0.37) without major loss in precision** means more comprehensive biomedical knowledge graphs, enabling richer literature mining and discovery.