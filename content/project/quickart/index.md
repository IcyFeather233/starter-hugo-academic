---
title: QuickArt
subtitle: An animation production system based on generative adversarial
  networks and depth-aware networks
date: 2022-06-09T12:17:43.067Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
## The main content of the research results

### (1) Line drawing refinement

In the process of anime creation, the original sketches need to be refined before they can be colored. The completion of fine line drawing is usually divided into two processes: 1. firstly, the general composition is completed, the character image, and the scene features are roughly outlined by the artist. 2. line sketching, i.e. line drawing, is done on the basis of the sketch, and the details of the character performance, costumes, and scene props in the picture are carefully engraved.

The model for our line drawing refinement is LineRelifer, which has a relatively small network that can run at an acceptable speed on the CPU. When there is enough memory, this network is capable of generating stylized line images with HD dimensions. In addition, it can fill strokes, denoise and correct the shape or gaps of lines.

Since the keyframe line art we used to fill and color the frames was itself clean, the effect of lineNormalizer was more obvious before and after refinement, and the effect of LineRelifer was not obvious, we chose other anime line art for testing. The test results are as follows.

It can be seen that after LineRelifer's beautification, the disconnected lines are well connected, and there is a natural transition of shades, and the original stiff lines become soft and dynamic.


### (2) Line coloring and additional light and shadow

In the process of drawing anime, in order to facilitate the painting and modification, the artist will usually paint in layers, so that when one layer needs to be modified, the other layers will not be affected. Since the color style of the same character or scene in an anime often remains the same, and the images are often similar in the adjacent seconds, the automatic coloring function will replace the artist to complete the highly repetitive coloring function.

We use CGAN to achieve line coloring. For example, if the user inputs a multi-layer colorless line drawing, the output will be a material with bright colors. The task consists of two parts: 1. Generating a network to colorize different layers of the line drawing. The coloring of each layer is broken down into two steps, each performed by two GANs. The first step is called the "sketch phase", where the coloring does not exactly follow the lines of the sketch, but rather splashes the colors onto the canvas in a relatively spontaneous manner. The purpose of this stage is to add richness to the color scheme. The resulting sketch may contain more coloring errors and blurred textures, but is filled with a rich, vibrant color scheme. The second stage, called "Finishing", focuses on fixing the details and clarifying the blurred textures to get the final painting. 2.

The model for adding light and shadow we used the reference model given by the team that published the ACM TRANSACTIONS ON GRAPHICS paper Generating Digital Painting Lighting Effects via RGB-space Geometry. The model provides an algorithm for adding lighting effects to digital board paintings, based on RGB color geometry.
Here is a demonstration of the model's effect



### (3) DAIN implementation of complementary frames
In the actual anime production, several main artists and a large number of remaining professional artists are needed. Among them, the main artist only draws the key frames of the animation, and a large number of ordinary frames in the middle need to be drawn by other artists manually, which is time-consuming and laborious. At the same time, there is a phenomenon that one picture is copied and used as two frames in the process of animation production. In order to solve this problem, our team adopts the automatic frame filling method based on depth-aware network, and draws the specified frames between key frames independently.

When drawing anime frame material, artists and artists can draw only one segment of key frames in the material, and this feature can help them automatically generate smooth multiple frames between the specified two frames, significantly reducing the workload and making the animation smoother at the same time.
We use Depth Awareness Network (DAIN) to implement an animation frame filler, which explicitly detects occlusions by using depth information from video frame interpolation, based on the simple observation that closer objects should be composited in the intermediate frames.    

Experimentally, the results are very good and stable, and it is optimized to reduce the video memory footprint, so it can be used for frame rate enhancement of 4k videos. Another advantage of this method is that it can insert any number of frames between two frames according to the time interval.

We modified and tuned the reference model in conjunction with the ncnn project, a high-performance neural network forward computing framework, to add custom training parameters and model access.
The dataset was trained by finding some anime images on the Internet after frame extraction using FFmpeg and using python script to artificially reduce the frame rate, with a total of 97482 images and a total size of 27.8G.

The following is the demonstration of the model



### (4) Implementation of interactive QuickArt

This project identified Conditional GAN (CGAN) as the technical implementation means of line refinement, CGAN and hierarchical generative adversarial network method as the idea of layering line coloring, and DAIN as the technical implementation means of fine frame filling. We integrate these three technical means into our backend to facilitate users to use our QuickArt interactively, providing a humanized one-stop animation development service of line refinement -> line coloring plus light and shadow -> frame filling. The specific structure is as follows

In this project, the ease of operation of the program should also be given enough attention. According to the development experience, a beautiful and easy-to-use interface can effectively enhance the user's acceptance of the software. In order to reduce the user's learning cost, we need to design the interface in a simple and easy-to-use way to facilitate the user's operation. Based on this, we designed the following interface.

The top left corner of our front-end interface is the logo, and the top is the project name and introduction. At the bottom, there are three functional modules: Line Finishing, Line Coloring, and Frame Fill. When using, first click to upload a new file, which supports batch file upload (uploading a zip can be done). After uploading, click on the specific operation at the left end, and you can click on the download result to download the corresponding result zip after the picture is trained.

