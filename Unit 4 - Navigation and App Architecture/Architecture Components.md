# Stages of the Activity Lifecycle
- *Activity Lifecycle*: The transition of activities through various states.
- An activity is an entry point for interacting with the users.
- The Activity Lifecycle extends from the creation of the activity to its destruction, when the system reclaims the activity's resources.
## Explore the Lifecycle Methods and Add Basic Logging
- Entry point of Android activities is `onCreate()` unlike it generally being `main()`. 
- The following diagram shows all the activity lifecycle states. They represent that status of the activity. NOTE: An Android app can have multiple activities, however recommended to have only a single one.
![The Activity Lifecycle scheme|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/468988518c270b38.png?authuser=1)

- When want to change some behaviour, the activity lifecycle state changes. Therefore the `Activity` class itself, and of its subclasses e.g. `ComponentActivity` implement a set of lifecycle callback methods. Callbacks invokes when activity moves between states, can be overridden with your own activities to perform tasks in response to lifecycle state changes.  

### Step 1: Examine the `OnCreate()` method and Add Logging
- Logging enables writing short messages to console while app is running, to see when different callbacks are triggered.
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {  
    enableEdgeToEdge()  
    super.onCreate(savedInstanceState)  
    Log.d(TAG, "onCreate Called")  
    setContent {  
        DessertClickerTheme {  
            // A surface container using the 'background' color from the theme  
            Surface(  
                modifier = Modifier  
                    .fillMaxSize()  
                    .statusBarsPadding(),  
            ) {  
                DessertClickerApp(desserts = Datasource.dessertList)  
            }  
        }    }}
```
- The `onCreate()` method has the one-time initializations. For example, `setContent()` which specified the activity's UI layout. When called, the OS creates the new `Activity` object in memory.
> [!Note]
> When overriding `onCreate()`,  you must call the superclass implementation to complete the creation of the Activity, so within it, must call `super.onCreate()`. Same is true for other lifecycle callback methods.

- For logging:
	1. Add constant above `MainActivity` class declaration: `private const val TAG = "MainActivity"`.
		NOTE: `const` marks variable as compile-time constant.
	2. In `onCreate()` after `super...`, add: `Log.d(TAG, "OnCreate Called")`
	3. Import [`Log`](https://developer.android.com/reference/kotlin/android/util/Log?authuser=1) class: `import andoird.util.Log`
- `Log` class writes messages to the **Logcat**, a console for logging messages. Three important aspects to the `Log` instruction:
	- *priority*: Signifies the importance of the message. 
		- `Log.v()`: Verbose messages
		- `Log.d()`: Debug messages
		- `Log.i()`: Informational messages
		- `Log.w()`: Warnings
		- `Log.e()`: Error messages
	- `tag`: String that lets you easily find your log messages in the Logcat. The `tag` is typically the name of the class.
	- `msg`: Short string.
### Step 2: Implement the `OnStart()` method
- Called after `onCreate()`. 
- After `onStart()` runs, activity is visible on the screen. Can be called many times in the lifecycle of your activity.
- `onStart()` is paired with a corresponding `onStop()` lifecycle method. If user starts app then returns to device's home screen, activity is stopped and no longer visible.
- Add logging to `onStart()`
```kotlin
override fun onStart() {
	super.onStart()
	Log.d(TAG, "onStart Called")
}
```
- Note: The `onStart()` is called when the app is reopened after going to the device's home screen.

### Step 3: Add more log statements
```kotlin
override fun onResume() {
	super.onResume()
	Log.d(TAG, "onResume called")
}

override fun onRestart() {
	super.onRestart()
	Log.d(TAG, "onRestart called")
}

override fun onPause() {
	super.onPause()
	Log.d(TAG, "onPause called")
}

override fun onStop() {
	super.onStop()
	Log.d(TAG, "onStop called")
}

