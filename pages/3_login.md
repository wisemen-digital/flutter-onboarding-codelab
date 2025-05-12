## Login
We use zitadel as our identity provider. For more information about zitadel, please visit [zitadel.com](https://zitadel.com).
Create a new branch from the `feature/setup-theme` branch called `feature/login`.

### 3.1 Wise Zitadel Login package
Add the `wise_zitadel_login` package to your pubspec by executing: `flutter pub add wise_zitadel_login`

Then go to your `main.dart` file and replace the `initCore` function with this:
```dart
final sharedPreferences = await SharedPreferences.getInstance();
final container = ProviderContainer(
  overrides: [
    sharedPreferencesProvider.overrideWithValue(sharedPreferences),
    wiseZitadelOptionsProvider.overrideWithValue(
      WiseZitadelOptions(
        baseUrl: F.zitadelBaseUrl,
        bundleId: F.applicationId,
        applicationId: F.zitadelApplicationId,
        organizationId: F.zitadelOrganisationId,
        buttonOptions: WiseZitadelButtonOptions(
          color: (context) => context.sanJuan,
          buttonTextStyle: (context) => context.button.copyWith(
            color: context.catskillWhite,
          ),
        ),
        onLoginSuccess: (router, ref, token) async {
          if (token == null) {
            return;
          }
          await ref.read(protectedClientProvider).setFreshToken(token: token);
          router.replace(const TodosOverviewScreenRoute());
        },
        supportedTypes: [
          ZitadelLoginType(
            buttonText: 'Login with Apple',
            iconSvgString: AppAssets.appleLogo,
            idp: F.zitadelAppleId,
          ),
          ZitadelLoginType(
            buttonText: 'Login with Google',
            iconSvgString: AppAssets.googleLogo,
            idp: F.zitadelGoogleId,
          ),
        ],
      ),
    ),
  ],
);
```

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

This view contains a simple scaffold with an app bar and a text widget. This is a good starting point for a new screen.

Reminder to add the `empty_screen.dart` to the views.dart barrel file in the same folder.

### 3.3 Add the route
Open the `app_router.dart` file and add the following routes to the `AutoRouter`:
```dart
AdaptiveRoute(
  page: EmptyScreenRoute.page,
),
CustomRoute(
  page: WiseLoginScreenRoute.page,
  transitionsBuilder: TransitionsBuilders.noTransition,
),
```

### 3.4 Navigation after login
Open the `auth_guard.dart` and add the following case to the switch statement:
```dart
case AuthenticationStatus.unauthenticated:
  resolver.redirect(
    WiseLoginScreenRoute(),
  );
case AuthenticationStatus.authenticated:
  resolver.redirect(
    const EmptyScreenRoute(),
  );
```
