author: Wisemen
summary: Wiselab Flutter 1
id: index
tags:
categories:
environments:
status: Draft

# Wiselab Flutter 1

## What you'll learn: overview

<img width="200" src="img/branding/wisemen_logo.png" />

Hello fellow developer! Welcome to **Wisemen**, we are happy to have you on board! We want to make sure you are
soon up to speed with the core of Flutter development and our way of working.
Therefore we created this awesome series of **CodeLabs** for you. These are a constant work in progress, so please
let us know if you have any feedback or suggestions!
This is what you will learn in this CodeLab:

### 1. Prerequisites

* Basic knowledge of Dart
  * If this is not the case, we recommend you to read the [Dart Basics](https://dart.dev/language)
* IDE installed (we recommend [VSCode](https://code.visualstudio.com/))

### 3. What are you going to build?

In this CodeLab you are going to build a simple Todo app in Flutter. The app will contain a few screens where the user can create and manage tasks. The requirements are written out in Linear **TODO: Add Linear**.
The designs for this app are here: [Figma wireframes](https://www.figma.com/file/hebgv4Qx8VanMAQkO1NFpa/Onboarding-to-do?node-id=407-4095&t=2qdyy89lKwN7dFw3-0)

### Let's get started!

### 4. Create a new App
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

#### Add flavours
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

### 4.1 Our Branching strategy

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

### 4.2 Theme
#### Colors
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

#### ColorScheme
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

#### TextStyles
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


### 5. Login
We use zitadel as our identity provider. For more information about zitadel, please visit [zitadel.com](https://zitadel.com).
Create a new branch from the `feature/setup-theme` branch called `feature/login`.

#### Login Brick
We are going to run our first Brick. A Brick is a code generator that generates code for you. We use Bricks to generate boilerplate code for us. This way we can focus on the important stuff.
Run the login Brick from your terminal by executing `mason make wise_login`.

Open the terminal and check if your buildrunner is still working. if not: execute `buildrunner`

#### Add an empty screen
Add to your `shared/views` a `empty_screen.dart` file and add a empty screen like the following:
```dart
import 'package:auto_route/auto_route.dart';
import 'package:flutter/material.dart';

@RoutePage()
class EmptyScreen extends StatelessWidget {
  const EmptyScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Empty Screen'),
      ),
      body: const Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Text('Empty Screen'),
        ],
      ),
    );
  }
}
```

This view contains a simple scaffold with an appbar and a text widget. This is a good starting point for a new screen.

Reminder to add the `empty_screen.dart` to the views.dart barrel file in the same folder.

#### Add the route
Open the `app_router.dart` file and add the following route to the `AutoRouter`:
```dart
AdaptiveRoute(
      page: EmptyScreenRoute.page,
    )
```

#### Navigation after login
Open the `auth_guard.dart` and add the following case to the switch statement:
```dart
case AuthenticationStatus.authenticated:
        resolver.redirect(
          const EmptyScreenRoute(),
        );
```

#### Button widget
We will start by creating our own primary button widget, this is the grey button that is visible in the login screen.
In `/features/shared/widgets` create a new file called `primary_button.dart`. Normally we try to prevent comments in our code unless it is very necessary. 
We believe that all of our code should be understandable without comments by using good naming conventions and clear code structure.

In the following code we added comments to explain the code so you understand what's happening.
```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:wisewidgetslibrary/wise_widgets_library.dart';

import '../../../theme/theme.dart';

// A custom button widget that adapts to both Cupertino (iOS) and Material (Android) styles (PlatformWidget).
class PrimaryButton extends PlatformWidget {
  // Constructor to initialize the button's properties.
  const PrimaryButton({
    super.key,
    required this.text, // The button label text.
    required this.onPressed, // The callback function when pressed.
    this.isDisabled = false, // Whether the button is disabled.
    this.isLoading = false, // Whether the button is in a loading state.
    this.height = HEIGHT, // The height of the button.
    this.leading, // An optional leading widget (icon, for example).
    this.trailing, // An optional trailing widget (icon, for example).
  });

  final double height;
  final String text;
  final VoidCallback onPressed;
  final bool isDisabled;
  final bool isLoading;
  final Widget? leading;
  final Widget? trailing;

  // Default button height.
  static const double HEIGHT = 48;
  // Border radius for rounded corners.
  static final BORDER_RADIUS = BorderRadius.circular(5);

  // Cupertino (iOS-style) button implementation.
  @override
  Widget createCupertinoWidget(BuildContext context) {
    return SizedBox(
      height: height, // Ensures the button maintains the specified height.
      child: CupertinoButton(
        padding: padH8, // Horizontal padding (assumed predefined).
        onPressed: isDisabled || isLoading ? null : onPressed, // Disables interaction when necessary.
        disabledColor: context.sanJuan.withValues(
          alpha: .4, // Reduced opacity when disabled.
        ),
        color: context.sanJuan, // Button background color.
        borderRadius: BORDER_RADIUS, // Rounded corners.
        child: Center(
          child: AnimatedSwitcher(
            duration: Duration.zero, // Instantly switches between loading and text states.
            child: isLoading
                    ? CupertinoActivityIndicator( // Shows a loading indicator when isLoading is true.
              color: context.catskillWhite,
            )
                    : Row(
              mainAxisSize: MainAxisSize.min, // The row takes only necessary space.
              spacing: 8, // Spacing between child elements.
              children: [
                if (leading != null) leading!, // Displays leading widget if provided.
                Text(
                  text,
                  style: context.button.copyWith(
                    color: isDisabled
                            ? context.catskillWhite.withValues(alpha: .4) // Faded color when disabled.
                            : context.catskillWhite, // Default text color.
                  ),
                ),
                if (trailing != null) trailing!, // Displays trailing widget if provided.
              ],
            ),
          ),
        ),
      ),
    );
  }

  // Material (Android-style) button implementation.
  @override
  Widget createMaterialWidget(BuildContext context) {
    return SizedBox(
      height: height, // Ensures the button maintains the specified height.
      child: MaterialButton(
        height: height,
        onPressed: isDisabled || isLoading ? null : onPressed, // Disables interaction when necessary.
        disabledColor: context.sanJuan.withValues(
          alpha: .4, // Reduced opacity when disabled.
        ),
        color: context.sanJuan, // Button background color.
        shape: RoundedRectangleBorder(
          borderRadius: BORDER_RADIUS, // Rounded corners.
        ),
        elevation: 0, // No shadow effect.
        child: Center(
          child: AnimatedSwitcher(
            duration: Durations.short1, // Short animation duration when switching child widgets.
            child: isLoading
                    ? CircularProgressIndicator( // Shows a loading indicator when isLoading is true.
              color: context.catskillWhite,
            )
                    : Row(
              mainAxisSize: MainAxisSize.min, // The row takes only necessary space.
              spacing: 8, // Spacing between child elements.
              children: [
                if (leading != null) leading!, // Displays leading widget if provided.
                Text(
                  text,
                  style: context.button.copyWith(
                    color: isDisabled
                            ? context.catskillWhite.withValues(alpha: .4) // Faded color when disabled.
                            : context.catskillWhite, // Default text color.
                  ),
                ),
                if (trailing != null) trailing!, // Displays trailing widget if provided.
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

Make sure to commit these changes.

#### Login screen
First off you will need our custom platform appbar widget. In your terminal, run: `mason make wise_nav_bar`.

Now you are going to add the login button to the login screen. Open the `login_screen.dart` file and add the just created button to the screen on the correct place.
use `PlatformAppBar.text()` and add a title and background color according to the design. The title should be 'Login' and 'Inloggen' for the Dutch version. We achieve this by localizing the string.
This is done by selecting the string including the quotes and right click and use `extract to ARB`. Click enter twice to confirm the key and value.
To translate the string, open the `intl_nl.arb` file in the `l10n` folder and change the value to Dutch.

From now on, we will always be localizing our strings!

Next up, we'll fill up the body of the scaffold with the login button.
Use the `PrimaryButton` widget and add the text 'Login' to it. Style it with the correct padding and positioning according to the design.

Make sure to test the login. Try logging in, and then hot restart the app to see if the bearer is stored and used correctly. If this is the case, you should automatically land on the empty screen.

Commit these changes and create a PR to the develop branch and assign your buddy as a reviewer.

### 6. Feature: Todo Overview

Create a new branch from the `feature/login` branch called `feature/todo-overview`.

First off we'll run our Feature Brick. In your terminal, run: `mason make wise_feature`.
Enter the feature's name: `todos-overview`
We'll be using all modules, so press enter to confirm. This will create a new feature, next up we'll run our build runner again.

This will be the main screen of our app. So we need to make sure this is where the user ends up after logging in or opening the app.
Replace all the references to the `EmptyScreenRoute` in the `auth_guard.dart` and `login_navigation_mananger_impl` with the `TodosOverviewScreenRoute`.
After doing this, you may hot restart and end up in the `TodosOverviewScreen`.

#### 6.1 Networking
In the network folder, create a new folder called services. In this folder, create a new file called `todo_service.dart`.
This file will contain the service that will handle all the networking for the todos.

Add the following code to the file:
```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

import '../clients/protected_client.dart';

class TodosService {
  TodosService(this.ref);
  final Ref ref;

  Future<void> getTodos({
    required int limit,
    required int offset,
  }) async {
    try {
      return await ref.read(protectedClientProvider).wGet(
        'api/v1/todos',
      );
    } catch (error) {
      rethrow;
    }
  }
}
```

Let's explain what's happening here:
* We import the necessary packages.
* We create a class called `TodosService` that takes a `Ref` as a parameter. This `Ref` is used to read the `protectedClientProvider`.
* We create a method called `getTodos` that takes a `limit` and `offset` as parameters. This method will make a `GET` request to the `api/v1/todos` endpoint. We use the `protectedClientProvider` to make the request.
* We catch any errors and rethrow them. This way we can handle the errors in the UI.

Create your dto model by creating a folder `network/dto/todos` and create a file called `todo.dart`:
```dart
import 'package:drift/drift.dart';
import 'package:json_annotation/json_annotation.dart';
import 'package:onboarding_todo_app/database/database.dart';

part 'todo_dto.g.dart';

@JsonSerializable()
class TodoDTO {
  final String uuid;
  final String createdAt;
  //...

  const TodoDTO({
    required this.uuid,
    required this.createdAt,
    //...
  });

  factory TodoDTO.fromJson(Map<String, dynamic> json) => _$TodoDTOFromJson(json);

  Map<String, dynamic> toJson() => _$TodoDTOToJson(this);
}
```
Add the necessary fields from the api documentation.

#### 6.2 Local storage
We will be using drift for our local storage. Drift is a simple and efficient way to store data locally in your app using SQLite.
First off create a table by creating a new folder called `tables` with a file called `todos_table.dart`.

Add the following code to the file:
```dart
import 'package:drift/drift.dart';

@DataClassName('TodoObject')
class TodosTable extends Table {
  TextColumn get uuid => text()();
  TextColumn get title => text()();
  //...

  @override
  Set<Column> get primaryKey => {uuid};
}
```
Make sure to add the necessary fields.

Now you can create an extension function on your todo dto model to map it to the table model:
```dart
extension TodoDTOMapper on TodoDTO {
  TodosTableCompanion toCompanion() {
    return TodosTableCompanion(
      uuid: Value(uuid),
      title: Value(title),
      //...
    );
  }
}
```

#### Dao
A DAO (Data Access Object) is a class that provides an abstract interface to some type of database or other persistence mechanism.
```dart
import 'package:drift/drift.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:onboarding_todo_app/network/dto/todos/todo_dto.dart';

import '../database.dart';
import '../tables/tables.dart';

part 'todos_dao.g.dart';

@DriftAccessor(tables: [TodosTable])
class TodosDao extends DatabaseAccessor<Database> with _$TodosDaoMixin {
  TodosDao(super.attachedDatabase);

  Future<void> insertTodos({
    required List<TodoDTO> todoDTOs,
  }) async {
    await batch(
      (batch) => batch.insertAllOnConflictUpdate(
        todosTable,
        todoDTOs.map(
          (e) => e.toCompanion(),
        ),
      ),
    );
    await _deleteTodos(todoDTOs: todoDTOs);
  }

  Stream<List<TodoObject>> watchTodos({
    required int limit,
    required int offset,
  }) {
    return (select(todosTable)
          ..limit(
            limit,
            offset: offset,
          ))
        .watch();
  }

  Future<void> _deleteTodos({
    required List<TodoDTO> todoDTOs,
  }) async {
    await (delete(todosTable)
          ..where(
            (tbl) => tbl.uuid.isNotIn(todoDTOs.map((e) => e.uuid).toList()),
          ))
        .go();
  }
}

final todosDaoProvider = Provider.autoDispose(
  (ref) => TodosDao(ref.read(databaseServiceProvider)),
);
```

#### 6.3 Repository
A repository is a class that abstracts the data layer from the rest of the app.
It is responsible for fetching data from the network, store it in the local storage and providing it to the rest of the app.

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:onboarding_todo_app/features/todos_overview/todos_overview.dart';

class TodosRepositoryImpl implements TodosOverviewRepository {
  TodosRepositoryImpl(this.ref);
  final Ref ref;
}

final todosRepositoryProvider = Provider.autoDispose(
  (ref) => TodosRepositoryImpl(ref),
);
```