# Clinical Trial Information Extraction with BERT (CT-BERT)

## Introduction

Clinical trial designs are documented in unstructured text and insights from natural language processing (NLP) of the documents can be useful in new trial design. We introduce a framework called CT-BERT to extract entities and relations from the clinical trial documents. CT-BERT uses BERT models for named entity recognition (NER) and a hybrid approach for relation extraction including rule-based and machine learning-based models. The details are described in a short paper [1] and an extend paper [2].

## CT-BERT entity types and relation types

The rationale for clinical trial NER is that the entity types must allow us to capture the key variables (entities) in trial design. For eligibility criteria, we leveraged the annotation schema in [3] for type definition. It includes 15 types, covering common types in clinical text (e.g., disease, treatment, clinical variable) as well as specialty types (e.g., consent, language frequency, technology access) and value ranges (e.g., lower and upper bound). We treat the lower and upper bound entities as “attribute” entities because they are modifiers of other base entities. See Table 1 for details.

Table 1. Entities and attributes

| | Entity Type | Example |
| --- | --- | --- |
| Entity | Age | age |
| | Allergy name | Hypersensitivity to nivolumab |
| | BMI | Body mass index |
|	| Cancer | Thymoma |
| | Chronic disease | Inflammatory disease |
|	| Clinical variable | Arterial Oxygen Saturation |
|	| Contraception consent | not willing to use double barrier methods of contraception |
|	| Ethnicity | Residents of Puerto Rico |
|	| Gender | Men and women |
| | Language fluency | Unable to read |
| | Pregnancy | Pregnant |
| | Technology access | Electronic patient diary |
| | Treatment | Solid organ transplantation \| immune-checkpoint inhibitors |
| Attribute | Lower bound | Age over 18 years |
| | Upper bound | daily opioid dose <= 160 mg/day |

We define two types of relations between base entities and attribute entities: has_value and has_temp (temporal value). Table 2 shows the details of the relation types. In “has_value” relation, lower bound and upper bound are values of subject entities; in “has_temp” relation, lower bound and upper bound are temporal constraints, e.g., “two weeks prior”. Non-numeric temporal constraints such as “in the past” are not captured.

Table 2. Relationships
| Relation | Entity | Attribute | Example |
| --- | --- | --- | --- |
| has_value | Age \| BMI \| Clinical variable | Lower bound \| Upper bound | “Rosen Modified Hachinski ischemic score” {clinical variable} <has_value> “less than or equal to 4” {upper bound} |
| has_temp | Allergy name \| Cancer \| Chronic disease \| Pregnancy \| Clinical variable \| Treatment | Lower bound \| Upper bound | “Antibiotic therapy” {treatment} <has_temp> “in the two weeks prior to the start of the study” {upper bound} |

## NER

We adapted the Clinical Trial Parser dataset in [3] for BERT NER. The original data uses its own off-set representation schema. We transformed the dataset into the standard BIO representation schema. The training data includes 102,985 entities in 40,876 criteria sentences (data_ner/train.txt) and the test data includes 11,317 entities in 4,482 criteria sentences (data_ner/valid.txt).

The data can be used to train NER models using pre-trained BERT models in the HuggingFace model library (https://huggingface.co/models). There are several useful packages to train NER models, such as BERT-NER [4], transformers-ner [5], and ClinicalTransformerNER [6].

## Relation Extraction (rule-based)

The rule-based method performs heuristic search of subject-attribute entity pairs from the NER result. Refer to the sample Jupyter notebook “relation_extraction_Rule-based.ipynb”.

## Relation Extraction (machine learning-based)

We created annotated datasets of relations between entities in clinical trial eligibility criteria, including the full data (data_re/allrealtions/train.tsv) and a subset of 2000 random samples (data_re/2000relations/train.tsv). The data can be used to train many types of machine learning models, including support vector machine, condition random field, and deep learning-based classification. 

For illustration, we trained BERT-based models using a recent package [7] with our relation training data. Refer to the sample Jupyter notebook “relation_extraction_ML-based.ipynb”, which shows how to use a trained BERT-classification model to classify candidate relations using a sample data set.

## References

1.	Liu X, Hersch G, Khalil I, Devarakonda M. Clinical trial information extraction with bert. In: IEEE International Conference on Healthcare Informatics. 2021 Aug 9.
2.	Liu X, Hersch G, Khalil I, Devarakonda M. Clinical trial information extraction with bert. BMC Biomedical Informatics and Decision Making. (to appear)
3.	Tseo Y, Salkola MI, Mohamed A, Kumar A, Abnousi F. Information extraction of clinical trial eligibility criteria. arXiv preprint arXiv:2006.07296. 2020 Jun 12.
4.	https://github.com/weizhepei/BERT-NER 
5.	https://github.com/liuyukid/transformers-ner 
6.	https://github.com/uf-hobi-informatics-lab/ClinicalTransformerNER 
7.	https://github.com/uf-hobi-informatics-lab/ClinicalTransformerRelationExtraction

