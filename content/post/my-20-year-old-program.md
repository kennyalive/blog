---
title: "My 20-Year-Old Program"
date: 2024-02-02T14:00:49+01:00
---

*Dedicated to my friend Volodymyr, who has a birthday today. I almost forgot about it but remembered when I wrote the publish date for this post.*

### Archive

![map2bsp program](/20-year-program/map2bsp.png)
*map2bsp program generates BSP trees*

I checked the archive of my old hobby projects. These are not very complex programs but also not trivial ones. Most of the projects I can build with modern compiler without issues. Then, one program passed compilation but failed the linking stage because the linker did not like old library binary. Another project caused a build failure because of a missing MFC component in my Visual Studio installation.

Most of these programs use only C++ and Win32 API. The latter has good backward compatibility. The prebuild executable made two decades ago runs just fine. The program that failed to link with the old static library includes the source code of that library. I'm happy I added it to the project tree. Rebuilding that library fixed the issue. Then I added MFC component to my VS installation and this allowed me to build the second program. I’d say that the programs are in good shape, assuming that I did not touch some of them for around 2 decades and that a few issues I had were easy local fixes. Although I took some steps to ensure that programs were more or less self-sufficient, I  did not think much about this aspect when I wrote them.

The rest of this write-up is about code written not by me in my programs.

![my hammer editor](/20-year-program/hammer-demo.jpg)
*Hammer program is an editor to build doom-style maps (not related to Valve's Hammer editor)*

![first fantasy project](/20-year-program/ff-demo.jpg)
*First Fantasy project is a package of 3 programs: map2bsp, Hammer editor and OpenGL renderer. All three can be built with modern Visual Studio without compilation and linker errors. But with warnings because fopen is not modern.*

### Connections

I like it when I can easily build my programs. Even if I don't touch them for many years. And when I'm on the plane without the internet. I like the idea that a project distribution contains everything necessary to build the project. Maybe even with the compiler if it's a compact and nice distribution.

So, I was thinking the last few years or so, not very actively or often, but just something that was on my mind. I still have hobby projects with complexity with tens of thousands of lines of code, and they use 3rd party dependencies. Should I do something special with a code written not by me?

There are package managers, online repositories of source code, repositories of prebuilt popular libraries, etc. One option is to depend on them, and that's what a lot of businesses do. But I'm not sure if it's a good idea for a small personal project that you want to survive for a long time.

In the case of functioning businesses, there are teams of people who can fix things. Often, there is even a dedicated position of a build engineer. If I check commits of open-source projects, it's not uncommon to see that quite a few commits are related to managing dependencies. I guess this makes sense from an economic perspective. If the program makes money, you have the resources to maintain it. Otherwise, who cares about software that does not make money directly or indirectly?

I think package managers and online code repositories work well as standard places where I go to get some software. I remember times when finding the software you needed was more challenging: a library could be hosted in different locations, and it was unclear if the distribution was an official version, a customized package, and whether it was the latest version.

![sphere tracer](/20-year-program/rt-demo.jpg)
*Sphere tracer program. I needed to get an asnwer if simple CPU ray tracing can be realtime. That's more recent experiment from around 2010. I had to undef `INFINITY` constant that conflicted with some external definition. 2 minutes fix.*

What I'm less happy about is when these systems are not only used to deliver the code to your computer but, in a sense, increasingly become part of your program. This directly affects your software. Businesses have dedicated resources to participate in this networking. But for my personal projects, I want to focus on implementing my todo list. And to minimize occasions when I must react to external events.

I wish the existing systems were less assertive about what you should do with a source code you get from the internet. A popular model nowadays is that your program does not contain the source code of external libraries and downloads them as part of the build process. Other models go out of flavor and, in general, are frowned upon.

The plan for my recent trip was to do some work during the flight. Earlier, I synced the project and initiated the build, which downloaded and built the dependencies so subsequent builds could work offline. It worked, and I made good progress. But not on the way back. At some point, I missed the key (economy class, you know) and maybe initiated a build clean-up or something similar. The full rebuild tried to fetch dependencies from the internet, and that's one example of how this does not work.

For the long-term survivability of the program, dependency on the external source is a weak link, even with a stable network connection. github.com is a popular place. Arguably, it is the biggest code site in the world, and it is a shame that it has this name. Not everyone likes git (I do); why not come up with a more inclusive name? If Microsoft buys *code.com* domain for one billion dollars, I won’t be able to build my archived hobby projects that assume that github.com is a universal constant. Instead of enjoying playing with code, I will spend the evening fixing build scripts. Who wants such a life?

![q3fantasy program](/20-year-program/q3fantasy-demo.jpg)
*Q3Fantasy program is a Quake3 map renderer written before Quake 3 source code was released in 2005. Does not support curved surfaces, and sky, and mirrors, and doors, and probably other features too. Linker error. I had to rebuild jpeg library which was bundled with the project. 20 minutes fix.*

### Doings

* Often, it's not a big deal to implement the functionality you need, and it should be part of your program.
* For a large library, there might exist a small and well-crafted alternative that works for you. 
* Put the dependent library source code into the project tree and build it with the rest of the program.
* Consider whether it can be an optional feature if a huge dependency is needed. For example, the project is always bundled with a compact GLTF library, but USD scene support is an external dependency, and the project can still be built without it if needed. You can then show pretty GLTF rendering for your grandchildren, but you definitely will be too old for USD.
* If the large library is an integral part of the project, consider bundling it, too.
* If you are forced to use the library as an external dependency, create software release distributions containing everything. That's one direction I hoped GitHub could be helpful because it provides a feature to create software releases. Unfortunately, it does not support including git submodules or other forms of dependencies in release distribution. Not helpful. AGAIN.
* No need to update your dependencies on a daily basis. Focus on your todo list.
