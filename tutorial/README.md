## Important Notice before Reading!!!

Please ensure you are reading the proper documentation (User Guide vs. Developer Guide).

### User Guide
** [How to use? / Usage Guide](https://lev.zone/tutorial/#/)】**

1、 **Target Audience**：**Everyone (including developers)**

2、 **Document use**： This tutorial sets up environment configuration. After the configuration of the environment according to this document, users can use all the tools on LEVIATHAN online without needing to deploy or install a single tool, greatly improving the work efficiency of security personnel.

3、 **Instructions for use**：
- For users of LEV, all tools can be used only after completing the configuration of the environment according to the " Usage Guide ";
- For developers, the configuration of the "Usage Guide" is a prerequisite for developing tools. After confiuguration, you can develop following the "Development Documentation". 

### Developer Guide
**【[How to develop? / Development Documentation](https://lev.zone/docs/#/)】**

1、 **Target Audience**：**Developers using LEVIATHAN**

2、 **Document use**：This tutuorial shows the best practices of the development process on LEV. It covers: ① how to develop single-mode/multi-mode tools ② how to develop and call the automatic workflows of the tools to improve development efficiency.

3、 **Instructions for use**：For developers on LEV, after setting up their enviornment via the "Usage Guide", the "Development Documentation" explains how they can begin developing tools using LEV.



# Leviathan Usage Guide

Welcome to Leviathan, where you can easily perform security testing using tools and workflows published by the community. We encouraged you to also become a developer and contribute tools and workflows to our community.

This document will demonstrate Leviathan setup and how to use tools and workflows. If you are interested in uploading tools or workflows, please move to the [development document](https://lev.zone/docs/).

In contrast to traditional online security tool integration platforms, Leviathan ensures task initiation and deployment are done on two seperate ends. **① The invocation and running of tools and ② the production and storage of data, all rely on the user's private local environment. This information does not go to the Leviathan cloud server, thus ensuring the privacy and data security of our users**. 

In addition, users can enable tools and detection tasks with one click without the download/deployment/update of the tools or any code!

Next, let's see how to use Leviathan!



## Overview

In order to run the tools and workflows on Leviathan, you need to complete the configuration and setup of the local environment. 

The detailed setup steps depend on your operating system. This manual is divided into the installation and configuration of three operating system environments: Windows, Linux and Mac. Nevertheless, they all follow the general three steps:

- Preliminary environment installation (Install Docker)
- Credential uploading and acquisition
- Environment initiation

Before you begin, make sure to create an account with Leviathan at: https://www.lev.zone/portal/user/sign_up 

## 1. Local environment configuration

### Windows

#### 	1.1  Preliminary Environment Installation (Install Docker)

Background: [what is docker](https://www.redhat.com/en/topics/containers/what-is-docker)

 If Docker is installed on your device, please check its version number. Leviathan supports 20.10.10 and above. If this condition is met, you can skip this step. Otherwise, please install or update Docker as follows.
 
 Docker installation on Windows is complicated. First, you need to enable the built-in 'Hyper-V' function on Windows (therefore, it is recommended to use Linux system or WSL instead of Windows). The following tutorial takes Windows 10 as an example, and explains differences when using different Win10 Editions (If you don't know your system's Edition, feel free to google it).

##### 1.1.1 Enable CPU virtualization support

 Enter the BIOS settings from startup and check whether BIOS has enabled CPU virtualization support as below.

 ![Enable virtualization support.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2e12d9cd-cf65-4c6c-88a2-1b6d42110f22.JPEG)

##### 1.1.2 Win10 Home Edition Pre-step

It is strongly recommended to upgrade to Win10 Professional version or use Linux or Mac. If you use Win10 Professional Edition, Enterprise Edition or Education Edition, please skip this step and proceed to 1.1.3.

First, you need to enable the Hyper-V module. The Windows Home Edition cannot query the 'hyper-v' function module. It needs to be enabled in a special way, as follows:

Update the operating system to the latest version, Windows Settings -> Update -> Check for updates

Enable the display of file extensions. For details, [refer to File Extension Display](https://fileinfo.com/help/windows_10_show_file_extensions)

Copy the following code into a new document:

```
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

 Save the document as install-hyper-v.cmd, right click and run as administrator.

![install hyper-v.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/575279f5-e410-4348-963b-349bdeb858a6.JPEG)

 After the installation is completed, enter Yes and restart. (Note: **If the system is updated after the installation has been completed, it may invalidate the installation. You need to check, and if necessary, reinstall Hyper-V**)

 Repeat the above steps, save the following code as change.cmd and run as administrator.

 `REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F`

 Now, the Win10 Home Edition Hyper-V should be enabled.

##### 1.1.3 Win10 Professional Edition, Enterprise Edition, Education Edition

 If you use Win10 Professional Edition, Education Edition, or Enterprise Edition, it won't be as complicated as the Home Edition. Go to Control Panel -> Programs -> Enable or Disable Windows functions -> Check hyper-v, and then follow the prompts to restart the system.

##### 1.1.4 Install Docker
 The following steps should and must be performed on all Windows systems.

 Enable the display of file extensions. For details, refer to [File Extension Display](https://fileinfo.com/help/windows_10_show_file_extensions)

[Download the docker installation file](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe).

 Click the file to install docker (the Home Edition requires that Windows Container WSL2 should be unchecked during the installation; This step is not needed in other Windows Editions).

 Wait for the installation to restart successfully. After it, open Docker by clicking the Desktop icon. Docker has a graphical interface under **Windows**.

 When the Docker Logo on the lower left corner is green, it means you have successfully launched Docker![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

 If it displays that docker fails to launch and reports the error **"Hyper-V component is not running"**, it means that hardware virtualization has not been started. Please perform the first step: **1.1.1 Enable CPU virtualization support**.
 
#### 1.2 Credential uploading and acquisition

​	1.	Open PowerShell and run as administrator:

​		![open powershell.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/ae0c173f-92ee-455f-88bf-87ece5ca95bb.JPEG)

​	Enter ssh, line feed, and check whether the system has the ssh command installed:		

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3e4dd10e-3021-4647-a9e4-09877140b6f6.png)

​	If the content shown above does not appear, please refer to the following document to install the ssh command: Install ssh.
​	[Install ssh](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse)

​	Then enter the command:	``` cd ~```

​	2.	Enter the current user's home directory with the command above, and then enter ``` ls .ssh``` to check whether an ssh key exists, namely the two files  "id_ed25519" 、"id_ed25519.pub" :

![Check ssh key.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/fc1959af-8a2a-45ee-af59-22cf8b83953a.JPEG)

As shown in the picture above, the prompt in red (path cannot be found) means that the file does not exist and needs to be created (**if it exists, skip the generation step entirely** ). Enter the following command and execute it in a new line:

​		```ssh-keygen.exe -t ed25519```

You will be prompted to input, please ignore it, just press the Enter key all the way to the end, until the following prompt appears (Notes: It is not recommended to use the default RSA encryption method to generate keys, and RSA keys are disabled by default in higher versions of openssh):

```vbscript
+---[RSA 2048]----+
|        .o       |
|        o        |
|      oo .       |
|.     ++o  .     |
|o.  ...*S.o      |
|.o+.o.o+=. .     |
|.o.+. o=.o+o     |
|. .. *oo.oB.o    |
| ...oE*.o=o+     |
+----[SHA256]-----+
```

​	After that, enter  `ls .ssh` again. The two files mentioned above will be displayed (please disregard the names in the picture below):

![ssh key.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/d0e95ec6-b907-4f19-80fc-4d43256b2b72.JPEG)



3. Enter the command `cat ~/.ssh/id_ed25519.pub` to view the contents of the file, and copy it. (If you cannot copy directly by right-clicking in PowerShell, please google alternative copy methods.) 

   If you really can't copy, you can also open the id_ed25519.pub file through the file browser (the file path is `C Disk\User\(Current User’s Name)\.ssh\)`, and copy it from there.
  
4. Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, enter the name, and add the content of the file copied in the steps above to the public key. 

5. Under the file path `C Disk\User\(Current User’s Name)\.ssh\`, create `New text document.txt`. Open the file and enter the following content(change the username), then save it:

   ```yaml
   Host lev
       HostName service.lev.zone
       Port 2222
       User username # Leviathan username，not email!
   ```

   After that, **rename the file as `config`**, note that there is no .txt suffix here, the file type is not a text document anymore.

6. Enter the test command `ssh lev` in PowerShell (the $ sign is meaningless, don't enter it):

   ```vbscript
   $ ssh lev
   Connection to service.lev.zone closed. # successful
   ```

   If the following message is returned, there is a problem with the configuration file, please check the previous configuration steps:

   ```Bash
   $ ssh lev
   Permission denied (publickey). # This message indicates that the ssh public key is incorrectly configured, please check whether the ssh public key and username are correct.
   ```

   If the process above shows an error, you can also directly enter the following command line:  
   ```ssh username@service.lev.zone -p 2222```（the username is your Leviathan username，not email!）

   The following statement appears:
   ```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may timeout.

7. Next, add a device on the **[Account Settings-My Device]** page of Leviathan. Enter a name, and click Submit.

8. Then, execute the following command to launch the local environment:

   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.
   INFO:root:Agent is ready
   # Successful
   ```

   After the command is executed, the image container will be automatically pulled and launched. The whole process may take a few minutes. When the containers are launched, everything is ready!

   If the download speed of the container is too slow due to network issues, it can be accelerated by changing the Docker image source:

   ```Bash
   Open Docker, click Settings
   Click Docker Engine, view the below
   ```

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e46b4413-d432-4817-89c3-6abd73b645e8.png)
   
   Paste the following code:

   ```Bash
     "registry-mirrors": [
       "https://docker.mirrors.ustc.edu.cn/"
     ]
   ```

   The location for the paste is as shown in the picture below (Tip for beginners: `"buildkit": true` A half width comma needs to be added after the curly braces in the following line, and the code should be pasted in front of the curly braces in the bottom line, otherwise an error will occur).

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/575d3a79-7772-4318-a566-7293f809a4c5.png)
   
   Click Apply & Restart!

   After completing the acceleration operations above, execute the following command again to launch the local environment:

   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.
   INFO:root:Agent is ready
   # Successful
   ```

   Finally, in [Account Settings -> My Device], you can see that the device is online! 
   

### Linux

#### 1.  Install docker

​		Ubuntu:	[reference documentation](thtps://docs.docker.com/engine/install/ubuntu/)

​		CentOS:	[reference documentation](https://docs.docker.com/engine/install/centos/)

​		Debian:	[reference documentation](https://docs.docker.com/engine/install/debian/)

#### 2. Generate ssh key

Enter the command: `ls -la ~/.ssh` to detect whether there is a ssh key in the system. If not, follow the below commands to generate a key pair:	

```
$ ssh-keygen -t ecdsa -C "youremail@example.com"
# Replace the email with Leviathan's registered email
# When an Input option appears, click Enter
# It is not recommended to use the rsa encryption algorithm to
# generate a key pair because ecdsa is more secure, and the rsa key
# is disabled by default in the higher version of openssh
```

#### 3. Upload

Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, enter the name, and copy the content of the id_ed25519.pub public key file in the .ssh folder generated in the steps above to the public key. 

#### 4.Configure the ssh config file

`touch ~/.ssh/config`

 Write as follows:

```bash
Host lev
	HostName service.lev.zone
	Port 2222
	User username # Leviathan username
```

Then enter the command `ssh lev` to verify if the configuration was successful:

```Bash
$ ssh lev
Connection to service.lev.zone closed. # Succeessful
```

If the following message is returned, there is a problem with the configuration file, please check the previous configuration steps:

```Bash
$ ssh lev
# The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.
Permission denied (publickey). # If this message displays, the ssh public key is incorrectly configured. Please check whether the ssh public key and username are correct.
```

If the process above shows an error, you can also directly enter the following command: 
```ssh username@service.lev.zone -p 2222```（the username is your Leviathan username，not email!）

The following statement appears: 
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` enter yes immediately and press Enter. Do not wait too long, otherwise the startup may fail.

#### 5. Launch

First, add a device on the [Account Settings - My Device] page, enter a name, and click Submit.

Next, execute the following command to launch the local environment (if the pull speed is slow, it is recommended to modify the image source of docker to that of the University of Science and Technology of China):

```bash
$ ssh lev agent | sudo docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
INFO:root:Agent is ready
# Successful
```

After the command is executed, the image container will be automatically pulled and launched. The whole process may take a few minutes. When the containers are launched, everything is ready!

Finally, in [Account Settings - My Device], you can see that the device is online!  
![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/f947afbe-93f3-4764-a97f-df67cbc4b14a.JPEG)

Next, let's take a look at how to use Leviathan to run tools/workflows for asset security detection! 


### Mac

#### Install docker

If Docker has been installed on your device, please check its version number. Leviathan supports 20.10.10 and above. If this condition is met, you can skip this step, otherwise, please install or update Docker as follows. 

Download the [docker installation file](https://docs.docker.com/get-docker/)

Click the file to launch Docker. Open Docker by clicking the Desktop icon. Docker has a graphical interface under **Mac**.

When the Docker Logo on the lower left corner is green, it means you have successfully launched Docker.![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

#### Generate ssh public key

Open the folder (Finder) under Mac, press the combination of `shift`+`command`+`G`, the following pop-up Windows will appear, enter `~/.ssh` . Enter the .ssh directory, and check whether the files `id_ed25519` and `id_ed25519.pub` exist.

![go_to_folder.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/01912358-ec54-41a7-9aa8-527a525ecbc0.JPEG)

If not, enter the following command:

```
Mac:
$ ssh-keygen -t ed25519 -C "youremail@example.com"
# Replace the email with Leviathan's registered email
# When an Input option appears, click Enter
# It is not recommended to use the rsa encryption algorithm to generate a key pair because ed25519 is more secure，and the rsa key is disabled by default in the higher version of openssh
```

#### Upload ssh public key

Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, enter a name for the key, and copy the content of the id_ed25519.pub public key file generated in the steps above to the public key field.

```sh
# Mac: View .pub file，copy and upload to Leviathan(recommended)
$ cat ~/.ssh/id_ed25519.pub
```

#### Configure ssh config file

First, you need to find the file directory where the ssh public key is stored. Once you find this directory (often, `~/.ssh/config`), run the command:`touch ~/.ssh/config` to create a config file and enter the following (changing the username to your own):

```bash
Host lev
    HostName service.lev.zone
    Port 2222
    User username # Leviathan username, not email!
```

Use the following command to verify whether the configuration is successful.

```Bash
$ ssh lev
Connection to service.lev.zone closed. # Successful
```

If the following message is returned, there is a problem with the configuration file and you should verify the previous configuration steps:

```Bash
$ ssh lev
Permission denied (publickey). # This message indicates that the ssh public key is incorrectly configured, please check if the ssh public key and username are correct.
```

If the process above shows an error, you can also directly enter the following command: 
```ssh username@service.lev.zone -p 2222```（the username is your Leviathan username，not email!）

The following statement appears:
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` enter yes and press Enter immediately, (do not wait too long, as the setup may timeout).

#### Launch

First, add a device on the [Account Settings - My Device] page, enter a name, and click Submit.

Next, execute the following command to launch the local environment (If the pull speed is slow, we recommended modifying the image source of docker to that of the University of Science and Technology of China; feel free to google this for more information): 

```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes and press Enter immediately, (do not wait too long, as the setup may timeout).
INFO:root:Agent is ready
# Successful
```

After the command is executed, the image container will be automatically pulled and launched. The whole process may last a few minutes. When the containers are all launched, everything is ready! 



## 2. Leviathan Cyber Security Platform

Log in to Leviathan, you can then see a total of 6 items in the directory bar.

![header.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/552b148e-90e4-4173-8806-6bbab6217ce8.JPEG)

- Task: Detection tasks you have run
- Collection: Your favorite tools/workflows/authors
- Repository: Tools/workflows you have uploaded
- Market: Tools/workflows published publicly
- Forum: A place to communicate with other community members
- Document: Usage and development documentations

### 2.1 Choose a tutorial

If this is your first time to Leviathan, you may not know where to start, or what tools should be experienced firstly, you can go to the Forum. Using the official penetration testing tutorials 1-4, you can easily complete a web penetration test on Leviathan! 

![image_1.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/a6b43e3e-9a12-42e4-bb35-51f8cc8df13e.JPEG)

### 2.2 Select tools

If you have a specific tool to use, you can find the relevant tools or workflows in the Market and directly run the task.

![image_2.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/f6fac1d4-508d-4254-9b4d-9b11b271afd0.png)

Find the tool you want to use and go to the tool detail page.

![image_3.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/95fa22c3-f026-4f15-9e17-b42d0316bdf7.JPEG)

The tool page is divided into the following four modules:

![image_4.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/27ced2ad-f6e9-4f02-8a69-5c502e543133.JPEG)

- Introduction/Basic Information:
  This briefly describes the function, version, and usage of the tool. For example, it shows that, to run the tool in Quick Mode, you only need to enter the url parameter, which is a string.

- Source:
  Here you can view the invocation source code of the tool to learn more about it.

- Comment:
  Here you can share your tips or thoughts on using the tool.

- History:
  Here you can view the invocation log of the tool.

---

You can create a task after you find the tool you want to run! There are two methods to create a new Task:

#### 2.2.1 Method 1: Create a new task on the tool page

Click `New Task` on the upper right of the tool page to enter the invocation interface of the tool.

![header_nmap.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/16eb4d67-9c99-41ee-9bca-0f1e4b6e0d8f.JPEG)

#### 2.2.2 Method 2: Collect tools/workflows and create tasks

On the upper right of the tool page, select Collect, add tool to `Collection`. After that, you can view the tool and create new tasks in `Collection` page.

![image_6.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/1c5679a5-7df0-48be-859c-593de707c9ce.JPEG)

### 2.3 Create the first task

Let's take a look at what information is needed to complete the creation of the task!

When creating a task, the first step (after naming your task of course!) is to choose the mode (i.e. what function of the tool you want to use).

Next you need to select the method of invocation:

- Quick Mode: No code knowledge is required, and the tool can be enabled with one click by directly entering parameters on the web page.
- Customized Mode: 100% of the tool capabilities can be exerted, with a high degree of freedom, which is convenient for debugging and advanced users (but may be more complicated for beginning users or simple use cases).

#### 2.3.1 Quick Mode 

We will look at Nmap as an example.

First, choose the `Nmap typical invocation` mode, keep the default method of invocation (Quick Mode). Then fill in the parameters required by the task and submit it.

In this mode, a detection only requires some basic target information.

![image_7.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0bd2ea8b-9514-4b55-a7e6-5127ce9d3c45.JPEG)

You can see that the task is being executed, and you can view the results after the task is complete.

![image_8.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0c5e9676-834b-4ced-a74c-acdd6599372a.JPEG)

#### 2.3.2 Customized Mode 

We have learned about Quick Mode Invocation. However, actual penetration testing situations changes rapidly and often require added customization fo tools. How can we realize the full performance of the tool?

In this section，we will learn how to conduct a customized invocation of a tool.

Here we use Nmap to perform a customized scan on an IP.

First, enter the `New task` page for Nmap.

Choose Raw Mode, and keep the default method of invocation (Customized Mode).

![image_9.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/be280f8d-1a0c-4bf5-bf02-fe4ee5a725c9.JPEG)

The use of the customized invocation command is exactly the same as running the tool directly on the command line. The only difference is that the argv parameter is a list. The only thing we have to do is upload the input command as a list into the argv parameter.

If you want to learn about the parameters and commands of one specific tool, you can visit the official details document of that tool.

For example, we want to scan an IP with NMap, so our command line arguments are:

```Bash
nmap -T4 -A -v -Pn IP    
```

We then convert each flag/input to an element of the argv list, as shown below:

![image_10.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/bdb7143f-2a95-4735-b867-89763e615798.JPEG)

We then submit it.

### 2.4 View Results

After the execution of the task, click `Result` to view the running result of the task. 

![image_11.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2eb75d26-4fd7-4073-b538-f161fdc7a3be.JPEG)

This completes a simple tool invocation.

### 2.5 Debug

Unexpected errors may occur during the task execution. Most are caused by input parameters (such as the target being inaccessible, or simply the wrong URL). So how do you debug it?

![debug](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/efc3a8d5-6d97-4b3a-b2b7-803e94136411.JPEG)

In the picture above, you can see the task is running. First, you can view the task log by clicking the `Log` button. This shows the running status of the task.

If the log cannot be used to directly identify or solve the problem, select the Debug button.

![debug_en](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4dd15574-2cfd-403d-92d9-2c661f26a24b.JPEG)

Leviathan will then give you a debug command. We copy it into the command line of the environment for execution, and we can enter the debug command line inside the container.

Within the container, debugging can be performed with Ipdb. Ipdb is a python code command-line debugging tool that integrates with ipython. For specific use cases, you can move to the Documentation of ipdb.

![image_15.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4d65446f-dfc3-4e73-860b-c3eeb323dcfc.png)
