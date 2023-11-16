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

## Formatting to Fit Conversational Format
My initial finetune of mistral 7b was performed on the conversationally finetuned variant to leverage its ability and comprehension of the question/answer format. I also chose to format the input data in the same 2 party \[Question\] | \[Answer\] format to further simplify the process. I intend to experiment on finetuning from the base model as well as formatting inputs in the \[Question\] | \[Context\] | \[Response\] format as well in subsequent tests.

## Hyperparamters + QLoRA Config + Training
There are a number of configurations involved in the optimizations required for training on 8GB VRAM along with the traditional hyperparameters involved in training a neural net. Firstly I utilized the bitsandbytes library to quantize my model to 4bit. A model size can be determined by multiplying the number of parameters by the size of the datatype. For example, If you’re using 16bit floats (2 bytes) for 7 billion parameters, you get ~14GBs (2 bytes * 1024^3) however the required memory for calculating gradients, the optimizer and additional overhead reaches a complexity that will ultimately answer the question of will I run out of memory or not. I tried two new tools to gauge the requirements for training, LLM check quoted me at 7GB necessary VRAM while Huggingface’s Model Memory Utility Quoted me at 14GB. Unsure of whether I would OOM, I also utilized Huggingface’s Accelerator Class, which handles memory management and attempts to shard and offload segments of the model to the CPU when faced with memory limitations on the GPU. The QLoRA paper notes impressive results despite very low rank values and to again try to keep everything as small as possible, I began with an r value of 2, setting my alpha to 16 to closely align with the authors ratio between the two related values. I kept my batches small as well using a batchsize of only 2 however I utilized gradient accumulation to simulate batching with a value of 4, effectively simulating a batch size of 8. Finally I utilized the SFTTrainer() class inplace of the standard Trainer() class as its recommended specifically for finetuning using QLoRA.

## Loss + Insights + Next Steps
<center>

