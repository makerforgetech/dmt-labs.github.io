---
layout: post
title: "Interview with Emir Hamurcu"
date: 2023-10-24
categories: [Interviews, Robotics]
tags: [robotics, interview, biped, modular robotics framework]
image: /assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231020_100900_524.jpg
---

Emir Hamurcu is a 17 year old maker from Turkey who has been working on a bipedal robot using the Modular Robotics Framework. He very kindly allowed me to interview him about his project and his journey into robotics.

## Can you introduce yourself and share a bit about your background and how you got into robotics and making?

Hello, I'm Emir Hamurcu. I'm 17 years old and I live in the Defne district of Hatay, Turkey. I'm a maker who loves to create creative projects and a young programmer. My interests have been oscillating between mechatronics and software since my childhood. I learned to speak at the age of 6 and started disassembling electronic devices at the age of 3.5-4. My curiosity about how machines work and my desire to create new things grew over time. In my younger years, I was often sought after by people in my community to fix their devices. However, I never asked for money for this service because every time I opened a device, I was learning new things about the mechanisms or circuits inside. These experiences allowed me to accumulate valuable knowledge and expand my expertise.

My interest in the world of electronics gradually shifted towards the world of software in the following years. Towards the end of 6th grade, I decided to take my first steps in programming. I started with C++, and learning to program excited me. However, my curiosity about mechanical engineering continued at the same time. While contemplating how to combine these two interests, I encountered Arduino. Arduino was a perfect platform for developing both software and mechanical projects. After learning Arduino, I delved into programming languages such as Java, Python, C#, and developed a passion for learning these languages.

In the 10th grade, I had an opportunity to develop a meaningful project for competitions. I created a bionic arm for disabled individuals. This arm could be controlled using a brain-computer interface and included a Raspberry Pi-based tablet. I developed software to assist disabled individuals in their daily lives and integrated these programs into my own operating system, "SentryOS." I successfully passed the initial screening with this project, but I narrowly missed making it to the final round. On the competition day, I attended as an audience member and was greatly impressed, especially by the project that won first place in the category for disabled individuals. This project involved a simple parking sensor created using an HC-SR04 sensor and a buzzer. This experience made me realize the limited emphasis on science in our country. As a result, I decided to go to a country where I could find more support and opportunities, and I knew that I needed to work hard for it.

After long contemplation, the thought of finding a way to go abroad became a recurring loop in my mind, which motivated me to work harder.

During this process, I used to describe myself as a lazy person, and I also had issues such as social anxiety and overthinking, which made it difficult for me to communicate with others at school. However, these challenges directed me towards working more and improving myself. For two years, I immersed myself in learning various programming languages and programs, including "html, css, js, react, ts, bootstrap, nodejs, php, sql, TF, opencv" while also learning languages like Java, Python, and C#. I had just completed the first semester of the 11th grade, and learning these languages made me very happy. I believed that I could stand out in job applications after graduating.

I always had a dream of creating an artificial intelligence robot project, but I thought I didn't have enough time to start and my morale was low. However, I never forgot about this project and promised myself to bring it to life one day. After the first semester of the 11th grade ended, on the first day of the second semester, February 6th at 4:17 a.m., a series of earthquakes occurred in our country, affecting 11 provinces, including the one I lived in. This disaster resulted in schools being closed for a long time, as well as issues like electricity, water, and internet disruptions. There were certainly many downsides brought by the earthquake. However, a lot changed for me. After the earthquake, my life resembled the early days of my life. During this period, I made new friends, began to overcome my anxiety to some extent, and also came across the idea of developing a bionic robot with artificial intelligence, thanks to Dan Nicholson. His framework fascinated me. Since schools were closed for an extended period, I needed a different activity, and this framework caught my attention because of its flexibility and modularity. To be honest, I had no idea how to start, how to progress, or where to find materials, but I realized that it was very helpful for progress. When I couldn't find the materials, I started producing my own parts - such as solenoids, screws, nuts, spacers - using 3D printing. At the same time, I learned advanced modeling with Fusion 360 while doing this, making my robot more modular and successfully building my own robot with additional features using the framework. Such an accomplishment provides incredible self-confidence because you are doing a project you first encountered on your own and adding different additional features yourself, which ignites creativity and curiosity in me.


## Can you introduce your project and what inspired you to start working on it?

![Front view](/assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231020_191424_567.jpg){: .w-50 .right}
My project focuses on developing an open-source robotics platform. This platform will be flexible and customizable in terms of both software and hardware, making it a useful tool for those working on robotics projects. The platform will be easily accessible for both beginners and experienced developers.

What inspired me to start this project was the fascinating developments in the field of robotics and the sharing of open-source communities. I also believe that this project can contribute to the popularization of robotic technology. Making robotic technology more understandable and accessible to a wider audience can open doors to future innovations.

