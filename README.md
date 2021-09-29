*Language: [한국어](README.ko.md)*

## Project Details
In Natural Language Processing (NLP), QA, the task of generating answer of the question with the given context, is one of the important tasks to solve. However, the datasets for QA tasks are hard to be human-generated since they should include all context, questions, and answers, causing lots of efforts. Thus, here comes the question generation task, which generates question when the context is given, alleviating the labour taken to generate dataset.<br>

So, the objective of this project is to make Korean-version model of both question generation and answer generation. For this, we used MRC data of [KLUE dataset](https://github.com/KLUE-benchmark/KLUE), and used Korean-pretrained T5 model [KE-T5](https://github.com/AIRC-KETI/ke-t5). We followed the work of [patil-suraj](https://github.com/patil-suraj/question_generation)'s, upgrading the version of used library.<br><br>

# HOW IT WORKS
![model structure](images/model_structure.png)
<br>

# EXAMPLE
![example](images/example.png)
<br>

# REQUIREMENTS
```
nltk
datasets
transformers = 4.3.0
torch = 1.8.1
```
<br>

# EXECUTION
- Data Preprocessing
```
python prepare_data.py \
    --max_source_length 512 \
    --max_target_length 32 \
    --train_file_name train_data_qaqg_t5.pt \
    --valid_file_name valid_data_qaqg_t5.pt
```
- Training
```
python run_qg.py \
    --model_name_or_path KETI-AIR/ke-t5-base \
    --tokenizer_name_or_path t5_qaqg_tokenizer \
    --output_dir ke-t5_base_qaqg \
    --train_file_path data/train_data_qaqg_t5.pt \
    --valid_file_path data/valid_data_qaqg_t5.pt \
    --per_device_train_batch_size 8 \
    --per_device_eval_batch_size 8 \
    --gradient_accumulation_steps 8 \
    --learning_rate 1e-4 \
    --num_train_epochs 10 \
    --seed 42 \
    --do_train \
    --do_eval \
    --logging_steps 100
```
- Evaluation
```
python eval.py \
    --model_name_or_path ke-t5_base_qaqg \
    --valid_file_path data/valid_data_qaqg_t5.pt \
    --num_beams 4 \
    --max_decoding_length 32 \
    --output_path results.txt
```