![Description](https://i.imgur.com/4R17TFL.jpg)

<br><br>
My initial training run was focused on not running out of memory which is why I tried to follow standard hyperparameter settings to start. Regardless, the train/test loss decreases in a very predictable pattern, most notably with the test loss beginning to vary more greatly towards the end, potentially indicating it reaching the limit of what it can learn from the training set. Knowing the fairly wide variety of content within the training data, one potential next step will be to increase the dataset size as well as refine its content.

</center>


## Example Outputs:

 what's your favorite line traveling circus episode? 

I think the one where they go to Japan is my fav. I love how they just went there and did a bunch of street skiing in Tokyo, it was so sick! The whole trip was so fun to watch. Also the one with them in the park in Korea was really good too. They had some crazy tricks going off that big jump at the end. Definitely worth watching if you haven’t already. 10/10 would recommend. If you want to see more of their stuff check out their instagram @travelingcircus_skiing. They have some other cool videos on there too. Hope this helps! The one where they go to Japan is my fav. I love how they just went there and did a bunch of street skiing in Tokyo, it was so sick! The whole trip was so fun to watch. Also the one with them in the park in Korea was really good too. They had some crazy tricks going

------------------------------------------------------------------------------------------------------------------------
 how do you get to chad's gap from the bottom of alta? 

I think it’s called the “chads gap” for a reason. I don’t know if there is an easy way to get there, but I would imagine that you have to be pretty skilled at skiing and have some experience with backcountry travel. If you are not familiar with backcountry travel, I would recommend taking a course or getting some guidance before attempting to go into the backcountry. It can be very dangerous if you don’t know what you are doing. Also, make sure to check the weather forecast and avalanche conditions before heading out. Good luck! **This post was edited on Jan 10th 2023 at 9:58:47pm **This post was edited on Jan 10th 2023 at 9:58:47pm **This post was edited on Jan 10th 2023

------------------------------------------------------------------------------------------------------------------------
 what new movie are you going to see this year? 

I'm not seeing any movies, I'll be skiing instead.  But if I had to pick one, it would probably be the TGR movie.  I don't know why but I just like their style of filming and editing.  Plus they always have some sick clips in there.  I also heard that they're doing a tour with the movie so maybe I'll catch it at one of those.  Either way, I'm stoked for the season!  Let's go! I'm gonna watch all of them.  I've been waiting for the last few years to see the TGR movie because I love their style of filming and editing.  I'm really looking forward to seeing it.  I'm also gonna check out the Level 1 movie since I haven't seen one in a while.  I'm sure it will be good too. 

------------------------------------------------------------------------------------------------------------------------
 how is the durability of line skis? 

I have a pair of 2018 revs and they are still going strong. I’ve had them for about 3 years now and they haven’t shown any signs of wear or tear. They’re my favorite ski so far, but I don’t beat on them too hard. I’m sure if you were to abuse them they would break down faster than most other skis. But overall I think they hold up pretty well. I’d say they’re definitely worth it if you want something that will last a long time. Just make sure you get the right size! I've got some 2019 revs and they're holding up great. I've been riding them for 4 seasons now and they look like new. Line makes good quality gear. If you take care of your skis they should last forever. I've never heard of anyone breaking their lines. I know people who ride

------------------------------------------------------------------------------------------------------------------------
 where should I go to college? 

I'm not sure what you mean by "where should I go" but if you want to ski a lot, there are tons of colleges with good parks and mountains nearby. If you want to be in the industry, look at schools that have strong business or marketing programs. If you want to be a pro skier, look for schools that offer scholarships for athletes. If you want to be an engineer, look for engineering schools. If you want to be a doctor, look for medical schools. If you want to be a teacher, look for education schools. If you want to be a writer, look for writing schools. If you want to be a musician, look for music schools. If you want to be a photographer, look for photography schools. If you want to be a filmmaker, look for film schools. If you want to be a chef, look for culinary schools. If you want to be a nurse, look for nursing schools. 

------------------------------------------------------------------------------------------------------------------------
 What's your favorite segment of all time? 

https://www.newschoolers.com/videos/watch/986704/Jake-Mageau---The-Saga-of-the-Bump-2013--Full-Length-Ski-Movie-HD-MOVIE-Trailer-by-TGR-Productions-and-Level-1-Productions-Featuring-Andri-Ragettli-Tom-Wallisch-Henrik-Harlaut-Aaron-Blunck-Nick-Goepper-Darren-Shapiro-and-more-Skiers-Snowboarders-and-Freeskiers-in-Colorado-and-Utah-Mountains-with-Music-by-Kendrick-Lamar-and-Odesza-and-More-Soundtrack-Music-by-Grouper-and-Panda-B

------------------------------------------------------------------------------------------------------------------------
 what do you think of armada? 

I like their graphics and the way they market themselves. I don't really know much about them though, but I have a pair of ARV 106s that are pretty sick. They're my first skis so I can't really compare them to other brands, but I love em! Armada is definitely a good brand in my book. I also like how they support local skiing and stuff like that. I feel like they're trying to make a difference in the industry. I'm not sure if that's true or not, but it seems like they care about the community. That's all I got for ya. Hope this helps! Let me know if you have any more questions. They’ve been around forever and have some of the best graphics in the game. Their quality has gone downhill over the years but they still make some solid skis.

------------------------------------------------------------------------------------------------------------------------
 what do you think of the game shredsauce? 

Its a fun little game, but I'm not sure how it would be as an actual skiing simulator. It seems like it could be fun to play with friends and mess around in, but I don't know if there is any kind of progression or customization. If you can make your own skis that would be cool. But overall its just a fun little game. I'd probably play it for a few hours before getting bored. Its definitely worth checking out though! I've only played it on my phone so I'm not sure how well it plays on PC. I'll have to try it out sometime. I'm curious about the physics engine too. How does it compare to something like Steep? Does it feel realistic at all? Or is it more of a cartoony style physics engine? I'm curious because I haven't really seen anything like this before. I'm sure someone will let us know soon

------------------------------------------------------------------------------------------------------------------------
 who would win in a game of slvsh, will wesson or andy parry? 

Andy Parry. Will Wesson is just too much of a pussy to fight. He's always been afraid of getting hurt so he doesn't do anything crazy like that. Andy Parry on the other hand has no fear and will go all out. I think he could take down any skier in the world if they were to have a slvsh match. But then again, it's hard to say because you never know what can happen when two people are fighting each other. It's not like skiing where you can just throw tricks and land them perfectly every time. In a fight, there's no telling what might happen. So it's really anyone's game. But I think Andy Parry would be the one to come out on top. Just my opinion though. I’d bet money on Andy Parry. Will is way more of a pussy than most people give him credit for.

------------------------------------------------------------------------------------------------------------------------
 what place did lj get in xgames realski? 

I think he got 10th. I'm not sure though, I don't really follow skiing comps that much. But I know he was pretty hyped on his insta story after the event. He said something like "I can't believe I just competed at X Games" or something like that. So I assume he did well. I hope he gets more recognition for it too, he deserves it. He's one of my favorite skiers and has been putting out some sick content lately. I'd love to see him do a few more comps this season. Maybe even try to make it onto the US team for the Olympics next year. That would be insane! I'll have to check out the results later today when I get home from work. Good luck LJ! He got 9th. I saw it on his story but I didn’t watch the whole thing so I’m not sure who

------------------------------------------------------------------------------------------------------------------------
 What are Tanner Hall's biggest accomplishments? 

I think he is the most influential skier of all time. He was a pioneer in so many ways and his style has been copied by so many people. I don’t know if there is anything else to say about him other than that. He is one of my favorite skiers of all time. His skiing is so unique, it’s like nothing you have ever seen before. It’s hard to put into words how much he has influenced me as a skier. I would say he is probably the best skier of all time. I am not sure what else to say about him. He is just an amazing skier. I love watching him ski. He is such a good skier. He is one of the greatest skiers of all time. He is one of the best skiers of all time. He is one of the best skiers of all time. He is one of the best skiers of all time. He is one of the

------------------------------------------------------------------------------------------------------------------------
Should Henrik focus on filming or contests? 

I think he should do both. He's a great skier and I think he has the potential to be one of the best in the world, but he also has a lot of style and creativity that could make him a really good filmer too. I think it would be cool if he did more stuff like "The Art of Skiing" with Tanner Hall. That was such an amazing movie and I think it would be cool to see what kind of movies he could come up with. But at the same time, he's still young and he needs to keep competing so he can get better and build his resume. So maybe he should just focus on comps for now and then when he gets older he can start doing more film projects. Either way, I think he's got a bright future ahead of him. He's already one of my favorite skiers and I know he'll only get better from here. He’s definitely

------------------------------------------------------------------------------------------------------------------------
Skis measurements: I’ve skied for 13 years now and I’m very embarrassed but anyone want to explain ski measurements?  

I'd say you should get a pair of 180s. They are the perfect size for me, and I'm 5'9" with a 6 foot wingspan. You can always go bigger if they feel too small, but it's hard to make them smaller once you buy them. Also, don't worry about the length of your skis being too long, as long as you have enough room to swing them around in the air, you will be fine. If you find that you need more control, just put on some stiffer bindings or use a shorter ski. Good luck! **This post was edited on Jan 2nd 2022 at 4:27:22pm I would recommend getting a pair of 180cm skis. That is what I ride and I am 5'10". The longer the better when it comes to park

------------------------------------------------------------------------------------------------------------------------
Epic sunset shredding with my pup: [URL]https://youtu.be/TCRIRT4gBO[/URL]A  

I'm not sure if you can post links to your own videos here, but I think this is a great idea! It would be cool to see more of these kinds of clips. I love the way it looks like you and your dog are just cruising down the mountain together. Keep up the good work! :)

------------------------------------------------------------------------------------------------------------------------
Congrats Eileen Gu!!: My girlfriend just won gold I’m so proud!!  

Congratulations! That's awesome. She is a super talented skier and it's great to see her succeeding in the Olympics. I hope she continues to do well in the future. Also, congratulations to all of the other athletes who are competing at the games. It's always cool to watch them compete on such a big stage.

------------------------------------------------------------------------------------------------------------------------
Stolen Skis: Broke my arm yesterday and had to ski to the ski patrol hut and some fuck stole them while I was in there. Pair of J Skis Vacation mallards 180 with some tyrolia attacks on them. lmk if anyone sees them around. Was at Mt. La Crosse in WI  

Damn that sucks man, hope you get better soon! If you're ever up here again we can probably help ya out. Good luck! Also, what happened to your arm? Did you fall or something? Hopefully it wasn't too bad. We got a pretty sick medical team here so hopefully they were able to take care of ya. Either way, good luck! And yeah, if you need anything just let us know. We'll be happy to help ya out. Peace! -Matt (Ski Patroller) @ Mt. LaCrosse Ski Area, Wisconsin.

------------------------------------------------------------------------------------------------------------------------
Returned from Hell(bent): https://heritagelabskis.com/products/hb122  

I'm not sure if you can get a refund on these, but I would definitely try to find out before you buy them. If they are the same as last year, then it is worth the risk. But if they have changed at all, then you might be screwed. Good luck! I hope you enjoy your new skis! :)

------------------------------------------------------------------------------------------------------------------------
Jackson or targhee: I’m going to my grandmas for New Years weekend and she lives fairly close to both Jackson hole resort and grand targhee. I’ve never been to either but I’m going to go to one of them on Saturday with my brothers. Which one is a better choice for those that have been? We are heavy park skiers but love us some quality runs with nice side hits, drops, etc… rougher terrain. Which has a better park?  

Targhee has the best park in Idaho imo. But if you want more of a resort vibe then JH would be your bet. Targhee is pretty much just a mountain with no real town around it. JH has a lot of stuff to do besides skiing. It's also closer to Salt Lake so you can get there faster. If you like a big resort vibe with lots of amenities then JH is probably the way to go. If you want a smaller resort vibe with less crowds and more natural terrain then Targhee is the way to go. Both places have great parks though. Just depends what you're looking for. Also, if you're going to JH make sure to check out the Jackson Hole Rodeo. It's a fun time. And if you're going to Targhee make sure to hit up the Mormon Row area. 

------------------------------------------------------------------------------------------------------------------------
Kinky rail ❤️: I walk by this rail almost everyday and it’s naaaasty. Would I hit it? Yes? Would I successful slide through the whole rail? Probably not for a while, but my god i wished it snowed here so many nice rails and urban features around my campus. Fucking shame it’s in the south :/  

I'm sure you could get some good footage of it though! Maybe even do a little backyard setup with some pvc or something. You can always just use your imagination to make it look like you're on a kinky rail. It'll be fun! And if you don't have any friends who are into skiing then maybe you should start looking for them. They might be able to help you out with that kind of stuff. Good luck! :)

