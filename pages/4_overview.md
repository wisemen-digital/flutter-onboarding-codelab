## Feature: Todo Overview

Create a new branch from the `feature/login` branch called `feature/todo-overview`.

First off we'll run our Feature Brick. In your terminal, run: `mason make wise_feature`.
Enter the feature's name: `todos-overview`
We'll be using all modules, so press enter to confirm. This will create a new feature, next up we'll run our build runner again.

This will be the main screen of our app. So we need to make sure this is where the user ends up after logging in or opening the app.
Replace all the references to the `EmptyScreenRoute` in the `auth_guard.dart` and `login_navigation_manager_impl` with the `TodosOverviewScreenRoute`.
After doing this, you may hot restart and end up in the `TodosOverviewScreen`.

### 4.1 Networking
In the network folder, create a new folder called services. In this folder, create a new file called `todo_service.dart`.
This file will contain the service that will handle all the networking for the todos.

Add the following code to the file:
```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

import '../clients/protected_client.dart';

class TodosService {
  TodosService(this.ref);
  final Ref ref;

  Future<dynamic> getTodos({
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

### 4.2 Local storage
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

Now you can create an extension function on your todo dto model to map it to the table model (repositories/dto_mapper):
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

#### 4.2.1 Dao
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
    required Iterable<TodosTableCompanion> todos,
  }) async {
    await batch(
      (batch) => batch.insertAllOnConflictUpdate(
        todosTable,
        todos,
      ),
    );
    await _deleteTodos(todos: todos);
  }

  Stream<List<TodoObject>> getTodos({
    required int limit,
    required int offset,
  }) {
    return (select(todosTable)
          ..limit(
            limit,
            offset: offset,
          ))
        .get();
  }

  Future<void> _deleteTodos({
    required Iterable<TodosTableCompanion> todos,
  }) async {
    await (delete(todosTable)
          ..where(
            (tbl) => tbl.uuid.isNotIn(todos.map((e) => e.uuid.value).toList()),
          ))
        .go();
  }
}

final todosDaoProvider = Provider.autoDispose(
  (ref) => TodosDao(ref.read(databaseServiceProvider)),
);
```

### 4.3 Repository
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

This repository needs to be accessible to our features, without them knowing it exists. We will use Riverpod (provider) to provide the repository to the rest of the app.
We'll achieve this by adding this to feature_init_util.dart.

```dart
TodosOverviewFeature.init(
  navigationManager: todosNavigationManagerProvider,
  repository: todosRepositoryProvider,
);
```

### 4.4 Data
This is the feature model. It contains the data that will be displayed in the UI. Check the documentation and what fields you need in the design to create the model.
```dart
class Todo {
  final String uuid;
  //...

  Todo({
    required this.uuid,
    //...
  });
}
```

If we look at the designs, what do we need? We need a list of todos, paginated.
Pagination is a way to split up a large list of items into smaller chunks. This way we can load the data in parts, which is more efficient.
We need the data from the api and store them into our local database.

Place this in the `todos_overview_repository.dart` file.
```dart
Future<void> getPaginatedTodos({
    required int limit,
    required int offset,
  });
  Future<List<Todo>> getTodos({
    required int limit,
    required int offset,
  });
```

This will give an issue on the `TodosRepositoryImpl` class, because it doesn't implement the method.
Click `command + .` on the class name and select 'implement missing members'. This will add the method to the class.

#### 4.4.1 Pagination Model
Create a new file in the `shared/utils` folder called `pagination_model.dart` and add the following code:
```dart
class Paginated<T> {
  final List<T> items;
  final Map<String, int> meta;

  Paginated({
    required this.items,
    required this.meta,
  });

  factory Paginated.fromJson(
    Map<String, dynamic> json,
    T Function(Map<String, dynamic>) fromJson,
  ) {
    return Paginated(
      items: (json['items'] as List).map((e) => fromJson(e)).toList(),
      meta: Map<String, String?>.from(json['meta']),
    );
  }
}
```

This model will be used to parse the paginated data from the api. It contains a list of items and a map with meta data. The meta data can contain information like the total number of items, the current page, etc.

Go back to the `TodosRepositoryImpl` class and implement the following functions:
```dart
@override
Future<void> getPaginatedTodos({required int limit, required int offset}) async {
  try {
    final todos = await ref.read(todosServiceProvider).getTodos(
          limit: limit,
          offset: offset,
        );
    final paginatedTodos = Paginated<TodoDTO>.fromJson(todos, TodoDTO.fromJson);
    await ref.read(todosDaoProvider).insertTodos(
          todoDTOs: paginatedTodos.items.map((todo) => todo.toCompanion()),
        );
  } catch (error) {
    rethrow;
  }
}
```

Some explanation for the code above: This function has return type of `void` because this calls the data from the api and writes it to the local database. It doesn't return anything to the UI.
It requires 2 parameters: `limit` and `offset`. These are used to paginate the data. The function reads the todos from the api, parses them to a `Paginated` object and writes them to the local database.

```dart
 @override
Future<List<Todo>> getTodos({required int limit, required int offset}) async {
  return (await ref.read(todosDaoProvider).getTodos(
            limit: limit,
            offset: offset,
          ))
      .map((e) => e.toModel())
      .toList();
}
```

This function has a return type of `List<Todo>` because this function returns the data to the UI. It requires 2 parameters: `limit` and `offset`. These are used to paginate the data. The function reads the todos from the local database and maps them to a `Todo` object.