## Innovative Features

Our project has the following features.

* Practical: in line with drawing development habits, easy to modify and solve industry pain points. 
* Efficient: quick generation of works, reducing repetitive work. 
* Easy to use: friendly interface and simple operation, simple to use and easy to start. 
* Cost-effective: you can get the labor equivalent to the level of skilled painters at a low price.
* Line refinement: just sketch, you can automatically refine the line
* Line coloring: sketching without manual coloring, line drawing can be automatically colored, support multi-style output, support manual color block selection, support adding light and shadow effects
* Complementary frames: only a small number of key frames need to be drawn, and intermediate frames can be generated automatically.


## Practical significance

Chengdu animation industry has a bright future and animation talents are scarce, however, there are many repetitive and tedious parts in the production process that can be replaced by AI. In recent years, there are many researches about animation images in AI, but they are not suitable for the real development process and needs of animation industry, and the entertainment value is more than the practical value, which is of little help to animation industry.

This project develops an animation production system with AI technology as the core. The system is designed to layer the coloring of the artist's layered lines, automatically refine the artist's scribbled lines, and automatically fill the frames in the middle of the key frames drawn by the main artist, freeing up the pressure of a large number of animation practitioners.
Animation artists only need to draw sketches to automatically refine the lines and automatically color them, and only need to draw part of the key frames to automatically fill the frames, which greatly reduces the workload of the artists, liberates the animation industry pressure, improves the animation production speed, and contributes to the rapid development of China's animation industry.

For the actual animation production process, our animation material generation software can improve the production efficiency and realize multi-style conversion, which not only shortens the artwork time but also makes the animation material more abundant. Meanwhile, our team's research on GAN and its variants, Deep Awareness Network (DAIN) and its application to multiple fields can help the public better understand and appreciate its principles. Its rich connotations and uses can stimulate people's interest and attract more related researchers to extend it to other fields.

## Social Impact

### Shortening the time of artwork

One of the functions of our project is to seek to meet the needs of designers by automatically refining the first draft of the artist's sketch to meet the standard of the line drawing. Since our project is to input sketches and completed line sketches and finally output the refined sketches, the effect is no different from that of the artist's hands-on work, while the operation is simple and the interface is friendly, so it can largely reduce the work of art designers, greatly reduce the design cycle of anime scenes and character materials, thus shortening the artwork time, shortening the anime production cycle and achieving the purpose of improving production efficiency.

At the same time, the animation production company can use the technology to replace most of the artworkers. Since the cost of artwork is extremely high, optimizing artworkers can greatly reduce the cost of animation production.

### Helping the development of China's animation industry

According to the data of "China Animation Industry Research Report 2021", the industry trend of Chinese animation is steady. Nowadays, more and more young people with active minds are emerging in the animation market, and they are very talented and play a good role in promoting both more natural movements and smooth plot propulsion and scene concepts in the design. This project can help them to realize these ideas better, so that not only the production period can be greatly shortened, but also the quality and quantity of work can be achieved.

China's animation development is short and still in the rapid development period, so there are still some unresolved problems, among which the artwork cost is too high, the art workers have a lot of repetitive work and workload, the animation output is low and the output style is single is especially serious. Of course, this is not only a problem of Chengdu animation development, but also a major problem to be solved in China's current animation industry. Due to the rapid development of the animation industry and the increasing improvement of people's aesthetics, many of these lagging factors that can be solved by technology due to the tedious and complicated ones are becoming more and more obvious.
Artificial intelligence technology can be a good solution to the current problems of animation. Among them, Generative Adversarial Network (GAN) and Deep Awareness Network (DAIN) are particularly effective in optimizing animation production, and the application of these technologies can help the strong development of animation.

### Development and promotion of the algorithms

We try to help people better understand the GAN variant and DAIN application by implementing image related processing in anime material, and to further understand its principles, as well as to stimulate interest and attract more researchers, which is also important for the future development of the algorithms themselves.
It is also important to extend the algorithm to other fields, firstly, to let the public understand the different architectures and variants of GAN and DAIN, and then to explore the richness of its contents and applications. For example, the application of Deep Awareness Network (DAIN) on anime frame filling can be extended to contemporary life in video, movies. We have started to use 120 fps display in cell phones, but most of the online videos are still 30 fps, so we use DAIN to improve the frame rate of online videos and give a good viewing experience. to pass on the classics. For example, the sketch refinement and line sketch content in CGAN can also be extended to the field of facial reconstruction in medicine; meanwhile, CGAN can also be applied to video prediction dynamics, which has a wide range of applications in the fields of robot decision-making, unmanned driving and video surveillance systems.

Different variants of GAN and its role, Depth Awareness Network (DAIN) need to be discovered by more imaginative and creative practitioners, what we do is to make these algorithms more known and promote their development and evolution, which is one of the original intentions of our team.