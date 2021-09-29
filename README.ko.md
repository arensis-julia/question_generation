*Language: [English](README.md)*

# Project Details
자연어 처리 분야(NLP)에서 어떤 문맥과 그에 해당하는 질문이 주어졌을 때 그에 맞는 답을 생성해내는 QA task는 중요하게 생각하는 tasks 중 하나이다. 그러나 이를 위한 데이터셋은 문맥, 질문, 답 모두가 제공되어야하기 때문에 사람이 직접 만들어내기에는 너무 많은 노력이 든다. 이 때 question generation task는 문맥이 주어졌을 때 질문을 생성하도록 하는 task인데, 그 노력을 완화해주는 역할을 한다고 볼 수 있다.<br>

따라서 이번 프로젝트에서는 한국어 버젼의 question generation과 answer generation을 동시에 하는 모델을 생성하고자 하였다. 이를 위해 데이터셋으로는 [KLUE dataset](https://github.com/KLUE-benchmark/KLUE)의 MRC 데이터를 사용하였으며, 사전 학습된 모델로는 한국어로 학습된 T5 모델인 [KE-T5](https://github.com/AIRC-KETI/ke-t5)를 사용하였다. 작업은 [patil-suraj](https://github.com/patil-suraj/question_generation)의 작업을 참고하였으며, 사용하는 library의 버젼을 좀 더 업그레이드 하였다.<br><br>

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