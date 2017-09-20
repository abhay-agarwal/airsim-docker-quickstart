AirSim Docker Quickstart
===

About
---
Docker is a container engine that creates an isolated environment for running applications without cluttering your system with specific dependencies. We have created a docker container that can be used to run AirSim on any linux machine (with some caveats). This container contains a pre-built version of the AirSim "blocks" environment that will run in an X Window on your desktop!


Requirements:
---
* Linux Machine (Mac support coming soon)
* Docker (tested with CE)
* [Nvidia-Docker](https://github.com/NVIDIA/nvidia-docker)
* Optimism


Running It
---
Execute the `run.sh` command and see what happens!



How It Works
---
Nvidia-docker is a tool developed by Nvidia to allow use of GPUs within containers (needed to render the Unreal Engine frames) without using machine-specific drivers. The pre-built container uses [VirtualGL](https://www.virtualgl.org/), which is the 'secret sauce' that makes it all work. Basically, Virtual GL allows you to render the GL commands inside the container to a pixel buffer, then stream the pixels as xwindow commands to your X Window manager outside the container. Best of all, you don't need to use privileged mode to get things to work! However, this still requires some hoop-jumping (like mounting the X11 file and a random library file into the container). 



Building your own Environment
---
The original docker container that was used to build the game binaries are in the `build` folder. To build the container, you will need to sign up for a free Epic Games account and download a zip of the source code of unreal engine from Github (download a zip of the 4.17 branch). Add in your own environment code to the provided directory, add it within the dockerfile and replace the project reference in the `GenerateProjectFiles.sh` command with your own `.uproject` file. Beware, this docker takes an _extremely_ long time to build. On my machine, it is around three hours and the final container is around 50GB! Be sure that you have space in your `/var/lib/docker` directory for this kind of data! If needed, move your `/var/lib/docker` directory to another larger external drive and turn `/var/lib/docker` into a soft link to that new directory. Be sure to stop the docker daemon first before moving anything, though.

After you finally build this mega container, copy the `/home/unreal/out/LinuxNoEditor` folder out into the build directory of this project so you can include it in the docker container that actually runs the code. For performance/size/licensing reasons, we use the second container for the binaries and the first to build it (you can't distribute the Unreal Engine source code) 

Running it headless
---
This container can be run headless by using a [fake display buffer](https://en.wikipedia.org/wiki/Xvfb) or by using a VNC display buffer. Then, you can stream frames like any VNC does, or just return some output without keeping the frames. Keep in mind that most cloud container engines (GKE, Amazon Cloud, etc.) will not support this, because it requires use of shared resources that most containers are not given access to.

We have not found a way to run Unreal completely headless (where no frame is rendered at all), but you can pass options to the docker command that will make the render super small. E.g.

`DISPLAY=:$PORT vglrun /home/unreal/Blocks/Binaries/Linux/Blocks-Linux-Shipping -ResX 1 -ResY 1`


