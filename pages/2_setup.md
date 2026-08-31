## Create a new App
Start off by creating a new private github Repository on your personal account. Name it `wiselab-flutter-<name>`.

We are going to run our first Brick. A Brick is a code generator that generates code for you. We use Bricks to generate boilerplate code for us. This way we can focus on the important stuff.

Open a terminal and navigate to a project folder on your machine where the project will be stored. Before cloning your repository, use the terminal and execute 'mason make wise_starter_project'. This will create a new Flutter project with our own custom template project.

Enter your project name: `wiselab_flutter_<name_last_name>` Note: The name should be in snake_case.
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
      return 'https://onboarding-todo.internal.appwi.se/';
    case Flavor.STAGING:
    case Flavor.QA:
    case Flavor.PRODUCTION:
    case null:
      return 'null';
  }
}
```

These flavors are used to switch between different environments. The baseUrl is used to make API calls to the correct environment.
We use these different environments to test the app in different stages of development.
* Development: Used for local development
* QA: Used by internal QA team (PM, other devs) for testing the app before it goes to Staging
* Staging: Used by the client to test the app before it goes to Production
* Production: Used for the final version of the app

Add these secrets in the same way:
* Client ID: `bdba526c-31b3-4740-a4e6-bfbaf96ec62e`
* Client Secret: `55d5f96e-eb16-4e98-8822-27cba3474e01`
* Zitadel App Id: `305078631263175721`
* Zitadel Organization Id: `284257737964064935`

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
      return 'com.wisemen.app.development';
  }
}
```

#### Use the terminal or IDE to link your project to GitHub

We recommend to use a GIT GUI like [Fork](https://git-fork.com/).
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

We use Trunk-base development with release branches. You can find more information about this
strategy [here](https://www.atlassian.com/continuous-delivery/continuous-integration/trunk-based-development).

* **main** branch: this is the main branch. This branch is always deployable and contains the latest updates.

* **feature/...** branches: these branches are used to develop new features for the upcoming release and are pushed to main
* **bugfix/...** branches: these branches are used to fix bugs in the app and are pushed to main
* **release/x.x** branches: these branches are used to track release versions and their hot fixes

You can use git by either using the terminal, IDE or a GUI tool like [Fork](https://git-fork.com/).

Now checkout the **main** branch and create a new feature branch called **feature/setup-theme**.

### 2.3 Theme
#### 2.3.1 WiseTheme

We set a general WiseTheme to use colors within the app. We do this by add this piece of code to `app.dart`
```dart
final theming = WiseTheming(
  supportedThemes: supportedThemes,
  targetPlatform: Theme.of(context).platform,
  selectedTheme: ref.watch(AppSettingsProviders.themeMode).value,
);
return MaterialApp.router(
  title: F.appName,
  theme: theming.lightTheme,
  darkTheme: theming.darkTheme,
  highContrastTheme: theming.lightContrastTheme,
  highContrastDarkTheme: theming.darkContrastTheme,
  themeMode: theming.themeMode,
  ... other app code
);
```

This theme will make it easier to access the colors in the app from the context.
You can do something similar to what this theme does to create text styles from context like this
```dart
extension TextThemeExtension on BuildContext {
  AppStyles get appStyles => AppStyles(this);
}

class AppStyles {
  const AppStyles(this.context);
  final BuildContext context;

  TextStyle get title => TextStyle(
    fontWeight: .w600,
    color: context.textColors.primary,
    fontSize: 24,
  );

  TextStyle get smallestTitle => TextStyle(
    fontWeight: .w600,
    color: context.textColors.primary,
    fontSize: 18,
  );

  ...
}
```
You may now commit these theming changes to the created branch an create a PR to the main branch. Assign your buddy as a reviewer.
