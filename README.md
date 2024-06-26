# Installation of UR Simulator on Ubuntu 20.04
- URSim is a simulation software of Universal Robot which is used for offline and online programming to control UR robot. 
- To install UR software, you could access this link for reference: [Download Search](https://www.universal-robots.com/download/?query=)  [1]
- URSim can be installed easi on virtual machines, but in my POV, it would be better to install directly on our OS like Ubuntu, ...

### I. Install UR on Ubuntu 20.04
UR has many versions for UR Sim, so based on your robot UR Sim version or your requirements, you could choose an appropriate one. Check this link for reference: 
#### Tested version:
- URSim 5.13           | Passed     | [URSim v5.13](https://www.universal-robots.com/download/software-e-series/simulator-linux/offline-simulator-e-series-ur-sim-for-linux-5130/)  [2]
- URSim 5.12.6 LTS     | Not tested | [URSim v5.12.6](https://www.universal-robots.com/download/software-e-series/simulator-linux/offline-simulator-e-series-ur-sim-for-linux-5126-lts/) 
#### Prerequisites:
- Before trying to install URSim on your Linux environment, you should first ensure that your Linux is configurated properly.
- You should take a look at this link before installation: [URCap Development Installation](https://www.universal-robots.com/articles/ur/urplus-resources/urcap-how-to-install-ursim-for-urcaps-development/) [3]
- In summary, you could copy some commands below:
```
sudo apt-get update
# Java OpenJDK8 SDK
sudo apt-get install openjdk-8-jdk
# Maven
sudo apt-get install maven
# Runit, check detail explained in [3]
sudo apt-get install runit
```

#### Install the software 
- At the end of the download link, UR guides us how to install UR Sim, so in this repos, we just try to fix the errors which could happen through the process. Assume the downloaded packge is located in Downloads folder.
```
cd Downloads/

tar xvzf [FILE NAME]

cd ursim-5.X.X.XXXXX

./install.sh 
```
#### [Error]: Package 'libcurl3' has no installation candidate    
- In ubuntu 20.04 LTS and higher, <strong>lubcurl3</strong> has been replaced by <strong> libcurl4 </strong>, so firstly, you need to install <strong> libcurl4 </strong> by these command lines:
```
sudo apt-get update
sudo apt-get install libcurl4
```
- Then, open file <strong>install.sh</strong> inside ursim-5.X.X.XXXXX folder, search for <strong>lubcurl3</strong> and replace by <strong> libcurl4 </strong>
- Reference link: [libcurl3 deprecated in ubuntu 20.04](https://askubuntu.com/questions/1363847/e-package-libcurl3-has-no-installation-candidate-in-ubuntu21-04)   
#### [Error]: Check Java JDK version failed   
- Sometimes, you will face the problems that your java jdk version is higher than requirements, but install.sh file always reject to install. First, check you <strong>Java JDK version</strong> again, and make sure it be higher than <strong>1.6</strong>:
```
javac -version
```
- If your javac does not satisfy the requirements, try to install the newer one. You could use this article as reference: [Install Java ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-20-04) or:
```
sudo apt install default-jdk
```
- Then check your version again, and try to install. If the problem is still not solved, maybe the issue comes from the content of install.sh file. You could replace the function <strong>needToInstallJava()</strong> by the below one:
```
needToInstallJava() {
    echo "Checking java version"
    if command -v java; then
        # source https://stackoverflow.com/questions/7334754/correct-way-to-check-java-version-from-bash-script
        version=$(java -version 2>&1 | awk -F '"' '/version/ {print $2}')
        echo "Java version $version"

        # Use awk for version comparison
        if awk -v ver="$version" 'BEGIN{if (ver >= "1.6") exit 0; else exit 1}'; then
            echo "Java version accepted"
            return 0
        fi
    fi
    echo "Java version not accepted"
    return 1
}
```
#### [Error]: Simulator hang at 100%
- In my case, the problem comes from Java version in use is 1.11, but the simulator expects 1.8
```
sudo update-alternatives --config java
```
- Choose JDK ver1.8 instead of ver1.11 by typing number: ```2```.

#### [Error]: Unable to read state
- Based on the thread [Universal Robot forum](https://forum.universal-robots.com/t/jsonmappingexception-while-getting-control-mode-from-uradmin-scipt/15769)
- Inside the ursim-5.X.X.XXXXX folder, you will find the folder /usr/bin. You can copy the content to /usr/bin of the host.
- E.g: Go directly into /usr/bin of ursim-5.X.X.XXXXX, type command ``` sudo cp * /usr/bin ```. Confirm the password and Done!
### II. Launch UR Simulator
- Now sure that you could install the URSim properly.
- You could choose type of robot to launch by adding subfix after ```./start-ursim.sh``` such as ```ur5```, ```ur5e```, ...
- Assumed that your URSim package is located on Desktop, start UR Simulator by:
```
cd Desktop/URSim_Linux-5.13.0.113898/ursim-5.13.0.113898/
./start-ursim.sh
```
It's time for you to enjoy the URSim !!!
![alt text](images/ursim_ubuntu.png "ursim_ubuntu")    

### III. Install URCap SDK on URSim
- The URCaps SDK is the core tool for you to develop a URCap plugin for the graphical programming interface PolyScope. You could access here and download: [UR Cap SDK](https://www.universal-robots.com/download/software-e-series/support/urcaps-sdk/)   [4]
- If you are newbie with URCap, it's a good idea to take a glance at this tutorial: [Beginners URCap Tutorial](https://youtu.be/ncZJQKL4Fxo?si=zrQt5sAAuYwgPKu6) . This will guide you how to do to install and run a sample URCap with URCap SDK. Note that, he worked with URSim running on VM.
- Prerequisites used in this README: Java SDK 6, Apache Maven 3.0.5, PolyScope version >= 5.11.0 and URCap API sdk-1.13.0. Make sure you have installed or downloaded all these packages before urcap installation
- Firstly, unzip the SDK and access to folder, install the file
```
unzip sdk-1.13.0.zip -d sdk-1.13.0
cd sdk-1.13.0/
./install.sh
```
- Build and deploy URCap into URSim. There are 2 ways to do that.
  +  I. Build, generated ```.urcap```. Copy to URSim programs place and deploy as in real Robot
    ```
    cd samples/swing/com.ur.urcap.examples.helloworldswing/
    mvn install
    ```
  +    Then a file with extension ```helloworldswing-1.0-SNAPSHOT.urcap``` will be generated in target folder.
  +    Copy ```helloworldswing-1.0-SNAPSHOT.urcap``` to programs.UR5 (inside URSim installation folder)
    ```   
    cp helloworldswing-1.0-SNAPSHOT.urcap ~/programs.UR5
    ```
  +    II. Deploy directly with Maven.
### IV. Notes: 
- Use non-sudo user to launch UR Simulator.
- Each UR Sim software version will be compatible with specific URCap SDK and URCap API. Make sure you install correctly the version of URCap to extend UR robot capabilities.
- As with UR Soft ver5.13, it will work with ```URCap API: 1.13.0```, ```URCap SDK: 1.13.0```
- Check [Release Notes](https://www.universal-robots.com/articles/ur/release-notes/release-note-software-version-513xx/) for more detail:    [5]
