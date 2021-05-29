

# Installing ContikiOS

Installing [ContikiOS](http://www.contiki-os.org/) historical (<3.0) in new Linux based machines. 
Just follow the sequence of commands below (I've tested those commands on a Ubuntu 20.04 vm).

1- Install the required packages
```console
sudo apt-get install build-essential binutils-msp430 gcc-msp430 msp430-libc binutils-avr gcc-avr gdb-avr avr-libc avrdude binutils-arm-none-eabi gcc-arm-none-eabi gdb-multiarch openjdk-8-jdk openjdk-8-jre ant libncurses5-dev doxygen srecord git
```
The main difference from [ContikiOS Wiki](https://github.com/contiki-os/contiki/wiki/Setup-contiki-toolchain-in-ubuntu-13.04) are the openjdk version (7->8) and gdb (gdb-arm-none-eabi -> gdb-multiarch)

The renaming is the same:

2- Clone the ContikiOS repository
```console
 git clone --recursive git://github.com/contiki-os/contiki.git contiki
```

3- Test the installation
```
cd contiki/examples/hello-world
make TARGET=native hello-world
./hello-world.native
```
The result in my machine:
```
Contiki-3.x-3345-g32b5b17f6 started with IPV6, RPL
Rime started with address 1.2.3.4.5.6.7.8
MAC nullmac RDC nullrdc NETWORK sicslowpan
Tentative link-local IPv6 address fe80:0000:0000:0000:0302:0304:0506:0708
Hello, world
```

Do not forguet to set the java 8:

```
sudo update-alternatives --config java
```

Set your `JAVA_HOME` by putting this script in your `bashrc` or `.zshrc`:

```
if [ -z "${JAVA_HOME}" ]
then
    JAVA_HOME=$(readlink -nf $(which java) | xargs dirname | xargs dirname | xargs dirname)
    if [ ! -e "$JAVA_HOME" ]
    then
        JAVA_HOME=""
    fi
    export JAVA_HOME=$JAVA_HOME
fi
```

# Mobility plugin for ContikiOS Cooja sim/emu.

The original plugin will not plug and simulated in new ContikiOS (>3.0) due to java issues. In order to fix the isuses, it is necessary to do some updates:
- Fix the coojar.jar directory in the build.xml (line 7) file 
>` <property name="cooja_jar" value="PATH TO cooja.jar"/>` 

> for example: **/home/user/contiki/tools/cooja/dist/cooja.jar**
- Update some deprecated message classes in the mobility.java file
> Those updates have already been made

### Steps to install the plugin in a fresh ContikiOS installation:

1- Download the mobility pluing
> Download the mobility folder of this reposiroty
> Copy the mobility folder to `<DIR-CONTIKI>/tools/cooja/apps/`

2- Check if the cooja.jar path in the build.xml line 7 file is ok.

> The build.xml is in `<DIR-CONTIKI>/tools/cooja/apps/mobility`
> for example: **/home/user/contiki/tools/cooja/dist/cooja.jar**

3- Compile the plugin

- Navigate to:
```
cd contiki/tools/cooja/apps/mobility
sudo ant jar
```
- Depending on your permission, it may be necessary run `sudo ant run`

3- Start the Cooja sim.
```
cd contiki/tools/cooja
ant run
```
Depending on your permission, it may be necessary run `sudo ant run`

4- Tell to Cooja that the mobility plugin exists
- Settings > External Tools Pat...
- Find DEFAULT_PROJECTDIRS
- Insert `;` symbol at the end 
- add the pluging path `[CONTIKI_DIR]/tools/cooja/apps/mobility`
- Save and restart Cooja.

5- Enable the mobility plugin

- Start the Cooja sim
- go to Settings>Cooja Extensions
- Check if the mobility plugin is enabled
- Save
- Now, under Tools tab, you should find "Mobility ..."  option.

6- Try the following position file

#node time(s) x y
```
0 0.0 0 10
0 1.0 10 10
0 2.0 10 0
0 3.0 0 0
0 4.0 0 10
```
save the above lines in a .txt file, except the header, then provide file to Cooja through "Mobility..." option.

 
credits to:

Marcus Lunden (Plugin author (I think so)), SICS, mlunden@sics.se.

George Fatsis [(someone else that needed the pluging working in >2020 rsrs)](https://github.com/georgefatsis)