override fun onDestroy() {
	super.onDestroy()
	Log.d(TAG, "onDestroy called")
}
```

When an activity starts from the beginning, you see all three of these lifecycle callbacks called in order:
- `onCreate()` when the system creates the app.
- `onStart()` makes the app visible on the screen, but the user is not yet able to interact with it.
- `onResume()` brings the app to the foreground, and the user is now able to interact with it.
Despite the name, the `onResume()` method is called at startup, even if there is nothing to resume.

## Explore Lifecycle Use Cases

Now that you have set up the Dessert Clicker app for logging, you're ready to start using the app and exploring how lifecycle callbacks are triggered.

### Use case 1: Opening and closing the activity

You start with the most basic use case, which is to start your app for the first time and then close the app.

1. Compile and run the Dessert Clicker app, if it is not already running. As you've seen, the `onCreate()`, `onStart()`, and `onResume()` callbacks are called when the activity starts for the first time.

```
2024-04-26 14:56:48.684  5484-5484  MainActivity            com.example.dessertclicker           D  onCreate Called
2024-04-26 14:56:48.709  5484-5484  MainActivity            com.example.dessertclicker           D  onStart Called
2024-04-26 14:56:48.713  5484-5484  MainActivity            com.example.dessertclicker           D  onResume Called
```

2. Tap the cupcake a few times.
3. Tap the **Back** button on the device.

Notice in Logcat that `onPause()` and `onStop()` are called in that order.

2024-04-26 14:58:19.984  5484-5484  MainActivity            com.example.dessertclicker           D  onPause Called
2024-04-26 14:58:20.491  5484-5484  MainActivity            com.example.dessertclicker           D  onStop Called
2024-04-26 14:58:20.517  5484-5484  MainActivity            com.example.dessertclicker           D  onDestroy Called

In this case, using the **Back** button causes the activity (and the app) to be removed from the screen and moved to the back of the activity stack.

The Android OS might close your activity if your code manually calls the activity's [`finish()`](https://developer.android.com/reference/android/app/Activity.html?authuser=1#finish()) method or if the user force-quits the app. For example, the user can force-quit or close the app in the Recents screen. The OS might also shut down your activity on its own if your app has not been onscreen for a long time. Android does so to preserve battery life and to reclaim the resources the app was using so they are available to other apps. These are just a few examples of why the Android system destroys your activity. There are additional cases when the Android system destroys your activity without providing a warning.

**Note:** `onCreate()` and `onDestroy()`, which this codelab teaches later, are only called once during the lifetime of a single activity instance: `onCreate()` to initialize the app for the very first time, and `onDestroy()` to nullify, close, or destroy objects that the activity may have been using so that they don't continue to use resources, like memory.

### Use case 2: Navigating away from and back to the activity

Now that you've started the app and closed it, you've seen most of the lifecycle states for when the activity gets created for the first time. You've also seen most of the lifecycle states that the activity goes through when it gets closed. But as users interact with their Android devices, they switch between apps, return home, start new apps, and handle interruptions by other activities such as phone calls.

Your activity does not close down entirely every time the user navigates away from that activity:

- When your activity is no longer visible on screen, the status is known as putting the activity into the _background_. The opposite of this is when the activity is in the _foreground_, or onscreen.
- When the user returns to your app, that same activity is restarted and becomes visible again. This part of the lifecycle is called the app's _visible_ lifecycle.

When your app is in the background, it generally should not be actively running to preserve system resources and battery life. You use the `Activity` lifecycle and its callbacks to know when your app is moving to the background so that you can pause any ongoing operations. You then restart the operations when your app comes into the foreground.

In this step, you look at the activity lifecycle when the app goes into the background and returns again to the foreground.

1. With the Dessert Clicker app running, click the cupcake a few times.
2. Press the **Home** button on your device and observe the Logcat in Android Studio. Returning to the home screen puts your app into the background, rather than shutting down the app altogether. Notice that the `onPause()` and `onStop()` methods are called.

2024-04-26 15:00:04.905  5590-5590  MainActivity            com.example.dessertclicker           D  onPause Called
2024-04-26 15:00:05.430  5590-5590  MainActivity            com.example.dessertclicker           D  onStop Called

When `onPause()` is called, the app no longer has focus. After `onStop()`, the app is no longer visible on screen. Although the activity is stopped, the `Activity` object is still in memory in the background. The Android OS has not destroyed the activity. The user might return to the app, so Android keeps your activity resources around.

![c470ee28ab7f8a1a.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/c470ee28ab7f8a1a.png?authuser=1)

3. Use the Recents screen to return to the app. On the emulator, the Recents screen can be accessed by the square system button shown in the image below.

Notice in Logcat that the activity restarts with `onRestart()` and `onStart()` and then resumes with `onResume()`.

![bc156252d977e5ae.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/bc156252d977e5ae.png?authuser=1)

**Note**: `onRestart()` is only called by the system if the activity has already been created and eventually enters the **Created** state when `onStop()` is called, but returns back to the **Started** state instead of entering the **Destroyed** state. The `onRestart()` method is a place to put code that you only want to call if your activity is **not** being started for the first time.

2024-04-26 15:00:39.371  5590-5590  MainActivity            com.example.dessertclicker           D  onRestart Called
2024-04-26 15:00:39.372  5590-5590  MainActivity            com.example.dessertclicker           D  onStart Called
2024-04-26 15:00:39.374  5590-5590  MainActivity            com.example.dessertclicker           D  onResume Called

When the activity returns to the foreground, the `onCreate()` method is not called again. The activity object was not destroyed, so it doesn't need to be created again. Instead of `onCreate()`, the `onRestart()` method is called. Notice that this time when the activity returns to the foreground, the **Desserts sold** number is retained.

4. Start at least one app other than Dessert Clicker so that the device has a few apps in its Recents screen.
5. Bring up the Recents screen and open another recent activity. Then go back to recent apps and bring Dessert Clicker back to the foreground.

Notice that you see the same callbacks in Logcat here as when you pressed the **Home** button. `onPause()` and `onStop()` are called when the app goes into the background, and then `onRestart()`, `onStart()`, and `onResume()` are called when it comes back.

**Note:** `onStart()` and `onStop()` are called multiple times as the user navigates to and from the activity.

These methods are called when the app stops and moves into the background or when the app restarts and returns to the foreground. If you need to do some work in your app during these cases, then override the relevant lifecycle callback method.

### Use case 3: Partially hide the activity

You've learned that when an app is started and `onStart()` is called, the app becomes visible on the screen. When `onResume()` is called, the app gains the user focus–that is, the user can interact with the app. The part of the lifecycle in which the app is fully onscreen and has user focus is called the [foreground lifetime](https://developer.android.com/reference/android/app/Activity?authuser=1#activity-lifecycle).

When the app goes into the background, the focus is lost after `onPause()`, and the app is no longer visible after `onStop()`.

The difference between focus and visibility is important. An activity can be _partially_ visible on the screen but not have the user focus. In this step, you look at one case in which an activity is partially visible but doesn't have user focus.

1. With the Dessert Clicker app running, click the **Share** button in the top right of the screen.

The sharing activity appears in the lower half of the screen, but the activity is still visible in the top half.

![677c190d94e57447.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/677c190d94e57447.png?authuser=1)![ca6285cbbe3801cf.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/ca6285cbbe3801cf.png?authuser=1)

2. Examine Logcat and note that only `onPause()` was called.

2024-04-26 15:01:49.535  5590-5590  MainActivity            com.example.dessertclicker           D  onPause Called

In this use case, `onStop()` is not called because the activity is still partially visible. But the activity does not have user focus, and the user can't interact with it—the "share" activity that's in the foreground has the user focus.

Why is this difference important? The interruption with only `onPause()` usually lasts a short time before returning to your activity or navigating to another activity or app. You generally want to keep updating the UI so the rest of your app doesn't appear to freeze.

Whatever code runs in `onPause()` blocks other things from displaying, so keep the code in `onPause()` lightweight. For example, if a phone call comes in, the code in `onPause()` may delay the incoming-call notification.

3. Click outside the share dialog to return to the app, and notice that `onResume()` is called.

Both `onResume()` and `onPause()` have to do with focus. The `onResume()` method is called when the activity gains focus, and `onPause()` is called when the activity loses focus.

## Explore Configuration Changes
- A *configuration change* occurs when the state of the device changes so radically that the easiest way for the system to resolve the change is to completely shut down and rebuild the activity.
- The last lifecycle callback is `onDestroy()`, called after `onStop()`. Called just before the activity is destroyed. Can happen when the app's code calls `finish()`, or system needs to destroy and recreate activity because of the configuration change.

### Configuration Change Causes `onDestroy` to be Called
Screen rotation is one type of a configuration that causes the activity to shutdown and restart.

```
2024-04-26 15:03:32.183  5716-5716  MainActivity            com.example.dessertclicker           D  onPause Called
2024-04-26 15:03:32.185  5716-5716  MainActivity            com.example.dessertclicker           D  onStop Called
2024-04-26 15:03:32.205  5716-5716  MainActivity            com.example.dessertclicker           D  onDestroy Called
```

### Data Loss on Device Rotation

```
2024-04-26 15:04:29.356  5809-5809  MainActivity            com.example.dessertclicker           D  onCreate Called
2024-04-26 15:04:29.378  5809-5809  MainActivity            com.example.dessertclicker           D  onStart Called
2024-04-26 15:04:29.382  5809-5809  MainActivity            com.example.dessertclicker           D  onResume Called
2024-04-26 15:06:52.168  5809-5809  MainActivity            com.example.dessertclicker           D  onPause Called
2024-04-26 15:06:52.183  5809-5809  MainActivity            com.example.dessertclicker           D  onStop Called
2024-04-26 15:06:52.219  5809-5809  MainActivity            com.example.dessertclicker           D  onDestroy Called
2024-04-26 15:06:52.302  5809-5809  MainActivity            com.example.dessertclicker           D  onCreate Called
2024-04-26 15:06:52.308  5809-5809  MainActivity            com.example.dessertclicker           D  onStart Called
2024-04-26 15:06:52.312  5809-5809  MainActivity            com.example.dessertclicker           D  onResume Called
```

- When the device is rotated, the activity is shut down and re-created. Thus the activity restarts with default values.

### Lifecycle of a Composable
- UI of app initially built from running composable functions is process called Composition.
- When app state changes, recomposition happens. 
- Recomposition is required to create or update a Composition.
- Composable functions have their own lifecycle, independent of the Activity lifecycle.
- While Compose remembers the revenue state during recompositions, it does not retain the state during a configuration change. For Compose to retain the state during a configuration change, must use `rememberSaveable`.

### Use `rememberSaveable` to Save Values across Configuration Changes
- Use `rememeberSaveable` function to save values you need if Android OS destroys and recreates the activity.
- To save values using recompositions, need to use `remember`. Use `rememberSaveable` to save values during recompositions and configuration changes. It also ensures data is available when activity is restored, if needed.
- How to change it:
```kotlin
var revenue by remember {mutableStateOf(0)}
...
var currentDessertImageId by remember {
	mutableStateOf(desserts[currentDessertIndex].imageId)
}
```

```kotlin
var revenue by rememberSaveable {mutableStateOf(0)}
...
var currentDessertImageId by rememberSaveable {
	mutableStateOf(desserts[currentDessertIndex].imageId)
}
```

# ViewModel and State in Compose
- [`ViewModel`](https://developer.android.com/topic/libraries/architecture/viewmodel?authuser=1) is an architecture component from the Android Jetpack libraries that can store your app data. Stored data is not lost if the framework destroys and recreates the activities during a configuration change, or other events. However, data is lost if activity is destroyed because of death process. The `ViewModel` only caches data through quick activity recreations.

## Learn About App Architecture
The most common [architectural principles](https://developer.android.com/jetpack/guide?authuser=1#common-principles):
- **Separation of concerns**: App is divided into classes of functions, each with separate responsibilities.
- **Drive UI from a model**: Drive UI from a model, preferably a persistent model. Models are components responsible for handling data for an app. They're independent from the UI elements and app components, thus unaffected by app's lifecycle and associated concerns.

### Recommended App Architecture
Each app should have at least 2 layers given above:
- **UI Layer**: Layer that displays app data on the screen, independent of data.
- **Data Layer**: Layer that stores, retrieves, and exposes app data.
Can also add *domain layer* to simplify and reuse interactions between the UI and data layers.
 ![a4da6fa5c1c9fed5.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/a4da6fa5c1c9fed5.png?authuser=1)

#### UI Layer
- Role: Display application data on the screen.
- UI should reflect changes when data changes due to user interaction (e.g. clicking button)
- Components:
	- **UI elements**: components that render data on the screen. These elements built using [Jetpack Compose](https://developer.android.com/jetpack/compose?authuser=1).
	- **State Holder**: components that hold data, expose it to UI, and handle app logic (e.g. ViewModel).

#### ViewModel
- `ViewModel` component holds and exposes the state the UI consumes.
- UI state is application data transformed by `ViewModel`. 
- It stores the app-related data that isn't destroyed when activity is destroyed and recreated by the Android framework.  Unlike activity instance, `ViewModel` objects are not destroyed. 
- To implement `ViewModel` is app, extend the `ViewModel` class, which comes from the architecture components library and stores app data within that class.

#### UI State
- UI is what user sees, and UI state is what the app says they should see.
- UI is visual rep. of the UI state. UI state changes are reflected in UI.

![9cfedef1750ddd2c.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/9cfedef1750ddd2c.png?authuser=1)

```kotlin
// Example of UI state def
data class NewItemUiState(
	val title: String,
	val body: String,
	val bookmarked: Boolean = false,
	...
)
```
#### Immutability
- UI state definition is immutable.
-  Immutable objects provide guarantees that multiple sources do not alter the state of the app at an instant in time. Thus the UI can focus on a single role: readings state and updating UI elements accordingly.
- => Should never modify the UI state in the UI directly, unless UI is the sole source of data. 

## Add a ViewModel
1. In `build.gradle.kts (Module :app)`, add the following dependence for `ViewModel`:
```kotlin
dependencies {
	...
	implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.6.1)
}
```
2. In the `ui` class, create a new class `GameViewModel` as an extension of `ViewModel`:
```kotlin
import androidx.lifecycle.ViewModel