------------------------------------------------------------------------------------------------------------------------
  What are mogul skiers like?: https://www.newschoolers.com/videos/watch/988376/The-Mayrand-podcast--4---Mikael-Kingsbury  

I'm not a mogul skier but I know they have to be pretty good at skiing and jumping. They also have to be able to handle the speed of going downhill in moguls, which is pretty fast. They also need to be able to handle the bumps and jumps that come with moguls. Overall, mogul skiers are pretty skilled athletes.

------------------------------------------------------------------------------------------------------------------------
  What if a Full Tilt, SPK and Apex had a baby?

I'd buy them. I have no idea what they would look like but I'm sure it would be sick. I've always wanted to see a ski boot with a full tilt heel piece. It would be so cool. Imagine the durability of a full tilt heel piece combined with the lightness of an apex or spk. That would be insane. I hope someone makes this happen one day. I'll pay good money for them too. I just want something that is both durable and lightweight. I don't know how you can make a ski boot that is both but I think full tilt could do it. I'm not saying I wouldn't still use my spks because I love them but I'd definitely try out some full tilts if they were made. I'd also be down to see a full tilt toe piece in an apex or spk. I think it would be really cool. 

------------------------------------------------------------------------------------------------------------------------
Is blunt hard: When I first was learning grabs with spins I went for safety because everyone says its the easiest. But I quickly found myself corking out everytime because of reaching down for the grab and I would land backseat a lot. Then I tried blunt and not only was it easier for me to spot the landing but it naturally helped bring the spin around because to look for the blunt I had to turn my head anyways. IDK I think blunt might be a good grab for people trying grabs with spins for the first time.  

I'm pretty sure you can do both at the same time, just gotta get your hand in the right position. Blunts are pretty easy to learn once you figure that out though. Safety is still a solid option if you want to keep it simple tho. Just gotta make sure you don't reach down too much or you'll end up backseating. Good luck! :)

------------------------------------------------------------------------------------------------------------------------

### Citations:
https://wandb.ai/byyoung3/ml-news/reports/Fine-Tuning-Mistral-7B-on-Python-Code-With-A-Single-GPU---Vmlldzo1NTg0NzY5
https://github.com/iamarunbrahma/finetuned-qlora-falcon7b-medical/blob/main/funetuned_qlora_falcon7b.ipynb
https://github.com/georgesung/llm_qlora/blob/main/QloraTrainer.py
https://github.com/titi-devv/mistral7b-finetuned/blob/main/mistral_finetune.ipynb
https://github.com/geronimi73/qlora-minimal/blob/main/qlora-minimal.ipynb
https://sumanthrh.com/post/distributed-and-efficient-finetuning/
