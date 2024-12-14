# Stages of the Activity Lifecycle
- *Activity Lifecycle*: The transition of activities through various states.
- An activity is an entry point for interacting with the users.
- The Activity Lifecycle extends from the creation of the activity to its destruction, when the system reclaims the activity's resources.
## Explore the Lifecycle Methods and Add Basic Logging
- Entry point of Android activities is `onCreate()` unlike it generally being `main()`. 
- The following diagram shows all the activity lifecycle states. They represent that status of the activity. NOTE: An Android app can have multiple activities, however recommended to have only a single one.
![The Activity Lifecycle scheme](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/468988518c270b38.png?authuser=1)

- When want to change some behaviour, the activity lifecycle state changes. Therefore the `Activity` class itself, and of its subclasses e.g. `ComponentActivity` implement a set of lifecycle callback methods. Callbacks invokes when activity moves between states, can be overridden with your own activities to perform tasks in response to lifecycle state changes.  

### Step 1: Examine the `OnCreate()` method and Add Logging
- Logging enables writing short messages to console while app is running, to see when different callbacks are triggered.
- 