class GameViewModel: ViewModel() {

}
```
3. In the `ui` class, create a new class `GameUiState`:
```kotlin
data class GameUiState(
	val currentScrambleWord: String = ""
)
```

### StateFlow
- [`StateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/): Data holder observable flow that emits the current and new state updates. Its [`value`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/value.html) property reflects the current state value. To update state and send it to the flow, assign a new value to the value property of the [`MutableStateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-state-flow/index.html) class.
- `StateFlow` works well with classes that must maintain an observable immutable state
- `StateFlow` can be exposed from the `GameUiState` so that composables can listen for UI state updates and make the screen state survive configuration changes
- In `GameViewModel`, add following: `private val _uiState = MutableStateFlow(GameUiState())`.
### Backing Property
- Backing property: Lets you return something from a getter other than the exact object.
- For `var` property, the Kotlin framework generates getters and setters. These methods can be overridden to provide custom behaviour. 
- To implement a backing property, override the getter method to return a read-only version of data. Example of backing property:
```kotlin
// Example code

// Declare private mutable variable that can only be modified
// within the class it is delcared.
private var _count = 0

// Declare another public immutable field and override its getter method.
// Return the private property's value in the getter method.
// When count is accessed, the get() function is called and
// the value of _count is returned.
val count: Int
	get() = _count
```

As another example, you want the app data to be private to the `ViewModel`:
	Inside the `ViewModel` class:
		- The property `_count` is `private` and mutable. Hence, only accessible and editable within the `ViewModel` class.
	Outside the `ViewModel` class:
		- Default visibility modifier in Kotlin is `public`, so `count` is public and accessible from other classes like UI controllers.
		- `val` type cannot have a setter, since it is immutable and read-only, so can only override the `get()` method.
		- When an outside class accesses the property, it returns the value of `_count` and its value cannot be modified.
		- Backing property protects the app data inside the `ViewModel` from unwanted and unsafe changes by external classes, but lets external callers safely access its values.
1. In `GameViewModel.kt`, add a backing property to `uiState` named `_uiState`.  Now `_uiState` is accessible and editable only within `GameViewModel`. Ui can read its value using the read-only property. 

```kotlin
import kotlinx.coroutines.flow.StateFlow
// Game UI state

// Backing property to avoid state updates from other classes
private val _uiState = MutableStateFlow(GameUiState())
val uiState: StateFlow<GameUiState> 
```

2. Set `uiState`. NOTE: `asStateFlow()` makes this mutable state flow as a *read-only* state flow.
```kotlin
import kotlinx.coroutines.flow.StateFlow  
import kotlinx.coroutines.flow.asStateFlow  
  
private val _uiState = MutableStateFlow(GameUiState())  
val uiState: StateFlow<GameUiState> = _uiState.asStateFlow()
```

### Display Random Scrambled Word
- `lateinit var` is a variable which will be initialized later.
- `init {}` block in a Kotlin class is used to execute something immediately after the primary constructor.
```kotlin
private val _uiState = MutableStateFlow(GameUiState())  
val uiState: StateFlow<GameUiState> = _uiState.asStateFlow()  
  
class GameViewModel: ViewModel() {  
    private lateinit var currentWord: String  
    private var usedWords: MutableSet<String> = mutableSetOf()  
  
    init {  
        resetGame()  
    }  
  
    private fun pickRandomWordAndShuffle(): String {  
        // Continue pikcing up a new random word until you get one that hasn't been used before  
        currentWord = allWords.random()  
        if (usedWords.contains(currentWord)) {  
            return pickRandomWordAndShuffle()  
        } else {  
            usedWords.add(currentWord)  
            return shuffleCurrentWord(currentWord)  
        }  
    }  
  
    private fun shuffleCurrentWord(word: String): String {  
        val tempWord = word.toCharArray()  
  
        // Scramble the word  
        tempWord.shuffle()  
        while(String(tempWord).equals(word)) {  
            tempWord.shuffle()  
        }  
        return String(tempWord)  
    }  
  
    fun resetGame() {  
        usedWords.clear()  
        _uiState.value = GameUiState(currentScrambledWord = pickRandomWordAndShuffle())  
    }  
}
```

## Architecting Compose UI
- In compose, only way to update UI is by changing app's state.
- Every time the state of the UI changes, Compose recreates the parts of the UI tree that changed. 
- Because composables can accept state and expose events, the unidirectional data flow pattern fits well with Jetpack Compose. This section focuses on how to implement the unidirectional data flow pattern in Compose, how to implement events and state holders, and how to work with `ViewModel` is Compose.

### Unidirectional Data Flow
- A *unidirectional data flow* (UDF) is a design pattern in which state flows down and events flow up. Following UDF, you can decouple composables that display state in the UI from parts of the app that store and change state.
- UI update loop for an app using UDF looks like the following:
	- *Event*: Part of UI, generates event and passes upward - e.g. button click passed to `ViewModel` to handle - or even passed from other layers of app e.g. indication user session expired.
	- *Update State*: Event handler might change state.
	- *Display State*: State holder passes state down, and UI displays it.
![61eb7bcdcff42227.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/61eb7bcdcff42227.png?authuser=1)

Use of UDF pattern for app architecture has following implications:
- `ViewModel` holds and exposes the state the UI consumes.
- UI state is application data transformed by the `ViewModel`.
- UI notifies `ViewModel` of user events.
- `ViewModel` handles user actions and updates the state.
- Updated state is fed back to the UI to render.
- Process repeats for any event that causes a mutation of state.

### Pass the Data
Pass the `ViewModel` instance to the UI - aka from `GameViewModel` to the `GameScreen()` in `GameScreen.kt`. In `GameScreen()`, use the ViewModel instance to access the `uiState` using `collectAsState()`.

The `collectAsState()` function collects values from `StateFlow` and represents its latest value via [`State`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/State?authuser=1). The [`StateFlow.value`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/value.html) is used as an initial value, and for every new value posted to `StateFlow`, the returned `State` updates, causing recomposition of every `State.value` usage.

![de93b81a92416c23.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/de93b81a92416c23.png?authuser=1)

1. Add a parameter `gameViewModel` to `GameScreen()`.
```kotlin
fun GameScreen (
	gameViewModel: GameViewModel = viewModel()
) {...}
```

2. In `GameScreen()`, add a new variable delegated to `collectAsState()` on `uiState`.
3. Pass `gameUiState.currentScrambledWord` to `GameLayout`.
4. Add `currentScrambledWord` as parameter to `GameLayout`, then add it as the text.

## Handle Last Round of Game

### Display Game End Dialog
- Pass `isGameOVer` data to `GameScreen` from `ViewModel` to use to display an alert dialog with options to end or restart the game.

#### Anatomy of Alert Dialog
![eb6edcdd0818b900.png|300](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/eb6edcdd0818b900.png?authuser=1)

1. Container
2. Icon (optional)
3. Headline (optional)
4. Supporting text
5. Divider (optional)