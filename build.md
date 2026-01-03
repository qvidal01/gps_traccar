Build from Source
Traccar server consists of a server and a web interface. Make sure you get both repositories if you want web interface to work. Web interface is a git submodule in the main Traccar repository, so you can checkout everything you need with a single git command:

git clone --recursive https://github.com/traccar/traccar.git
There are three parts for building the full package:

Build Server
Build Web App
Generate Installers (optional)
In most cases all you need is build Traccar from source code and replace the original files on the server with the newly compiled ones.

The following instructions are only for building the server and web apps. Android and iOS apps are standard Android Studio and Xcode projects, so there are no special instructions required.

For the IDE (Integrated Development Environment) we recommend using IntelliJ IDEA for the server code and VS Code for the web app.

Build Server
To build the server binary file (JAR) use following command:

./gradlew assemble
It will automatically download all dependencies and generate tracker-server.jar in the target subfolder. Now you can just replace the file in your Traccar installation and restart the service for changes to take affect. Make sure you have a compatible version of Traccar installed. Use the latest release when using the master branch for development.

Build Web App
We have two web apps. Use the following links for details on how to build each one:

Modern Web App
Legacy Web App
Generate Installers (optional)
You almost never need to do this. The only reason you would want to generate installer is to distribute your version of Traccar server.

You need to have a Linux system as the script is designed for Linux. We recommend the latest version of Ubuntu. If you are using Windows, you can install Ubuntu in a virtual machine.

To generate installer, execute setup/package.sh shell script in the terminal. The script will check all requirements and show if anything is missing and where to download missing package.