We still need to add the map function. Add the following code to `repositories/table_mappers/todos_table_mapper.dart`. This code will map the table model to the feature model.
```dart
extension TodosTableMapper on TodoObject {
  Todo toModel() => Todo(
        uuid: uuid,
        title: title,
        description: description,
        completed: isCompleted,
        deadline: deadline,
        createdAt: createdAt,
        updatedAt: updatedAt,
      );
}
```

### 4.5 Page Controller
We need to add a page controller to the feature. This controller will handle the pagination logic. Open the `todos_overview_providers.dart` file.

```dart
import 'package:infinite_scroll_pagination/infinite_scroll_pagination.dart';
import 'package:onboarding_todo_app/features/shared/shared.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';
import 'package:talker_flutter/talker_flutter.dart';

import '../todos_overview.dart';

part 'todos_overview_providers.g.dart';

class TodosOverviewProviders {
  static var todosPageController = todosPageControllerProvider;
}

@riverpod
// ignore: unsupported_provider_value
class TodosPageController extends _$TodosPageController {
  static const _pageSize = AppConstants.pageSize;
  @override
  PagingController<int, Todo> build() {
    return PagingController<int, Todo>(firstPageKey: 0)..addPageRequestListener(_fetchNewPage);
  }

  Future<void> _fetchNewPage(
    int pageKey,
  ) async {
    try {
      await ref
          .read(TodosOverviewFeature.todosOverviewRepository)
          .getPaginatedTodos(
            limit: _pageSize,
            offset: pageKey * _pageSize,
          )
          .catchError(
        (error) {
          TalkerLogger().error(error);
        },
      );

      final items = await ref.read(TodosOverviewFeature.todosOverviewRepository).getTodos(
            limit: _pageSize,
            offset: pageKey * _pageSize,
          );

      final isLastPage = items.length < _pageSize;
      if (isLastPage) {
        state.appendLastPage(items);
      } else {
        final nextPageKey = pageKey + 1;
        state.appendPage(items, nextPageKey);
      }
    } catch (error) {
      state.error = error;
    }
    ref.notifyListeners();
  }
}
```

First and foremost we need a build function that returns a `PagingController`. This controller will handle the pagination logic.
The int is the page key and the Todo is the data type that will be paginated.
In the constructor the first page key is passed. We also add a listener to the controller that listens for page requests.

We create a _fetchNewPage function that takes a page key as a parameter.
We have a try-catch block that fetches the paginated data from the api. It has a silently catch block that logs the error.
This is done so when the api calls fail, the app doesn't crash and still queries the local database.
When it's done calling the api, it fetches the todos from the local database.
Followed by a check if it's the last page. If it is, it appends the last page to the state. If it's not, it appends the page to the state and increments the page key.

The `ref.notifyListeners()` is called to notify the listeners that the state has changed.

### 4.6 UI
Let's create the UI for the todos overview screen. Open the `todos_overview_screen.dart` file.

```dart
import 'package:auto_route/auto_route.dart';
import 'package:flutter/material.dart';
import 'package:hooks_riverpod/hooks_riverpod.dart';
import 'package:infinite_scroll_pagination/infinite_scroll_pagination.dart';
import 'package:onboarding_todo_app/generated/l10n.dart';
import 'package:onboarding_todo_app/theme/theme.dart';
import 'package:wise_nav_bar/wise_nav_bar.dart';
import 'package:wisewidgetslibrary/wise_widgets_library.dart';

import '../todos_overview.dart';

@RoutePage()
class TodosOverviewScreen extends ConsumerWidget {
  const TodosOverviewScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return Scaffold(
      body: CustomScrollView(
        slivers: [
          PlatformSliverAppBar.text(
            title: S.of(context).myTodos,
          ),
          SliverPadding(
            padding: padH16.copyWith(
              bottom: Sizes.p24,
            ),
            sliver: PagedSliverList<int, Todo>.separated(
              pagingController: ref.watch(
                TodosOverviewProviders.todosPageController,
              ),
              separatorBuilder: (context, index) => gapH10,
              builderDelegate: PagedChildBuilderDelegate<Todo>(
                // TODO: Styling
                itemBuilder: (context, item, index) => ListTile(
                  title: Text(item.title),
                ),
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

Start by switching the `StateLessWidget` to a `ConsumerWidget`. The consumer widget is a widget that can read providers and listen to changes.
Change the body with a `CustomScrollView`. This is a scrollable view that allows you to create custom scroll effects.
Add a `PlatformSliverAppBar` with the title 'My Todos'. This is a custom app bar that adapts to both iOS and Android styles. Slivers are scrollable areas that can be composed to create custom scroll effects.
Add a `SliverPadding` with padding and a `PagedSliverList`. This is a sliver list that fetches data from the `PagingController` and displays it in a list.
The `PagedSliverList` has a `separatorBuilder` that adds a gap between the items.
The `builderDelegate` is a delegate that builds the list items.
The `itemBuilder` builds the list items.

You can see that the background color does not match with the design. Open the `AppTheme` and add the following code:
```dart
scaffoldBackgroundColor: _AppColors.catskillWhite.white,
```

This will only apply on Android. For iOS, we need to add the background color to the screen.
Open the `todos_overview_screen.dart` and add the following line in the scaffold and the app bar:
```dart
backgroundColor: context.catskillWhite,
```

Make sure to commit these changes. Create a PR and assign your buddy as a reviewer.
