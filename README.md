# Ultimate android emulator Image

It's a docker image, provides an easy way to have an android emulator working inside a docker container by running few simple commands or by using a simple docker-compose file, the image comes with the latest Android SDK, also the ability to run automation tests against multiple browsers using selenium.

The image is updated regularly every month.

# Feature

- Run android emulator in headless or in headed mode (through VNC)
- Support selenium driver, allowing remote execution against browsers
- Support chromedriver/geckodriver
- Support cloning private repository by passing few simple environment variables, allowing execution test inside the docker against browser or android
- Support Appium driver
- Support test execution for Nodejs or Maven projects
- Come with the latest JDK lts.
- Allure report, where you can visualize the report after the test execution 
<!-- Perform automatic updates every time docker-compose file is up -->

<!-- Emulator image is x86 CPU to enhance its speed and performance, also google play services have been added. -->

For any issues or questions please open a ticket on the GitHub repo : 
**[Docker image](https://hub.docker.com/repository/docker/amrka/ultimate-android)**


# Setup

## Manual execution

Down below is the list of the main scripts to launch the relevant service, certain environment variables should be passed during starting the container.

1.  **Pull the docker image :** 

        docker pull amrka/ultimate-android:latest
    
3.  **Start your container:**

        docker run -it -d -p 5900:5900 --name androidImage --privileged amrka/ultimate-android    

4.  **Launch the appium session :**

        docker exec --privileged -it androidImage bash -c "appium -p 5900"
 
5.  **Start the emulator in headless mode :**

        docker exec --privileged -it androidImage bash -c "./start_emu_headless.sh"

6.  **Cloning your repository (in case of execution inside the container) :**

        docker exec --privileged -it androidImage bash -c "./clone_repo.sh"
        docker exec --privileged -it androidImage bash -c "./execute_test.sh"

7.  **Starting selenium instance:**

        docker exec --privileged -it androidImage bash -c "./clone_repo.sh"
        docker exec --privileged -it androidImage bash -c "./start_selenium.sh"

8.  **Starting VNC server:**

        docker exec --privileged -it androidImage bash -c "./bootstrap.sh"



## Launch emulator in headed mode


1.  **You will have to start the docker container by using the following command:**

        docker run -it -d -p 5900:5900 --name androidImage -e VNC_PASSWORD=password --privileged amrka/ultimate-android

2.  **Instantiate the VNC service by running:**

        docker exec --privileged -it androidImage bash -c "./bootstrap.sh"

3.  **Connect to the VNC server via remmina or any VNC viewer, on:**
          
        localhost:5900
    
4.  **Open dash terminal in vnc viewer and right the following command:** 

        #: ./start_emu.sh
 
<a href="https://ibb.co/pPq0bn9"><img src="https://i.ibb.co/pPq0bn9/vnc.png" alt="vnc" border="0"></a>       <a href="https://ibb.co/cJB6qkX"><img src="https://i.ibb.co/cJB6qkX/gif.gif"       alt="gif" border="0"></a>
    
Note: 
  - start_emu.sh will launch the emulator in headed mode, so this shell script should not be used for integration with the pipeline (e.g. Jenkins), Use **start_emu_headless.sh** instead, you can run chrome/firefox in headed mode using vnc terminal as well
  - For appium caps default name of the emulator is 'nexus' android 11 
  - It does not make sense to launch all services in docker-compose, just enable the service you need and comment on the others in the docker-compose file

## Cloning a git Repository
In order to clone your repository inside the docker container automatically, you will need to pass the env variables as shown below during container initialization, cloning repository will allow you to run test inside the container against either browsers or emulators using appium, note that relevant services should be triggered according to your need:

-   **BRANCH**: Repository branch name
-   **GITHUB_USERNAME**: GitHub username/Orgnization name
-   **GITHUB_TOKEN**: Github personal token
-   **REPO_NAME**: the exact name of the GitHub repository
-   **SCRIPT** : using this command will allow you to execute certain test command such as "mvn clean test" or "npm run test" note you don't need to run npm I
-   **REPORT**: Name of the reporting service 'only allure for now'
-   **REPORT_DIR**: The expected root directory for allure-results folder, such as './report'

        docker run --privileged -it -d -p 5900:5900 --name androidImage -e BRANCH=win -e GITHUB_USERNAME=amrka -e GITHUB_TOKEN=test -e REPO_NAME=yourRepoName amrka/ultimate-android

**After running the command above you will be able now to run the following command to have your Repository clones in / directory:**

    docker exec --privileged -it androidImage bash -c "./clone_repo.sh"

*Note: You can combine all VNC env var with the env above.*

## Using Docker-compose

Using the docker-compose file will make initiating the service even easier, docker-compose YAML file provided has several services such as launching emulator with appium instance, starting selenium instance with specific web driver (firefox/chrome), cloning your test automation repo, and executing your test, launching VNC server, it's possible to disable/enable any service based on your need.
    
    docker compose up  >> For mac with latest docker cli
    docker-compose up  >> Other OS


## Environments

**Provide the required Environments during container initializing, in case of manual execution** 

| Environments      | Description                                                                                              | Required     |  Service   |
| ----------------- | -------------------------------------------------------------------------------------------------------- | ------------ | -----------|
| APPIUM_PORT       | Port for the appium instance                                                                             | Yes          | Android    |
| BRANCH            | Repository branch name, default is master                                                                | Optional     | Clone      |
| GITHUB_USERNAME   | Github username                                                                                          | Yes          | Clone      |
| GITHUB_TOKEN      | Personal GitHub token                                                                                    | Yes          | Clone      |
| REPO_NAME         | Repository exact name                                                                                    | Yes          | Clone      |
| TEST_SCRIPT       | Test command to be run e.g. 'mvn clean test'/ 'npm run test'                                             | Optional     | Clone      |
| REPORT            | Provide 'allure' in order to generate allure report                                                      | Optional     | Clone      |
| REPORT_DIR        | Provide expected directory for allure-results folder e.g './' or 'report/'...etc                         | Optional     | Clone      |
| BROWSER           | Use firefox or chrome, default is chrome                                                                 | Optional     | Selenium   |
| VNC_PASSWORD      | Password needed to connect to VNC Server                                                                 | Yes          | VNC        |

**[Check docker-compose file](https://github.com/Amrkamel1/Android-Emulator-Docker)**


## Kill the container

-   **Run the following command to kill and remove the container:** 

        docker rm -f containerName
