# SPARQA: question answering over knowledge bases

Codes for paper: "SPARQA: Skeleton-based Semantic Parsing for Complex Questions over Knowledge Bases" (AAAI-2020) [detail](https://www.aaai.org/Papers/AAAI/2020GB/AAAI-SunY.3419.pdf).
If you meet any questions, please email to him (ywsun at smail.nju.edu.cn).

## Project Structure:

<table>
    <tr>
        <th>File</th><th>Description</th>
    </tr>
    <tr>
        <td>code</td><td>codes</td>
    </tr>
    <tr>
        <td>dataset</td><td>datasets and files for preprocessing </td>
    </tr>
    <tr>
        <td>skeleton</td><td>skeleton bank of complex questions</td>
    </tr>
    <tr>
        <td>slides</td><td>slides and poster</td>
    </tr>
</table>
 
## Requirements
* See [requirements.txt](https://github.com/nju-websoft/SPARQA/blob/master/code/requirements.txt)

## Configuration
* Root of dataset: default D:/dataset. Note that you can edit it in common/globals_args.py. 

## Common Resources
* GloVe (glove.6B.300d) [download](https://nlp.stanford.edu/projects/glove/). Default save in the root.
* Stanford CoreNLP server. [download](https://stanfordnlp.github.io/CoreNLP/corenlp-server.html), get started, and replace ip_port address in globals_args.py with URL of your CoreNLP.
* Python wrapper for Stanford CoreNLP's SUTime Java library. [download](https://github.com/FraBle/python-sutime). Default save in root/resource_sutime/. Note that if you save other place, replace the sutime_jar_files in configution with your path.
* BERT pre-trained Models download [download](https://drive.google.com/drive/folders/1tlUF7ALLLXiHu280gPdlVyQlGvJFklGC) and default save the seven files in the root/pre_train_models.
* Four preprocessing files (stopwords.txt, ordinal_fengli.tsv, unimportantphrase, and unimportantwords) [download](https://drive.google.com/drive/folders/1VT6UkPDhtJ3EN0rP9bM3N80OA8jK3TA6), and save in the root.
* Two version Freebase: [latest version](https://developers.google.com/freebase) and 2013 version (email SPARQA author, if you can not find the version)
* Download a virtuoso server and load the KBs. The [file](http://ws.nju.edu.cn/blog/2017/03/virtuoso%E5%AE%89%E8%A3%85%E5%92%8C%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE/) is helpful, if you meet questions.

## Specific CWQ 1.1 Resources
* Configure odbc address of virtuoso server (freebase_pyodbc_info) in common/globals_args.py. 
* Configure html address of virtuoso html (freebase_sparql_html_info) in common/globals_args.py. 
* Five BERT pre-trained models for skeleton parsing. [download](https://drive.google.com/drive/folders/1t4Rb2feVOSGF_5lRBHwrB_GfxyL2rqby) and save the five files in the default root/dataset_cwq_1_1.
* Entity-related Lexicons and schema-related lexicons. [download](https://drive.google.com/drive/folders/1t4Rb2feVOSGF_5lRBHwrB_GfxyL2rqby) and save in the default root/kb_freebase_latest.
* CWQ 1.1 dataset. [download](https://github.com/nju-websoft/SPARQA/tree/master/dataset/dataset_cwq_1_1).

## Specific GraphQuestions Resources
* Configure odbc address of virtuoso server (freebase_pyodbc_info) in common/globals_args.py. 
* Configure html address of virtuoso html (freebase_sparql_html_info) in common/globals_args.py. 
* Five BERT pre-trained models for skeleton parsing. [download](https://drive.google.com/drive/folders/1Mjpan599INCVRgRQTsirgVdyt29iKblO) and save the five files in the default root/dataset_graphquestions.
* Entity-related Lexicons and schema-related lexicons. [download](https://drive.google.com/drive/folders/1Mjpan599INCVRgRQTsirgVdyt29iKblO) and save in the default root/kb_freebase_en_2013.
* GraphQuestions dataset. [download](https://github.com/nju-websoft/SPARQA/tree/master/dataset/dataset_graphquestions).

## Run SPARQA Pipeline
The pipeline has two steps for answering questions: 

* (1) KB-indenpendent graph-structured ungrounded query generation.
* (2) KB-dependent graph-structure grounded query generation and ranking.

See running/freebase/pipeline_cwq.py if run CWQ 1.1.
See running/freebase/pipeline_grapqh.py if run GraphQuestions.
Below, an example on GraphQuestions.

### Specific-dataset Configuration

* Set datset in the common/globals_args.py: q_mode=graphq. (note that q_mode=cwq if CWQ 1.1)
* Set skeleton parsing in the common/globals_args.py: parser_mode=head, which means skeleton parsing. (note that parser_mode=dep, which means dependency parsing).
* Replace the freebase_pyodbc_info and freebase_sparql_html_info in the common/globals_args.py with your local address.

### KB-indenpendent query generation
* Run KB-indenpendent query generation. Setup variable module=1.0. The input: graph_questions_filepath. The output: structure_with_1_ungrounded_graphq_file.

### KB-dependent query generation
* Generate variant generation. Set variable module=2.1. The input: structure_with_1_ungrounded_graphq_file. The output: structure_with_2_1_grounded_graph_file.
* Ground candidate queries. Set module=2.2. The input: structure_with_2_1_grounded_graph_file. The output: structure_with_2_2_grounded_graph_folder.
* Rank using word-level scorer. Set module=2.3_word_match. The input: structure_with_2_2_grounded_graph_folder.
* Combine sentence-level scorer and word-level scorer. Set module=2.3_add_question_match. The input: structure_with_2_2_grounded_graph_folder.
* Run evaluation. Set module=3_evaluation. The input: structure_with_2_2_grounded_graph_folder. The output: results.

## Skeleton Models
* Five pre-trained models in the dataset_graphquestions/fine_tuning_models_graphq_0905.

## Multi-strategy Scoring Models
* Word-level scorer. The code is in grounding/ranking/path_match_nn/train_test_path_nn.py. The trained model is in dataset_graphquestions/data_path_match/models
* Sentence-level scorer. The code is in paraphrase_classifier_interface.py in BERT folder. The trained model is in dataset_graphquestions/data_question_match

## Compare with Baselines
* GraphQuestions: PARA4QA, SCANNER, UDEPLAMBDA.
* CWQ 1.1: PullNet, SPLITQA, and MHQA-GRN. Note that PullNet used annotated topic entities of questions in its KB only setting. SPARQA, an end-to-end method, do not use annotated topic entities. Thus, it is not comparable.

## Citation

	@inproceedings{SunZ0Q20,
	  author    = {Yawei Sun and Lingling Zhang and Gong Cheng and Yuzhong Qu},
	  title     = {{SPARQA:} Skeleton-Based Semantic Parsing for Complex Questions over Knowledge Bases},
	  booktitle = {The Thirty-Fourth {AAAI} Conference on Artificial Intelligence, {AAAI} 2020, The Thirty-Second Innovative Applications of Artificial Intelligence Conference, {IAAI} 2020, The Tenth {AAAI} Symposium on Educational Advances in Artificial Intelligence, {EAAI} 2020, New York, NY, USA, February 7-12, 2020},
	  pages     = {8952--8959},
	  publisher = {{AAAI} Press},
	  year      = {2020},
	  url       = {https://aaai.org/ojs/index.php/AAAI/article/view/6426},
	}

## Contacts
If you have any difficulty or questions in running codes, reproducing experimental results, and skeleton parsing, please email to him (ywsun at smail.nju.edu.cn).