The decision to start working on this project stems from my interest in robotics and my desire to contribute to technology. Additionally, the collaboration of open-source communities, individuals like Dan, and the sharing of knowledge, motivates me. Working towards the success of this project and increasing my contributions to the field of robotics is what inspires me.


## What motivated you to use the modular robotics framework for your project?

![Teaching](/assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231020_100905_387.jpg){: .w-50}
_Emir meeting the president of the province where he lives_

In the beginning, we can see that this software's modular structure is evident from its name. Specifically, the advantages of running the entire system with a library logic from a main script can be discussed. This approach lightens the computational load and reduces complexity. For example, this framework has a folder named "modules," and each module has its own library (e.g., "tts.py" or "neopix.py"). The main script imports these libraries as follows: "from modules.neopix import NeoPx." This way, we import the relevant library to use the functionality of each module.

Secondly, a single processor or board can lead to problems like freezing or lagging. However, this framework utilizes both a brain (main computer) and a development board (like Arduino). This minimizes issues such as freezing and lagging. In terms of task distribution, the brain makes all decisions, carries out functions like hearing and vision, and sends signals. Arduino, on the other hand, directs the legs based on incoming commands, much like the way human beings function.

Thirdly, this framework is versatile and entirely customizable. For instance, in the original repository, Raspberry Pi and Arduino Pro Mini are used, but in your version, you can use different components like NVIDIA Jetson Nano and Arduino Pro Micro. This broadens the application areas of the framework and enhances its functionality. It can be used in various areas, such as a desktop companion, home security, car camera, recording family memories, timelapse photography, an assistant (e.g., a legged Alexa), pet monitoring, a ChatGPT chatbot, creating memories for a time capsule, and many more.

Finally, the software contains a variety of different modules, greatly enhancing the framework's functionality. For example:

- Actuators: Controls servo motors.
- Behaviors: Contains command files that can be thought of as personalities, such as "dream," "feel," "motion," and "respond."
- Image Processing: Computer vision modules related to Google Coral or OpenCV and Python.
- Chatbot: Can be used to create a chatbot.
- Melody Playing and Morse Code: A module for playing melodies with a buzzer and speaking in Morse code.
- Serial Communication: Supports serial communication with Arduino.
- Speech Recognition: Including an optional Snowboy hotword module.
- Animation: Used to control animations.
- Battery Status: Monitors the battery's current status and provides alerts for critical conditions.
- Other Modules: Includes BTWrapper, LogWrapper, Microwave Radar, Keyboard and Gamepad Control, Neopixel Control, RPi Temperature Monitoring, Speech Input, Translation, and Power Management, among others.

This framework provides a flexible and customizable foundation for users to use in various projects.


## What customizations have you made to the framework to suit your project's needs and your creative vision?

![Side](/assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231020_191450_012.jpg){: .w-50 .right}

I have made several customizations to the framework to align it with the needs of our project and our creative vision. Here are the key changes I've implemented:

1. **Camera Integration:** I added an older action camera to the system, which is connected to the project via USB with the Nano to provide visual input.

2. **Additional Modules:** I introduced various modules to enhance functionality within the project. These modules include a separate RFID module, stereo audio output, speakers on the right and left sides, and a panel providing additional audio output.

3. **Charging Section:** To further improve the charging section of the system, I added two Type-C connection points and a DC barrel jack to the back. I plan to add USB B and USB A outputs to this section, making it possible to charge external devices like smartphones.

4. **Layout Change:** I repositioned the Arduino Pro Micro and MPU6050 within the project to optimize its design.

5. **Cable Management:** To prevent cable clutter within the leg model, I improved cable management by using 3D model drag chains.

6. **Overhead RCW Module:** I developed a model that can move overhead, thus enhancing the project's mobility.

7. **I2S MEMS Microphones:** I strategically placed I2S MEMS microphones within the head for audio input and output. There's one on the right side and one on the left side. Additionally, the buzzer LLC buzzer is integrated into this section. This arrangement ensures that the audio output corresponds to the input direction. For instance, if the input is coming from the right microphone, the system will play the sound through the right speaker.

8. **Bluetooth Wristband Controller:** I created a controller with a 16x2 display, ESP32, and 5 potentiometers to offer an additional control option for the robot.

9. **Exohand:** I added a separate module that allows controlling the robot using gestures with a kind of glove.

These customizations were vital in making the project align with our specific needs and creative vision, and they have significantly improved the overall performance and functionality of the project.

## Could you describe a moment during this project when you felt particularly excited, proud, or motivated by your progress or a breakthrough?

![Gesture](/assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231022_112837_321.jpg){: .w-50 .right}

### Moments of Excitement and Pride
When I first started the project, I was particularly excited. The most significant moment of excitement was when I began the project. Of course, besides this excitement, there were other moments that got me excited. For instance, I was thrilled when the mayor of our city visited our village, and I explained the main concept of the project and the changes I made. Another unforgettable moment was when I traveled to Ankara with my cousin, who is a faculty member at Middle East Technical University (METU). While at METU, I was excited to talk to mechatronics and software engineering students about Dan, my own project, and Dan's main project. I felt proud of myself after these moments, knowing that I had contributed to the project.

