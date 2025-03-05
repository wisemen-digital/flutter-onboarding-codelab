## Feature: Add todo
Create a new branch from the `feature/todo-overview` branch called `feature/add-todo`.
First off add an `floatingActionButton` to the `TodosOverviewScreen`. This button will navigate to the `AddTodoScreen`.
```dart
floatingActionButton: FloatingActionButton.small(
        backgroundColor: context.sanJuan,
        elevation: 0,
        onPressed: ref.read(TodosOverviewFeature.todosOverviewNavigationManager).navigateToAddTodo,
        shape: const RoundedRectangleBorder(
          borderRadius: rad8,
        ),
        child: Icon(
          Icons.add_rounded,
          color: context.white,
        ),
      )
```

### 5.1 AddTodoScreen
Run the feature Brick. In your terminal, run: `mason make wise_feature`.
Now you should be able to create a new feature called `add_todo`.

Don't hesitate to ask your buddy for help if you're stuck.

### 5.2 navigation
Next, add a `navigateToAddTodo` inside the `TodosOverviewNavigationManager` class and implement it in the impl class.

If the add todo feature is ready, create a PR and assign your buddy as a reviewer.

### 6 Feature: Styling overview
Create a new branch from the `feature/add-todo` branch called `feature/styling-overview`.

Style the overview according to the design. Make sure to use the correct colors, fonts, and paddings.

Don't forget to create a PR and assign your buddy as a reviewer.

### 7 Feature: Edit todo
Now you should be able to implement the following feature: editing a todo. Create a new branch from the `feature/styling-overview` branch called `feature/edit-todo`.
Think ahead about a strategy to implement this feature. Discuss it with your buddy before you start.

After finishing the feature, you are able to finish up the whole application.

#### Good luck!