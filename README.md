# Ultimate android emulator Image

It's a docker image, provides an easy way to have an android emulator working inside a docker container by running few simple commands, the image as well comes with the latest Android SDK and the latest Appium version to ease the CI/CD, Aslo selenium service is added so you can run you automation script against chrome browser.

Emulator image is x86 CPU to enhance its speed and performance, also google play services have been added.

**[Docker Image](https://hub.docker.com/repository/docker/amrka/ultimate-android)**


# Setup


1.  pull the docker image: 
**_docker pull amrka/ultimate-android:latest_**
    
3.  Start your container :
 **_docker run -it -d -p 5900:5900 --name androidImage --privileged amrka/ultimate-android_**    

4.  Launch the appium session:
 **_docker exec --privileged -it androidImage bash -c "appium -p 5900"_**
 
5.  Start the emulator in headless mode :
 **_docker exec --privileged -it androidImage bash -c "./root/start_emu_headless.sh"_**


## Launch emulator in headed mode

1.  You will have to start the docker container by using the following command :
**_docker run -it -d -p 5900:5900 --name androidImage -e VNC_SERVER_PASSWORD=password --privileged amrka/ultimate-android_**

2.  Instantiate the vnc service by running :
**_docker exec --privileged -it androidImage bash -c "./root/bootstrap.sh"_**

3.  Connect to the VNC server via remmina or any VNC viewer
    
4.  Open dash terminal and right the following command: 
#: **./root/start_emu.sh**
 
<a href="https://ibb.co/pPq0bn9"><img src="https://i.ibb.co/pPq0bn9/vnc.png" alt="vnc" border="0"></a>       <a href="https://ibb.co/cJB6qkX"><img src="https://i.ibb.co/cJB6qkX/gif.gif"       alt="gif" border="0"></a>
    
*Note: start_emu.sh will launch the emulator in headed mode, so this shell script should not be used for integration with the pipeline (e.g. Jenkins), Use **start_emu_headless.sh** instead so emulator will lauch in headless mode*

## Cloning a git Repository
Basically, we are running the same command mentioned above except that we are passing different Env variables, so to clone a repo you will need to pass the following env while starting the container:

-   **branch**: Repository branch name
-   **username**: GitHub username (not your email)
-   **gitPass**: Github password
-   **gitUrl**: Repository url (without https:// e.g. **github.com/amrka/androidImage.git**

**docker run --privileged -it -d -p 5900:5900 --name androidImage -e branch=win -e username=amrka -e gitPass=test -e gitUrl=github.com/amrka/Docker-Images.git amrka/ultimate-android**

After running the command above you will be able now to run the following command to have your Repository clones in / directory:
**docker exec --privileged -it androidImage bash -c "./root/clone_repo.sh"**

*Note: You can combine all VNC env var with the env above.*

## Using Docker-compose

Using the docker-compose file will make initiating the service even easier, docker-compose yml file provided has two main services android emulator with appium and selenium service, you can tweak them or adjust the file based on your need.

## Kill the container

-   Run the following command to kill and remove the container : 
**docker rm -f androidImage**
