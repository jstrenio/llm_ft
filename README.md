# Fine Tuning an LLM on a small consumer grade GPU using qLORA
With the recent developments in model performance as well as memory
efficiency in smaller more manageable models inference as well as 
modified training methods have become possible on modest home
computers. After the impressive release of mistral 7b I wanted to
experiment with it locally and see if I could train a variant 
entirely locally. This repo will serve as my hub for 
experimentation.

### Initial Results
I started by scraping ~8k threads and posts from the 
newschoolers.com forums, a long established online community for
freestyle skiers.

I formatted the threads into a convserational layout that aligned
with mistral's chat-tuned model, used every memory reducing method
I could find and hit train(). Training with only batch size of 2
and only 150 steps (300 rows of data of the 8k) training/eval loss
both seemed to be decreasing steadily and healthily and example 
prompts look amazing. Improvements to the training set, playing
with hyperparameters and more substantial training to come.

### Citations:
https://wandb.ai/byyoung3/ml-news/reports/Fine-Tuning-Mistral-7B-on-Python-Code-With-A-Single-GPU---Vmlldzo1NTg0NzY5
https://github.com/iamarunbrahma/finetuned-qlora-falcon7b-medical/blob/main/funetuned_qlora_falcon7b.ipynb
https://github.com/georgesung/llm_qlora/blob/main/QloraTrainer.py
https://github.com/titi-devv/mistral7b-finetuned/blob/main/mistral_finetune.ipynb
https://github.com/geronimi73/qlora-minimal/blob/main/qlora-minimal.ipynb
https://sumanthrh.com/post/distributed-and-efficient-finetuning/
