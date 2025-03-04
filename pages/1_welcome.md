author: Wisemen
summary: Wiselab Flutter 1
id: index
tags:
categories:
environments:
status: Draft

## Wiselab Flutter

### What you'll learn: overview

<img width="200" src="img/branding/wisemen_logo.png" />

Hello fellow developer! Welcome to **Wisemen**, we are happy to have you on board! We want to make sure you are
soon up to speed with the core of Flutter development and our way of working.
Therefore we created this awesome series of **CodeLabs** for you. These are a constant work in progress, so please
let us know if you have any feedback or suggestions!

### 1. Welcome to our team!

#### Tools
At Wisemen, we use the following tools daily:
- **Slack** – For internal (and sometimes external) communication.
- **Linear** – For project and ticket management.
- **Figma** – Designers create their app prototypes and designs with this. We as developers use it as a guide to build our UI.
- **Google Drive** – As a central document storage. Every project has it's own folder, here you can place every file related to the project like documentation, screenshots... Also we as Flutter-team have [our own space](https://drive.google.com/drive/folders/1Fr92dpOKxVC3u6ZwmlUNwtYjQUw0XwT9).
- **GitHub** – For version control and code review.
- **Proxyman** – To monitor, debug and test API-calls.
- **1password** - Our passwordmanager where you can store your personal credentials and access sensitive information about our apps like environment files, keystores...

Some other tools that might be useful are:
- **Rapidapi (formerly known as Paw)** – To test and modify API-calls and much more (Postman can also be used).
- **Fork** - A git client with a GUI, has a very good overview unlike some other tools.
- **Setapp** - A subscrition service we offer with a bunch of usefull tools.
- **Paste** - A clipboard manager to keep your copy-paste history and use it whenever you want.

#### Flutter Circle
We operate within a circle-based structure, which includes:
- **Meetings** – 2 weekly check-in to discuss the following topics:
    - **What went good/bad** – A quick round to get everyone involved and settled into the meeting. Discussions that come up here are parked and addressed later in the meeting or at another time.
    - **General** – General points, such as a new intern starting.
    - **PR comments** – PR comments we’ve received or given in the past period. So that everyone is up-to-date with new insights like f.e.: more efficient ways of implementing. This also reminds us of general principles that may be forgotten.
    - **Cool things created** – Interesting packages we've used or cool self-made components worth sharing.
    - **Rocks/Goals** – An update on our progress on these topics.
    - **Expertise** – Per stack (iOS and Android), a few Flutter members attend internal expertise meetings to stay up to date on new trends or principles within these stacks.
    - **Discussion** – Discussion of any points raised during the meeting.
    - **Actions** – Action points that arise from the meeting and need to be followed up on.
    - **Varia** – A fun closing round where everyone shares an interesting fact or event.
- **Rocks** – Key goals we focus on (often quarterly). These are bigger goals we work towards and often form a workgroup (see below).
- **Workgroups** – Small teams dedicated to specific topics or improvements.
- **Support** - We are one team and there to help eachother. Feel free to ask for help regarding Flutter or mobile questions in the circle-flutter Slack channel, private message or in person!

#### 1.1 Flutter Setup
##### InstalL VS Code
Install VS Code [with this link](https://code.visualstudio.com/).
##### Install Xcode
In order to develop iOS apps you will need to install Xcode. You can find the app in the [App Store](https://apps.apple.com/be/app/xcode/id497799835?l=nl&mt=12). After downloading you should automatically have an app called "simulator" in which you will be able to open several virtual devices to run and test your Flutter apps.
##### Install CocoaPods
Follow [this tutorial](https://docs.flutter.dev/get-started/install/macos/mobile-ios#install-cocoapods) to install CocoaPods.
##### Android Studio
Install Android studio [with this link](https://developer.android.com/studio/?gclid=Cj0KCQiAjJOQBhCkARIsAEKMtO3zEhdK4_I0CEZic3UH4dl-9gVXuHFR9dCl3TOHKjmv3xWLU3UxfhYaApfAEALw_wcB&gclsrc=aw.ds).
##### Install the Flutter SDK
Follow the steps in the following guide:
https://docs.flutter.dev/get-started/install/macos/mobile-ios#install-the-flutter-sdk
##### Install Flutter extensions
Open VS Code and open the sidebar on the left. Click on the extensions tab and search for the "Flutter" extension or just visit the [extension marketplace](https://marketplace.visualstudio.com/items?itemName=Dart-Code.flutter) and download the flutter extension. This will automatically install the dart extension as well.

Also download the following extensions:
- Dart Barrel File Generator: [marketplace link](https://marketplace.visualstudio.com/items?itemName=miquelddg.dart-barrel-file-generator)
- Flutter Intl: [marketplace link](https://marketplace.visualstudio.com/items?itemName=localizely.flutter-intl)

Other usefull extensions:
- Better Comments: [marketplace link](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments)
- Image preview: [marketplace link](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-gutter-preview)
- Material Icon Theme: [marketplace link](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)
- Surprise (download at your own risk): [marketplace link](https://marketplace.visualstudio.com/items?itemName=VirejDasani.incredibly-in-your-face)

### 2. Prerequisites

* Basic knowledge of Dart
    * If this is not the case, we recommend you to read the [Dart Basics](https://dart.dev/language)
* IDE installed (we recommend [VSCode](https://code.visualstudio.com/))

### 3. What are you going to build?

In this CodeLab you are going to build a simple Todo app in Flutter. The app will contain a few screens where the user can create and manage tasks. The requirements are written out in Linear **TODO: Add Linear**.
The designs for this app are here: [Figma wireframes](https://www.figma.com/file/hebgv4Qx8VanMAQkO1NFpa/Onboarding-to-do?node-id=407-4095&t=2qdyy89lKwN7dFw3-0)

### Let's get started!
