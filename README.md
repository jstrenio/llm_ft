<center>

# Freeski Chatbot: Local LLM Finetune on a Single Consumer GPU using QLoRA

![skier](https://i.imgur.com/K0XrhY0.jpg)

</center>

## Base Model Selection
The current boom in performance of state-of-the-art large language models and subsequent research into the major contributing factors into these notable improvements has not only led to further performance gains in SOTA models, but has also led to improvements in smaller models as well. MistralAI’s newly released 7 billion parameter open-source model is one such LLM helping bridge the gap between the compute power of a single consumer computer and the performance of cutting edge models.

## QLoRA + Quantization
Research into optimizations on how the models are fundamentally represented and how they are trained has also helped bring the power of LLMs to personal computers. Model Quantization is a technique that leverages findings that the precision used to represent the parameters of a model can be reduced with an initially small degradation of output quality, reducing the required memory to work with these models. Low Rank Adapters (LoRA), or more specifically Quantized LoRA (QLORA) is a technique that simplifies the fine-tuning process of models by utilizing a specialized module that enables the adjustment of specific model parameters to influence the base model’s outputs, significantly reducing the computational complexity of otherwise updating all of the model’s parameters.

## Computer Specs
These  techniques in conjunction with a smaller model and a reasonably modern consumer-grade computer system can allow for the customization and deployment of modern LLMs without the use of a large distributed system from a major cloud provider. The following tests have been conducted on a home computer with the following specs:

OS: Linux  
CPU: 12 core 3.6 GHz Intel i7 12700k  
RAM: 32 GB DDR5  
GPU: 8 GB VRAM NVIDIA RTX 3070  

## Scraping Technique + Dataset
Some of the key factors mentioned above in improved model performance were found to be, to no surprise, related to training dataset quality, highlighting the importance of a well curated dataset. As an ex-professional skier, I’d like even an early iteration of a personal assistant to be well versed in freeskiing. Thankfully there exists a long running online community forum for freeskiers known as newschoolers with a dedicated subforum for ski related discussion with 10s of thousands of threads spanning the course of over 2 decades. Being a public forum, scraping thread discussions only required iterating across pages of threads, and pages of quesiton/responses within. My initial scrape pulled up to the first 10 pages of ~8,000 threads.

![threads](https://i.imgur.com/RMHMmKy.jpg)

## Formatting to Fit Conversational Format
My initial finetune of mistral 7b was performed on the conversationally finetuned variant to leverage its ability and comprehension of the question/answer format. I also chose to format the input data in the same 2 party \[Question\] | \[Answer\] format to further simplify the process. I intend to experiment on finetuning from the base model as well as formatting inputs in the \[Question\] | \[Context\] | \[Response\] format as well in subsequent tests.

![threads](https://i.imgur.com/FMqwBk1.jpg)

## Hyperparamters + QLoRA Config + Training
There are a number of configurations involved in the optimizations required for training on 8GB VRAM along with the traditional hyperparameters involved in training a neural net. Firstly I utilized the bitsandbytes library to quantize my model to 4bit. A model size can be determined by multiplying the number of parameters by the size of the datatype. For example, If you’re using 16bit floats (2 bytes) for 7 billion parameters, you get ~14GBs (2 bytes * 1024^3) however the required memory for calculating gradients, the optimizer and additional overhead reaches a complexity that will ultimately answer the question of will I run out of memory or not. I tried two new tools to gauge the requirements for training, LLM check quoted me at 7GB necessary VRAM while Huggingface’s Model Memory Utility Quoted me at 14GB. Unsure of whether I would OOM, I also utilized Huggingface’s Accelerator Class, which handles memory management and attempts to shard and offload segments of the model to the CPU when faced with memory limitations on the GPU. The QLoRA paper notes impressive results despite very low rank values and to again try to keep everything as small as possible, I began with an r value of 2, setting my alpha to 16 to closely align with the authors ratio between the two related values. I kept my batches small as well using a batchsize of only 2 however I utilized gradient accumulation to simulate batching with a value of 4, effectively simulating a batch size of 8. Finally I utilized the SFTTrainer() class inplace of the standard Trainer() class as its recommended specifically for finetuning using QLoRA.

## Loss + Insights + Next Steps
<center>

![Description](https://i.imgur.com/4R17TFL.jpg)

<br><br>
My initial training run was focused on not running out of memory which is why I tried to follow standard hyperparameter settings to start. Regardless, the train/test loss decreases in a very predictable pattern, most notably with the test loss beginning to vary more greatly towards the end, potentially indicating it reaching the limit of what it can learn from the training set. Knowing the fairly wide variety of content within the training data, one potential next step will be to increase the dataset size as well as refine its content. A spotcheck of some example outputs shows the output distribution has been positively affected, with the most notable difference being the tone. While some instances show signs of knowledge distillation, given the current training parameters, the answers don't seem to stray far from the basemodel's.

</center>


## Example Outputs:

 QUESTION:  
 Jackson or targhee: I’m going to my grandmas for New Years weekend and she lives fairly close to both Jackson hole resort and grand targhee. I’ve never been to either but I’m going to go to one of them on Saturday with my brothers. Which one is a better choice for those that have been? We are heavy park skiers but love us some quality runs with nice side hits, drops, etc… rougher terrain. Which has a better park? 

GROUND TRUTH:  
Targhee without a doubt. Targhee and Jackson are the only 2 places I’ve been to out west and Jackson will always be a shit hole on the holidays. All the in bounds hiking terrain at targhee is great. Side hits are amazing there is a little natural half pipe right under the main lift I forget what it’s called tho you’ll see it. Also targhees parks are better too.</s> [INST] Targhee for sure if you'r

ORIGINAL MODEL:  
 Both Jackson Hole Resort and Grand Targhee offer great skiing experiences, but they differ in terms of their terrain and amenities.Jackson Hole Resort is known for its steep and challenging slopes, as well as its world-renowned halfpipe. It also offers a wide range of terrain, from beginner to expert level, and has several high-speed chairlifts to help you get around quickly. However, the resort 

FINETUNED MODEL:  
Targhee has the best park in Idaho imo. But if you want more of a resort vibe then JH would be your bet. Targhee is pretty much just a mountain with no real town around it. JH has a lot of stuff to do besides skiing. It's also closer to Salt Lake so you can get there faster. If you like a big resort vibe with lots of amenities then JH is probably the way to go. If you want a smaller resort vibe.

------------------------------------------------------------------------------------------------------------------------

QUESTION:  
 What are mogul skiers like?: https://www.newschoolers.com/videos/watch/988376/The-Mayrand-podcast--4---Mikael-Kingsbury 

GROUND TRUTH:  
mogal skiing is lame</s> [INST] Not if you actually know how to ski. [/INST]Mogul skiers love full tilts and look pivots. They are our brothers from another mothers</s> [INST] We will accept them then [/INST]Moguls is the best form if comp skiing, especially when they got them racing side by side.</s> [INST] change my mind [/INST]I've always liked that a lot</s> [INST] makes us sound like those su

ORIGINAL MODEL:  
 Mogul skiers, also known as freestyle skiers, are athletes who specialize in performing tricks and stunts on snowy slopes. They typically perform their routines in moguls, which are large bumps or mounds of snow that are created by grooming machines.In the video you linked to, Mikael Kingsbury is a professional mogul skier who has won numerous World Cup titles and Olympic medals. He is considered

FINETUNED MODEL:  
I'm not a mogul skier but I know they have to be pretty good at skiing and jumping. They also have to be able to handle the speed of going downhill in moguls, which is pretty fast. They also need to be able to handle the bumps and jumps that come with moguls. Overall, mogul skiers are pretty skilled athletes.

------------------------------------------------------------------------------------------------------------------------
QUESTION:  
 What if a Full Tilt, SPK and Apex had a baby?: **This thread was edited on Oct 21st 2020 at 8:21:07pm 

GROUND TRUTH:  
I should start a ski company called partial tilt, where the boots rise to ur knees, completely eliminating shin bang</s> [INST] Or just have the boots cover each side of your body, and zip together in the center, creating an impenetrable suit and eliminating all pain. [/INST]completely eliminating movement</s> 

ORIGINAL MODEL:  
 It's not possible for a Full Tilt, SPK, and Apex to have a baby as they are all different types of products.Full Tilt is a brand that produces high-end gaming chairs, while SPK is a brand that produces audio equipment such as speakers and headphones. Apex is a brand that produces gaming gear such as mice, keyboards, and headsets.While it might be interesting to imagine what a hybrid product from 

FINETUNED MODEL:  
I'd buy them. I have no idea what they would look like but I'm sure it would be sick. I've always wanted to see a ski boot with a full tilt heel piece. It would be so cool. Imagine the durability of a full tilt heel piece combined with the lightness of an apex or spk. That would be insane. I hope someone makes this happen one day. I'll pay good money for them too. I just want something that is bot

------------------------------------------------------------------------------------------------------------------------
QUESTION:  
 Is blunt hard: When I first was learning grabs with spins I went for safety because everyone says its the easiest. But I quickly found myself corking out everytime because of reaching down for the grab and I would land backseat a lot. Then I tried blunt and not only was it easier for me to spot the landing but it naturally helped bring the spin around because to look for the blunt I had to turn my head anyways. IDK I think blunt might be a good grab for people trying grabs with spins for the first time. 

GROUND TRUTH:  

ORIGINAL MODEL:  
 Blunt is generally considered one of the easier grabs to learn, especially when combined with spins. It involves using your body weight to generate momentum and power, which can help you maintain control and avoid landing in the back seat. Additionally, blunt allows you to focus on turning your head and spotting the landing, which can make it easier to execute the grab successfully.

FINETUNED MODEL:  
I'm pretty sure you can do both at the same time, just gotta get your hand in the right position. Blunts are pretty easy to learn once you figure that out though. Safety is still a solid option if you want to keep it simple tho. Just gotta make sure you don't reach down too much or you'll end up backseating. Good luck! :)

------------------------------------------------------------------------------------------------------------------------
QUESTION:  
 Tips on how to improve style: I can hit rails, 360, BS 270 out, etc. but on film it doesn’t look clean (arm flail & shit like that) any tips on how to stomp shit more smooth, keep things tight n steezy? 

GROUND TRUTH:  
Also how to get smooth enough w rotations to send em onto rails/ front/back swap</s> [INST] Sadly though there isn’t, you just gotta practice and be comfortable enough to make those adjustments to your tricks. [/INST]Get soft skis and huge pants</s> [INST] hope this helps [/INST]put your hands in your pockets when you do tricks. But tbh most people don’t like their own voice their own face their o

ORIGINAL MODEL:  
 Improving your style in snowboarding involves a combination of technique, practice, and mindset. Here are some tips to help you stomp tricks smoothly and maintain a tight, steady style:1. Focus on balance: Maintaining good balance is crucial for executing smooth, controlled turns and tricks. Practice balancing on different types of terrain, such as flat ground, slopes, and jumps, to develop a str

FINETUNED MODEL:  
I'm not a coach or anything but I think the best way is just to watch a lot of skiing videos and try to emulate what you see. It's also good to have someone who knows what they are doing watch your footage and give feedback. Also don't be afraid to try new tricks and stuff, sometimes trying something different will help you find a better way to do it. Good luck!

------------------------------------------------------------------------------------------------------------------------

QUESTION:  
 Ski suggestions: Looking for a powder tool something that can slash pow but still carve decent of groomers, I thinking something on the wide side, 110 and up. 

GROUND TRUTH:  
Black Crows Nocta</s> [INST] vishnu wets perfect for the job [/INST]exactly what i had my mind on</s> [INST] JJs [/INST]

ORIGINAL MODEL:  
 Based on your preferences, here are some ski recommendations that could be suitable for you:1. Atomic Vantage 95 CTI: This ski is designed to provide excellent performance in both powder and groomed terrain. It has a 95mm waist width and a dual-core construction that provides stability and responsiveness. The CTI (Carving Technology Inserts) technology allows for precise control when carving turn

FINETUNED MODEL:  
I'd say look into the armada ARV 96 or the ON3P Wet 98. Both are great all mountain skis with good park performance too. The Armada is more playful while the ON3P is stiffer and more stable at high speeds. If you want to go wider, the ON3P Magma 104 is a solid choice. It's a little heavier than the other two though. I personally have an ON3P Wet 98 and love it. It's my first pair of skis that I bo

------------------------------------------------------------------------------------------------------------------------

QUESTION:  
 Affordable living in SLC: What's up NS! I'm a junior at UW and was wondering what living options were out there for a 4 month lease to ski and take classes online this winter. Is it a pretty pricey spot to live in and do Univ. of Utah / Westminster homies ride Solitude at all? I have an ikon so ill prolly be there mostly. 

GROUND TRUTH:  
Watching this thread...</s> [INST] ^^ [/INST]I took a gap year and rode solitude last season. very worth it.</s> [INST] your best bet for finding a 4 month lease is by looking in the classified section on KSL and finding a random person with an open room willing to do a short term thing. as far as being a pricey spot to live, it depends on what you consider pricey. it definitely used to be cheaper

ORIGINAL MODEL:  
 Hey there! Salt Lake City is definitely an affordable place to live, especially compared to other major cities in the US. There are plenty of housing options available for short-term leases like 4 months. Some popular options include apartments, houses, and even some hostels or vacation rentals.As for skiing, Solitude Mountain Resort is located about an hour south of Salt Lake City, which makes i

FINETUNED MODEL:  
I’ve lived in Salt Lake my whole life, and I can tell you that the housing market is insane right now. It’s not just because of covid, but also because of the Olympics next year. If you want to live in the city, expect to pay around $1500+ per month for a decent apartment. You could try looking into some of the smaller towns outside of the city like Park City or Ogden, but even those are getting expensive.

------------------------------------------------------------------------------------------------------------------------

### Citations:
https://wandb.ai/byyoung3/ml-news/reports/Fine-Tuning-Mistral-7B-on-Python-Code-With-A-Single-GPU---Vmlldzo1NTg0NzY5
https://github.com/iamarunbrahma/finetuned-qlora-falcon7b-medical/blob/main/funetuned_qlora_falcon7b.ipynb
https://github.com/georgesung/llm_qlora/blob/main/QloraTrainer.py
https://github.com/titi-devv/mistral7b-finetuned/blob/main/mistral_finetune.ipynb
https://github.com/geronimi73/qlora-minimal/blob/main/qlora-minimal.ipynb
https://sumanthrh.com/post/distributed-and-efficient-finetuning/
