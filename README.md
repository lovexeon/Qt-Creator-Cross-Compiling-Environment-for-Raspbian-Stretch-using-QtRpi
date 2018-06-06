## Qt-Creator-Cross-Compiling-Environment-for-Raspbian-Stretch-using-QtRpi
This post shows how to create Qt Creator cross development environment on Ubuntu PC for Raspbian Stretch by using QtRpi. Although QtRpi does not officially support Raspbian Stretch , additional few steps make it possible to create the cross development environment for Stretch. 

                                          Pre-requisities ->
                                          Here are some pre-requisities you need before you can start
                                          Ubuntu Linux running on host PC
                                          Raspbian Stretch running on target Raspberry Pi 3
                                          Qt Creator running on host PC
                                          SSH access from host PC to target Raspberry Pi 3
                                            
                                          In this post, the versions I use are below:
                                          Ubuntu 16.04 (host)
                                          Qt Creator 4.6.1 (host)
                                          Raspbian Stretch April 2018 version running on Raspberry Pi 3 (target                                                         Qt 5.7.0 (target)
                                              
### Steps
### 1. Host Preparation (Ubuntu)
### 1-1. Install dependencies.

*sudo apt-get update*
*sudo apt-get install curl g++ gdb-multiarch git unzip zip -y*

### 1-2. Configure environment variable for Qt version. As of writing this, QtRpi supports only 5.6.2 and 5.7.0. [2]

*export QTRPI_QT_VERSION='5.7.0'*

### 1-3. Configure environment variable for Raspberry Pi model. For different models, check the official site.

*export QTRPI_TARGET_DEVICE='linux-rpi3-g++'*

### 1-4. Configure environment variable for target IP address.

*export QTRPI_TARGET_HOST='pi@192.168.0.5'*

### 1-5. Generate SSH key. Just press enter when you asked about pass phrase.

*ssh-keygen*

### 1-6. Copy the key to the target so that you can log in without requiring a password afterwards.

*ssh-copy-id $QTRPI_TARGET_HOST*
 

### 2. QtRpi Installation
### 2-1. On host, download QtRpi from GitHub.

*git clone https://github.com/neuronalmotion/qtrpi.git && cd qtrpi*

### 2-2. Then run “init-qtrpi-minimal.sh” script. This will take a while.

*./init-qtrpi-minimal.sh*
 

### 3. Deploy to Raspberry Pi
### Before start the deployment script, one modification is required since a name of a package has been changed. [3]

### 3-1. Open the deployment script file.

*nano deploy-qtrpi.sh*

### 3-2. Look for the line below and replace “libinput5” with “libinput10”

*ssh $TARGET_HOST 'sudo apt-get install -y libts-0.0-0 libinput5 fontconfig'*

### After the change, the line should be this:

*ssh $TARGET_HOST 'sudo apt-get install -y libts-0.0-0 libinput10 fontconfig'*

### 3-3. Then, run the deployment script.

./deploy-qtrpi.sh --prepare-rpi
 
### 4. Raspberry Pi Configuration
### 4-1. Login to Raspberry Pi.

*ssh $QTRPI_TARGET_HOST*

### 4-2. Create symbolic links since some libraries has changed names in Stretch. [4] [5] [6]

*sudo ln -s /opt/vc/lib/libbrcmEGL.so /usr/lib/arm-linux-gnueabihf/libEGL.so*
*sudo ln -s /opt/vc/lib/libbrcmGLESv2.so /usr/lib/arm-linux-gnueabihf/libGLESv2.so*

### 4-3. Copy font files.

*cp -r /usr/share/fonts/truetype/dejavu /usr/local/qt5pi/lib/fonts*
Note: This step is a workaround for font display. (see troubleshoot in the previous post for detail.)

