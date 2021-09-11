# Ultimate android emulator Image

It's a docker image, provides an easy way to have an android emulator working inside a docker container by running few simple commands, the image as comes with the latest Android SDK, also ability to run automation test against multiple browsers using selenium.

# Feature

- Run android emulator in headless or in headded mode (through VNC)
- Support selenium driver, allowing remote execution against browsers
- Support chromedriver/geckodriver
- Support clonning repository by providing set of environments varaiable, allowing execution test inside the docker against browser or android
- Support Appium driver
- Nodejs Maven, and JDK lts

<!-- Emulator image is x86 CPU to enhance its speed and performance, also google play services have been added. -->

**[Docker Image](https://hub.docker.com/repository/docker/amrka/ultimate-android)**


# Setup

## Manual execution

Down below is the list of the main scripts to launch the relevant service, certain environment varaiables should be passed during starting the conatiner.

1.  pull the docker image: 

        docker pull amrka/ultimate-android:latest
    
3.  Start your container :

        docker run -it -d -p 5900:5900 --name androidImage --privileged amrka/ultimate-android    

4.  Launch the appium session:

        docker exec --privileged -it androidImage bash -c "appium -p 5900"
 
5.  Start the emulator in headless mode :

        docker exec --privileged -it androidImage bash -c "./root/start_emu_headless.sh"

6.  Clonning you repository (in case of execution inside container) :

        docker exec --privileged -it androidImage bash -c "./root/clone_repo.sh"
        docker exec --privileged -it androidImage bash -c "./root/execute_test.sh"

7.  Starting selenium instance:

        docker exec --privileged -it androidImage bash -c "./root/clone_repo.sh"
        docker exec --privileged -it androidImage bash -c "./root/start_selenium.sh"

8.  Starting VNC server:

        docker exec --privileged -it androidImage bash -c "./root/bootstrap.sh"



## Launch emulator in headed mode


1.  You will have to start the docker container by using the following command :

        docker run -it -d -p 5900:5900 --name androidImage -e VNC_PASSWORD=password --privileged amrka/ultimate-android

2.  Instantiate the vnc service by running :

        docker exec --privileged -it androidImage bash -c "./root/bootstrap.sh"

3.  Connect to the VNC server via remmina or any VNC viewer, on :
          
        localhost:5900
    
4.  Open dash terminal in vnc viewer and right the following command: 

        #: ./root/start_emu.sh
 
<a href="https://ibb.co/pPq0bn9"><img src="https://i.ibb.co/pPq0bn9/vnc.png" alt="vnc" border="0"></a>       <a href="https://ibb.co/cJB6qkX"><img src="https://i.ibb.co/cJB6qkX/gif.gif"       alt="gif" border="0"></a>
    
*Note: start_emu.sh will launch the emulator in headed mode, so this shell script should not be used for integration with the pipeline (e.g. Jenkins), Use **start_emu_headless.sh** instead, you can run chrome/firefox in headed mode using vnc terminal as well*

## Cloning a git Repository
In order to clone your repository inside the docker container automatically, you will need to pass the env varailables as shown below during container initialization, cloning repository will allow you to run test inside the container against either browsers or emulator using appium, note that relevant services should be triggered according to your need:

-   **BRANCH**: Repository branch name
-   **GITHUB_USERNAME**: GitHub username (not your email)
-   **GITHUB_TOKEN**: Github password
-   **REPO_NAME**: exact name of the github repo
-   **SCRIPT** : using this command will allow you to execute certain test command such as "mvn clean test" or "npm i && npm run test"

        docker run --privileged -it -d -p 5900:5900 --name androidImage -e BRANCH=win -e GITHUB_USERNAME=amrka -e GITHUB_TOKEN=test -e REPO_NAME=youRepoName amrka/ultimate-android

After running the command above you will be able now to run the following command to have your Repository clones in / directory:

    docker exec --privileged -it androidImage bash -c "./root/clone_repo.sh"

*Note: You can combine all VNC env var with the env above.*

## Using Docker-compose

Using the docker-compose file will make initiating the service even easier, docker-compose yml file provided has serveral services such as launching emulator with appium instance, starting selenium instance with specific web driver (firefox/chrome), cloning your test automation repo and executing you test,launching VNC server,its possible to disable/enable any service based on your need.
    
    docker compose up**  >> For mac with latest docker cli
    docker-compose up**  >> Other OS


## List Environments

Provide the required Environments during container initializing, in case of manual execution 

| Environments      | Description                                                                                              | Required | Service    |
| ----------------- | -------------------------------------------------------------------------------------------------------- | -------- | ---------- |
| APPIUM_PORT       | Port for the appium instance                                                                             | Yes      | Android    |
| BRANCH            | Repository branch name, default is mastter                                                               | optional | Clone      |
| GITHUB_USERNAME   | Github username                                                                                          | Yes      | Clone      |
| GITHUB_TOKEN      | Personal github token                                                                                    | Yes      | Clone      |
| REPO_NAME         | Repository exact name                                                                                    | Yes      | Clone      |
| TEST_SCRIPT       | Test command to be run e.g. 'mvn clean test'/ 'npm i && npm run test'                                    | optional | Clone      |
| BROWSER           | Use firefox or chrome, default is chrome                                                                 | No       | Selenium   |
| VNC_PASSWORD      | Password needed to connect to VNC Server                                                                 | Yes      | VNC        |



## Kill the container

-   Run the following command to kill and remove the container : 

        docker rm -f androidImage
