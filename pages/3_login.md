## Login
We use zitadel as our identity provider. For more information about zitadel, please visit [zitadel.com](https://zitadel.com).
Create a new branch from the `feature/setup-theme` branch called `feature/login`.

### 3.1 Login Brick
We are going to run our first Brick. A Brick is a code generator that generates code for you. We use Bricks to generate boilerplate code for us. This way we can focus on the important stuff.
Run the login Brick from your terminal by executing `mason make wise_login`.

Open the terminal and check if your buildrunner is still working. if not: execute `buildrunner`

### 3.2 Add an empty screen
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

### 3.3 Add the route
Open the `app_router.dart` file and add the following route to the `AutoRouter`:
```dart
AdaptiveRoute(
      page: EmptyScreenRoute.page,
    )
```

### 3.4 Navigation after login
Open the `auth_guard.dart` and add the following case to the switch statement:
```dart
case AuthenticationStatus.authenticated:
        resolver.redirect(
          const EmptyScreenRoute(),
        );
```

### 3.5 Button widget
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

### 3.6 Login screen
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
