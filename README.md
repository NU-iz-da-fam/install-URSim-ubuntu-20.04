# Installation of UR Simulator on Ubuntu 20.04
- URSim is a simulation software of Universal Robot which is used for offline and online programming to control UR robot. 
- To install UR software, you could access this link for reference: https://www.universal-robots.com/download/?query=   
- URSim can be installed on virtual machines, but in my POV, it would be better to install directly on our OS like Ubuntu, ...
### Install UR on Ubuntu 20.04
UR has many versions for UR Sim, so based on your robot UR Sim version or your requirements, you could choose an appropriate one. Check this link for reference: https://www.universal-robots.com/download/software-e-series/simulator-linux/offline-simulator-e-series-ur-sim-for-linux-5130/    
#### Install the software 
- At the end of the download link, UR guides us how to install UR Sim, so in this repos, we just try to fix the errors which could happen through the process. Assume the downloaded packge is located in Downloads folder.
```
cd Downloads/

tar xvzf [FILE NAME]

cd ursim-5.X.X.XXXXX

./install.sh 
```
#### Error: Package 'libcurl3' has no installation candidate    
- In ubuntu 20.04 LTS and higher, <strong>lubcurl3</strong> has been replaced by <strong> libcurl4 </strong>, so firstly, you need to install <strong> libcurl4 </strong> by these command lines:
```
sudo apt update
sudo apt install libcurl4
```
- Maybe sometimes, you also need to install php packages. Try below commands:
```
sudo apt install php7.2-curl
```
- Then, open file <strong>install.sh</strong> inside ursim-5.X.X.XXXXX folder, search for <strong>lubcurl3</strong> and replace by <strong> libcurl4 </strong>
- Reference link: https://askubuntu.com/questions/1363847/e-package-libcurl3-has-no-installation-candidate-in-ubuntu21-04   
#### Error: Check Java JDK version failed   
- Sometimes, you will face the problems that your java jdk version is higher than requirements, but install.sh file always reject to install. First, check you <strong>Java JDK version</strong> again, and make sure it be higher than <strong>1.6</strong>:
```
javac -version
```
- If your javac does not satisfy the requirements, try to install the newer one. You could use this article as reference: https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-20-04 or:
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
- Now sure that you could install the URSim properly.
### Launch UR Simulator
```
cd Downloads/ursim-5.X.X.XXXXX
./start-ursim.sh
```
It's time for you to enjoy the URSim !!!
![alt text](images/ursim_ubuntu.png "ursim_ubuntu")    

### Install URCap SDK on URSim
- The URCaps SDK is the core tool for you to develop a URCap plugin for the graphical programming interface PolyScope. You could access here and download: https://www.universal-robots.com/download/software-e-series/support/urcaps-sdk/   
- If you are newbie with URCap, it's a good idea to take a glance at this tutorial: https://youtu.be/ncZJQKL4Fxo?si=zrQt5sAAuYwgPKu6 . This will guide you how to do to install and run a sample URCap with URCap SDK. Note that, he worked with URSim running on VM.
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
### Notes: 
- Each UR Sim software version will be compatible with specific URCap SDK and URCap API. Make sure you install correctly the version of URCap to extend UR robot capabilities.
- As with UR Soft ver5.13, it will work with ```URCap API: 1.13.0```, ```URCap SDK: 1.13.0```
- Check this link for more detail: https://www.universal-robots.com/articles/ur/release-notes/release-note-software-version-513xx/   
