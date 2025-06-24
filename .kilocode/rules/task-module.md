# Task Module
CraftyCore provides a module to run actions async

## How To Use
You can access the task api through the main plugin instance. You can use it to run async and sync tasks.

```java
public void runMyTasks() {
    Main.INSTANCE.task...
}
```

The task api provides the following methods:

```java
/**
 * Executes a given task on a specified target thread and returns the result.
 *
 * @param <T> The type of the result produced by the task.
 * @param action The supplier representing the task to be executed.
 *               This task will be executed on the specified thread.
 * @param onSuccess The action to be run on success
 * @param targetThread The thread on which the task should be executed.
 */
<T> void run(Callable<T> action, Consumer<T> onSuccess, TargetThread targetThread);

/**
 * Executes a given task on a specified target thread and returns the result.
 *
 * @param <T> The type of the result produced by the task.
 * @param action The supplier representing the task to be executed.
 *               This task will be executed on the specified thread.
 * @param onSuccess The action to be run on success
 * @param onFailure The action to be run on failure
 * @param targetThread The thread on which the task should be executed.
 */
<T> void run(Callable<T> action, Consumer<T> onSuccess, Runnable onFailure, TargetThread targetThread);

/**
 * Executes a given task on a specified target thread.
 *
 * @param action The runnable task to be executed. This task will be executed
 *               on the specified thread.
 * @param targetThread The thread on which the task should be executed. This
 *                     specifies the execution context for the task.
 */
void run(Runnable action, TargetThread targetThread);

/**
 * Schedules a task for execution on a specified target thread after a specified delay
 * and returns the result of the task execution.
 *
 * @param <T> The type of the result produced by the task.
 * @param action The supplier representing the task to be executed.
 *               This task will be executed on the specified thread after the delay.
 * @param onSuccess The action to be run when the task is complete.
 * @param delay The delay before the task is executed.
 * @param targetThread The thread on which the task should be executed.
 */
<T> void schedule(Callable<T> action, Consumer<T> onSuccess, UnitNumber<TimeUnit> delay, TargetThread targetThread);

/**
 * Schedules a task for execution on a specified target thread after a specified delay
 * and returns the result of the task execution.
 *
 * @param <T> The type of the result produced by the task.
 * @param action The supplier representing the task to be executed.
 *               This task will be executed on the specified thread after the delay.
 * @param onSuccess The action to be run when the task is complete.
 * @param onFailure The action to be run on failure.
 * @param delay The delay before the task is executed.
 * @param targetThread The thread on which the task should be executed.
 */
<T> void schedule(Callable<T> action, Consumer<T> onSuccess, Runnable onFailure, UnitNumber<TimeUnit> delay, TargetThread targetThread);


/**
 * Schedules a task for execution on a specified target thread after a specified delay.
 *
 * @param action The runnable task to be executed. This task will be scheduled to
 *               run on the specified thread after the delay period has elapsed.
 * @param delay The delay before the task is executed.
 * @param targetThread The thread on which the task should be executed. This specifies
 *                     the execution context for the task.
 */
void schedule(Runnable action, UnitNumber<TimeUnit> delay, TargetThread targetThread);
```

To create a UnitNumber of type TimeUnit, you can do the following:

```java
UnitNumber.of(0, TimeUnit.MILLISECOND);
```

The package for unit number is dev.crafty.core.number, the package for all units is dev.crafty.core.number.units.

TimeUnit has the following items:
- MILLISECOND
- SECOND
- MINUTE
- HOUR
- TICK

You can convert to any other time type by doing:
```java
UnitNumber.of(0, TimeUnit.MILLISECOND).convertTo(TimeUnit.HOUR);
```

You have access to different target threads. Here are the ones:
```java
/**
 * Represents a virtual thread as the target execution context for a task.
 * <p>
 * When this option is used, the task will execute on a virtual thread, which is
 * lightweight and managed by the Java runtime, allowing for highly concurrent
 * and scalable task execution.
 */
VIRTUAL,
/**
 * Represents a platform-specific thread as the target execution context for a task.
 * This option indicates that the task should execute on a thread determined by the platform,
 * which may depend on the underlying operating system or runtime configuration.
 * <p>
 * This is used for more CPU-intensive workloads. For normal-intensity workloads and IO calls,
 * use {@link TargetThread#VIRTUAL}
 */
PLATFORM,

/**
 * Represents the main thread as the target execution context for a task.
 * This option specifies that the task should execute on the main thread.
 */
MAIN
```

The package for the TargetThread enum is dev.crafty.core.task.api.