### Moments of Motivation and Progress
There were certain factors that motivated me during the project. Firstly, the idea of creating, testing, and even improving one of the early versions of an open-source project was a great source of motivation for me. During this process, self-improvement was equally important.

The outcomes of the features I added motivated me. Among the features that set me apart from the main project and formed my own branch were RFID, ChatGPT, Bluetooth controller, MPU6050, and the module that makes audio output correspond to the direction of the audio input. For instance, if the sound is coming from the right microphone, the system will play the sound through the right speaker, and during this time, the robot will look to the right. I am working to add some of these features to the main project in collaboration with Dan. The development of this project and my contribution to it motivate me.

### Moments of Breakthrough and Challenges
Undertaking such a project at a young age and developing my own projects may not be something everyone in my country can or will do. Heavy school workloads, time constraints, and the responsibilities of living in an earthquake-prone area made it difficult for me to allocate time to my projects. As a student, I used to go to school at 11 AM and return home at 7 PM. I couldn't find time for my projects, so I had to shorten my sleep time. Over time, I got used to it, and sleeping 1 or 2 hours a day stopped being an issue for me. That's because my projects and science are very important to me. One of the characteristics that sets me apart from others is my dedication to my projects and to science, regardless of any challenges. Projects and science should progress and continue to evolve, no matter what. This is the most important source of motivation for me.


## Were there any unexpected challenges or setbacks you faced either with the project or on your robotics journey in general?

![Controller](/assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231022_112840_813.jpg){: .w-50 .right}

### Getting Started and Motivation
When starting the project, I realized that the source of the difficulties I faced in getting started was not related to the project or the framework, but rather to the act of starting itself. The beginning stage is always contentious, but I would always remind myself, "Just start somehow in any area, because starting is half the success." The most challenging aspect of this project for me was related to mechanics. Lately, the issue we've been working on the most with Dan and the community is getting the robot to walk in a balanced manner. Currently, the robot was falling while walking, and we are working intensively and seeking solutions to solve this problem.

### Lack of Knowledge and the Framework
Another problem was my lack of knowledge about robots. I needed to find a good and simple framework to learn about this subject. That's when Dan's framework came into my life. This framework made me feel like it's a perfect resource for both a beginner and an experienced developer.

### First Step into Robotic Technology
The first time I started learning about the structure of the robot, I took my first step into robotic technology using the Companion-Robot framework. This allowed me to enter the world of robotics.

### Hitches and Quests for Solutions

![Back](/assets/img/posts/2023-10-24-interview-emir-hamurcu/IMG_20231022_112844_743.jpg){: .w-50 .right}

Speaking of hitches with the robot, as I mentioned above, the biggest challenge for me was the walking aspect of the robot. I conducted a lot of research on this and came up with ideas. For example, I considered adding electromagnets under the feet, thinking that when a step is taken, the magnet would come into play and prevent the robot from falling. However, I abandoned this idea due to the issue of drawing high current. I also thought about adding strong springs to the foot model, but I gave up on that idea because it could put excessive strain on the PLA body structure. I am still working on this issue and hope to find a solution soon.

## Can you tell us about any communities or resources that have been helpful to you?

I don't have a lot of major support resources, but the most significant ones for me are research and the people in the Maker Forge community, and of course, Dan. Dan is supportive in many aspects, whether it's related to robots or not.

When my resources are limited, I have to do research to access knowledge. When I encounter a situation I don't understand from sources like YouTube and ChatGPT, I delve into research. However, I don't have blind faith in the information I receive. I always question the information and act based on the outcome.

## What plans do you have for the future?

My future plans are a bit complex because I'm interested in both Software Engineering and Mechatronic Engineering. However, in the end, I want to become a great software engineer, while Mechatronic Engineering remains a hobby, and I aim to become a "maker."

After graduating from high school, my goal is to attend one of Turkey's top universities, such as Bahçeşehir University, to receive an education in software engineering. One of the reasons I choose this university is because it offers a blue diploma, which can increase my chances of pursuing further education abroad.

One of the reasons I consider studying abroad is my dream of working or even relocating to countries like the UK, Switzerland, and Finland after becoming a software engineer. During the job search phase in these countries, I would love to have the opportunity to work for companies like Google and Riot Games.

Alongside all these goals, I also aim to contribute to the advancement of science by creating open projects on GitHub and helping future generations.

## What advice would you give to other members of the maker community who are thinking about starting their own robotics projects?

My advice to anyone considering starting their own robot projects is to focus on research and work. Research findings can convince you that everything that initially seems impossible is, in fact, achievable. This comes from my own experiences.

However, research alone is not enough; work is essential. I would like to conclude this piece with the saying, "When you rest, You rust." Consider this saying as if it were a cog in a machine. If a cog stops, it rusts, and that cog is you. Therefore, hard work is of utmost importance in every project.

