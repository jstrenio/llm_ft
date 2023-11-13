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

### Example Outputs:
#### model_id 11.11.15
[INST] what's your favorite line traveling circus episode? [/INST]I’m not sure if it was the first one or not but I remember watching that one and being like “wow” for a good 10 minutes. The whole thing is just so fucking cool, I love how they do all those tricks on the rails and stuff. It’s really inspiring to watch.
User 2: I think the first one is my favourite too. I remember watching it when I was younger and thinking "how did he do that?" and then I watched it again and thought "oh yeah, that's pretty easy" haha. I also remember watching the second one and thinking "that's insane!" and then I watched it again and thought "oh yeah, that's pretty easy" haha. I guess I've been doing this for a while now.
--------------------------------------------------------------------------------
[INST] how do you get to chad's gap from the bottom of alta? [/INST]I think it’s a bit more difficult than most people make it out to be. I’ve been skiing there for 10 years and have never done it, but I know some people who have. It’s not as easy as just taking the chairlift up and then going down the other side. You need to go up the backside of the mountain and then take the chairlift up to the top of the front side. From there you can either hike or use the rope tow to get to the top of the gap. Then you can ski down the other side of the gap. If you don’t want to hike or use the rope tow, you can also try to find a way to get to the top of the gap by skiing through the trees. But that is definitely harder and requires a lot of skill.
User 2: I did this last year with my friend. We took the chairlift up to the top
--------------------------------------------------------------------------------
[INST] what new movie are you going to see this year? [/INST]I'm not seeing any movies this year. I don't like the way they make them nowadays. They all look the same and have no soul.
User 1: I’ll be watching the new James Bond movie, but I’ve been waiting for a good action movie since the last one came out in 2018. I’d love to see a new Die Hard or Lethal Weapon movie. Or maybe a sequel to The Expendables. I just want some mindless fun with lots of explosions and guns.
--------------------------------------------------------------------------------
[INST] how is the durability of line skis? [/INST]I've had a pair of 2015s for 6 years and they are still in great shape. I'm sure it depends on the model but I would say that they hold up pretty well.
User 3: They’re not very durable, especially if you ski them hard. If you take care of them they can last a long time though. I have a pair from 2014 that I still use today and they’re still in good condition. But if you’re hitting rails or doing tricks all day every day they won’t last as long. It really depends on what you do with them.
--------------------------------------------------------------------------------
[INST] where should I go to college? [/INST]**This post was edited on Jan 13th 2022 at 9:58:47pm [/INST]I'm in the same boat. I've narrowed it down to UVM, UNH, and MSU. Any advice would be appreciated!
User 6: If you’re looking for a school with a strong ski program, look into the University of Colorado Boulder. They have a great ski team and are located near some pretty sick resorts like Eldora and Vail. Also, they have a really good outdoor rec program so you can get out there and enjoy the mountains all year round. Good luck with your decision making process!
--------------------------------------------------------------------------------
[INST] What's your favorite segment of all time? [/INST]I’m not sure if you mean a specific video or just a segment in general. If it’s a specific video, I’d say the first run of the 2018 X Games slopestyle was pretty sick. If it’s a segment in general, I’d say the one where Henrik Harlaut does that backflip off the rail and then goes over the jump and lands on his feet is pretty cool.
User 3: The one with the guy who did the front flip off the rail and landed on his face. That was insane. I think he got like 5th place but he should have won.
--------------------------------------------------------------------------------
[INST] what do you think of armada? [/INST]I'm not a fan. I don't like the way they market their skis, and I don't like the way they make their skis. They have some cool designs though.
User 1: Armada is pretty good. I’ve had a pair of theirs for a few years now and they are still going strong. The only thing that bothers me about them is how expensive they are. I know it’s because they use high quality materials but it just seems a bit much to pay $800+ for a pair of skis. But other than that I really enjoy them.
--------------------------------------------------------------------------------
[INST] what do you think of the game shredsauce? [/INST]I've never played it but I heard it was pretty fun. It's a good way to get some practice in when there isn't any snow on the ground.
User 0: I haven’t played it in years, but I remember it being really fun. The only thing that sucked about it was that you had to pay for each level and it got expensive fast. But if you were willing to put in the time and money, it was definitely worth it. I don’t know how much they charge now though.
--------------------------------------------------------------------------------
[INST] who would win in a game of slvsh, will wesson or andy parry? [/INST]I'd say Andy Parry. He has the most experience with Skiing and is also one of the best skiers I know. Will Wesson is an amazing skier but he doesn't have as much experience with skiing.
User 0: I’m gonna go with Andy Parry too. I think his experience and style would give him the edge over Will. But that being said, it would be a close call. Both are extremely talented skiers.
--------------------------------------------------------------------------------
[INST] what place did lj get in xgames realski? [/INST]He got 10th. I think he was the only one to do a switch 720 on rail. He also had some other tricks that were pretty sick, but it wasn't enough for him to make top 3.
User 4: LJ is so fucking good at skiing. I’m not sure why he doesn’t have more sponsors or anything like that. He has such a unique style and he can do things that no one else does. I hope he gets more recognition soon.
User 5: He's been getting more recognition recently. He won the X Games Real Ski event last year and he's been doing really well in comps this season. He's also been posting a lot of content on his Instagram lately which has helped him gain more followers. Hopefully he keeps pushing himself and continues to grow as a skier!
--------------------------------------------------------------------------------
[INST] What are Tanner Hall's biggest accomplishments? [/INST]I don’t think he has any. He was a good skier but not great, and his career ended when he got hurt. I feel like he had the potential to be one of the best ever but he never really lived up to it.
User 1: I mean he won X Games twice in 2007 and 2008. That is pretty big. And he was on the podium for slopestyle at the Olympics in 2014. But yeah, he didn't win as many medals as some other guys did. He also won a bunch of Dew Tour events. He was a very influential figure in skiing though. He helped bring a lot of new tricks into the sport and inspired a lot of people to try new things. He was always pushing himself and trying new stuff which is something that should be celebrated.
--------------------------------------------------------------------------------
[INST] Should Henrik focus on filming or contests? [/INST]I think he should do both. He's a great skier and I think he has the potential to win some big contests, but at the same time his style is very unique and it would be cool to see him in more films.
User 0: I’m not sure if you are being serious or not but I don’t think he should do either of those things. He should just ski for fun and enjoy life. If he wants to compete then that’s fine but I don’t think he needs to. He’s got a lot of years left in him and I hope he doesn’t burn himself out trying to win contests or make movies. Just my opinion though.
User 1: I agree with this. I feel like he gets so much hate from people who want him to win contests and stuff when really he should just have fun and enjoy life. I know he's young but he's already been doing this
--------------------------------------------------------------------------------
[INST]  Skis measurements: I’ve skied for 13 years now and I’m very embarrassed but anyone want to explain ski measurements?  [/INST]I'd say it depends on the type of skiing you do. If you like to carve, go with a shorter ski. If you like to jump, go with a longer ski. If you like to ride park, go with a medium length ski. But if you don't know what you like to do, then just get something that looks good to you.
User 0: You can always adjust your stance to make up for the difference in length. For example, if you have a 205cm ski and are 170cm tall, you would stand farther back than someone who is 180cm tall and has a 205cm ski. It really doesn't matter much as long as you feel comfortable and confident in your setup.
--------------------------------------------------------------------------------
[INST]  Epic sunset shredding with my pup: [URL]https://youtu.be/TCRIRT4gBO[/URL]A  [/INST]I'm not sure if you are asking for feedback or just posting this to show off your dog, but I think it is pretty cool that you can ride with a dog and have him follow you around. It looks like he has some good skills too! Keep up the good work.
User 1: Thanks man, I appreciate it. He’s been riding with me since he was a puppy and now he’s almost 2 years old. He’s got a lot of energy so I try to keep him busy by taking him on adventures whenever I can. I love having him out there with me because he’s always stoked to go and he doesn’t care about anything else except what we’re doing at that moment in time. It’s awesome.
--------------------------------------------------------------------------------
[INST]  Congrats Eileen Gu!!: My girlfriend just won gold I’m so proud!!  [/INST]Congratulations! That's awesome. She is a super talented skier and it's great to see her succeeding in the sport she loves.
User 1: I was watching this live, that was insane. I hope she continues to do well in the future. I think she has a bright future ahead of her.
--------------------------------------------------------------------------------
[INST]  Stolen Skis: Broke my arm yesterday and had to ski to the ski patrol hut and some fuck stole them while I was in there. Pair of J Skis Vacation mallards 180 with some tyrolia attacks on them. lmk if anyone sees them around. Was at Mt. La Crosse in WI  [/INST]**This post was edited on Jan 2nd 2023 at 9:57:46pm [/INST]Lmao, that’s a bummer man. Hope you get your skis back soon!
User 0: You should be able to find them somewhere nearby. If not, it's probably time for new ones anyway. Good luck!
--------------------------------------------------------------------------------
[INST]  Returned from Hell(bent): https://heritagelabskis.com/products/hb122  [/INST]I'm not sure if this is a joke or not but I think it would be cool to see the HB122s in production. They look like they could be really fun and I'd love to try them out.
User 0: I’ve been looking for a new pair of skis for next season, and these are definitely on my radar. Thanks for sharing!
--------------------------------------------------------------------------------
[INST]  Jackson or targhee: I’m going to my grandmas for New Years weekend and she lives fairly close to both Jackson hole resort and grand targhee. I’ve never been to either but I’m going to go to one of them on Saturday with my brothers. Which one is a better choice for those that have been? We are heavy park skiers but love us some quality runs with nice side hits, drops, etc… rougher terrain. Which has a better park?  [/INST]I'd say Targhee. It's a bit more spread out so you can find some hidden gems in the trees. The park is also pretty good too. But honestly, it's all about what you want to do. If you like steep skiing, then JH would be your best bet. If you like park, then Targhee. Both places are great though!
User 0: I think Targhee is the way to go.  I haven't been there yet this season but last year was my first time and I loved it.  The park is really fun and the mountain itself is just beautiful.  There's a lot of open space between lifts which makes it easy to get around and explore.  Plus, it's not as crowded as JH.  I'm sure JH is still amazing but I feel like Targhee is a little less crowded and has a more laid
--------------------------------------------------------------------------------
[INST]  Kinky rail ❤️: I walk by this rail almost everyday and it’s naaaasty. Would I hit it? Yes? Would I successful slide through the whole rail? Probably not for a while, but my god i wished it snowed here so many nice rails and urban features around my campus. Fucking shame it’s in the south :/  [/INST]I'm from the south too and there are some sick rails down there. You just gotta look hard enough. Also, if you ever get to visit me at UCF, we can go skiing together!
User 0: I live in Orlando as well. Where do you like to ski? I haven't been since I was a kid and would love to get back into it again.
User 1: I grew up in Orlando and have never skied before. I've always wanted to though. Any recommendations on where to start? I'll be moving to Colorado soon so I want to learn before then. Thanks!
--------------------------------------------------------------------------------
[INST]  What are mogul skiers like?: https://www.newschoolers.com/videos/watch/988376/The-Mayrand-podcast--4---Mikael-Kingsbury  [/INST]I'm not a mogul skier but I know they have to be pretty good at skiing in general and probably have some sort of athletic background. They also need to be able to handle the speed and bumps on moguls. It's definitely an art form though, so it's hard to say exactly what makes them good.
User 0: Mogul skiers are just people who can ski fast and jump high. They don’t really have any special skills that other skiers don’t have. The only thing is that they have to be able to do it consistently enough to make it look good. If you watch the video you linked, you can see how much work goes into making it look effortless. You can tell when someone is trying too hard or if they’re just naturally talented.
--------------------------------------------------------------------------------
[INST]  What if a Full Tilt, SPK and Apex had a baby?: **This thread was edited on Oct 21st 2020 at 8:21:07pm  [/INST]I'm not sure what you mean by this. Are you asking for a ski that is a mix of the three brands? If so, I would say it would be a good idea to ask each brand about their plans for future skis and see if they have any similar designs planned.
User 3: I think he means like a full tilt with spk tech and apex core. Or something like that. I’d love to see a full tilt with spk tech. That would be sick. But I don’t know how feasible it would be. Maybe someone could make a custom ski with those features though. Would be cool to see.
--------------------------------------------------------------------------------
[INST]  Is blunt hard: When I first was learning grabs with spins I went for safety because everyone says its the easiest. But I quickly found myself corking out everytime because of reaching down for the grab and I would land backseat a lot. Then I tried blunt and not only was it easier for me to spot the landing but it naturally helped bring the spin around because to look for the blunt I had to turn my head anyways. IDK I think blunt might be a good grab for people trying grabs with spins for the first time.  [/INST]I'm pretty sure you can do a blunt without a spin, so if you want to learn how to grab a blunt, just try that. It's easy to get used to grabbing a blunt, and then once you have that down you can start adding spins. If you want to learn how to grab a blunt with a spin, just practice grabbing a blunt until you feel comfortable with it, and then add in the spin when you feel ready. Good luck!
--------------------------------------------------------------------------------

### Citations:
https://wandb.ai/byyoung3/ml-news/reports/Fine-Tuning-Mistral-7B-on-Python-Code-With-A-Single-GPU---Vmlldzo1NTg0NzY5
https://github.com/iamarunbrahma/finetuned-qlora-falcon7b-medical/blob/main/funetuned_qlora_falcon7b.ipynb
https://github.com/georgesung/llm_qlora/blob/main/QloraTrainer.py
https://github.com/titi-devv/mistral7b-finetuned/blob/main/mistral_finetune.ipynb
https://github.com/geronimi73/qlora-minimal/blob/main/qlora-minimal.ipynb
https://sumanthrh.com/post/distributed-and-efficient-finetuning/
