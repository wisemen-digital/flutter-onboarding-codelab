## Create a new App
Start off by creating a new private github Repository on your personal account. Name it `wiselab-flutter-<name>`.

Open a terminal and navigate to a project folder on your machine where the project will be stored. Before cloning your repository, use the terminal and execute 'mason make wise_starter_project'. This will create a new Flutter project with our own custom template project.

Enter your project name: `wiselab_flutter_<name_lastname>` Note: The name should be in snake_case.
Enter the app name: `wiselab_flutter`
The rest of the questions can be entered with the default values. Then press Y to finish.

Then open this project in your IDE.

run the following command: `run build_runner build --delete-conflicting-outputs` to generate the necessary files.

Open Xcode, click runner, targets -> runner: minimum deployments target -> latest iOS version minus 2 versions.

In the VSCode terminal, open the root folder, go to the ios folder, execute `pod install --repo-update` to install the necessary pods.

Now you should be able to build your app. 🚀

### 2.1 Add flavours
Open flavors.dart

Add your base url to the flavors like this:
```dart
static String get baseUrl {
  switch (appFlavor) {
    case Flavor.DEVELOPMENT:
      return 'https://onboarding-todo-api.development.appwi.se';
    case Flavor.STAGING:
    case Flavor.QA:
    case Flavor.PRODUCTION:
    case null:
      return 'null';
  }
}
```

Add client id and client secrets in the same way:
* Client ID: `bdba526c-31b3-4740-a4e6-bfbaf96ec62e`
* Client Secret: `55d5f96e-eb16-4e98-8822-27cba3474e01`

Now add the following block for the `applicationId` getter in the `flavors.dart` file:
```dart
static String get applicationId {
  switch (appFlavor) {
    case Flavor.DEVELOPMENT:
      return 'com.wisemen.app.development';
    case Flavor.STAGING:
    case Flavor.QA:
    case Flavor.PRODUCTION:
    default:
      return 'com.energyking.app.development';
  }
}
```

#### Use the terminal or IDE to link your project to GitHub

We recommend to use a GIT GUI like [SourceTree](https://www.sourcetreeapp.com/) or [Fork](https://git-fork.com/).
As backup we will show you how to work with the ***terminal***.

* Open the terminal in VSCode
* add remote origin to project
* ```shell
  git init
  git remote add origin <your repo url>
  ```

From here on you can choose to use the terminal or the IDE to work with Git.

You may now commit your local changes to the main branch with an 'init project' commit message.

### 2.2 Our Branching strategy

We use the **Gitflow** branching strategy. You can find more information about this
strategy [here](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).

* **develop** branch: this branch contains the latest development changes
* **QA** branch: this branch is used to test the features before they are merged into the staging branch
* **staging** branch: this branch is used send to the client for testing
* **master** branch: this branch contains the latest release of the app

* **feature/...** branches: these branches are used to develop new features for the upcoming release and are only
  pushed to develop
* **bugfix/...** branches: these branches are used to fix bugs in the app and are only pushed to develop

Make sure you have created all 4 'standard' branches. You can do this by either using the terminal, IDE or a GUI
tool like [SourceTree](https://www.sourcetreeapp.com/) or [Fork](https://git-fork.com/).

Now checkout the **develop** branch and create a new feature branch called **feature/setup-theme**.

### 2.3 Theme
#### 2.3.1 Colors
Open 'Colors' tab in the Figma file. Here you can find the colors that are used in the app.

Open `/theme/app_theme.dart` in the project and add the colors to the theme. Edit the _AppColors class like this:
```dart
class _AppColors {
  static const white = Colors.white;
  static const black = Colors.black;
  static const blackPearl = Color.fromRGBO(27, 33, 45, 1);
  static const sanJuan = Color.fromRGBO(71, 81, 97, 1);
  static const shadowBlue = Color.fromRGBO(120, 135, 160, 1);
  //...
}
```

Add the rest of the colors yourself.

#### 2.3.2 ColorScheme
delete darkTheme from `app_theme.dart` and from `app.dart`.

Edit the lightTheme from app_theme.dart like this:
```dart
colorScheme: const ColorScheme.light(
      primary: _AppColors.sanJuan,
      secondary: _AppColors.blackPearl,
      surface: _AppColors.white,
      onSurface: _AppColors.black,
      onPrimary: _AppColors.catskillWhite,
      primaryContainer: _AppColors.shadowBlue,
      onPrimaryContainer: _AppColors.periwinkel,
      tertiary: _AppColors.solitude,
    ),
```

See if there is any room for improvement in the theme and colors. If you have any questions, don't hesitate to contact your buddy.

Then open `theme.dart` and add the following code:
```dart
import 'package:flutter/material.dart';
import 'app_theme.dart';
import 'styles/styles.dart';
import 'text_styles/app_text_styles.dart';

extension AppThemeColorExtension on BuildContext {
  Color get sanJuan => colorScheme.primary;
  Color get blackPearl => colorScheme.secondary;
  Color get solitude => colorScheme.tertiary;
  Color get shadowBlue => colorScheme.primaryContainer;
  Color get periwinkel => colorScheme.onPrimaryContainer;
  Color get catskillWhite => colorScheme.onPrimary;
  Color get black => colorScheme.onSurface;
  Color get white => colorScheme.surface;

  ColorScheme get colorScheme => Theme.of(this).colorScheme;
}
```

This extension will make it easier to access the colors in the app from the context.

#### 2.3.3 TextStyles
In the `Theme.dart` file, add the following code:
```dart
extension TextStyleExtension on BuildContext {
  TextStyle get normal => AppStyles.normal;
  TextStyle get title => AppStyles.title;
  TextStyle get appBarTitle => AppStyles.title;
  TextStyle get label => AppStyles.label;
  TextStyle get button => AppStyles.label;
}
```

This extension will make it easier to access the text styles in the app from the context.

You may now commit these theming changes to the created branch an create a PR to the develop branch. Assign your buddy as a reviewer.
