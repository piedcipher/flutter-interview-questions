# Flutter Interview Questions

A collection of Flutter interview questions and answers for learning and interview preparation.

## 1. What are platform channels?

Platform channels let Dart code communicate with native Android or iOS code, for example to access a device API that is not available through Flutter packages. Messages pass through a named channel and are encoded and decoded using a codec.

## 2. What is the difference between an EventChannel and a MethodChannel?

A `MethodChannel` is for a request and its response: ask the native side to perform an operation and return one result. For example, request the current battery level:

```dart
const batteryChannel = MethodChannel('device/battery');
final level = await batteryChannel.invokeMethod<int>('getBatteryLevel');
```

An `EventChannel` is for a stream of ongoing updates, such as accelerometer readings:

```dart
const accelerometerChannel = EventChannel('device/accelerometer');
final readings = accelerometerChannel.receiveBroadcastStream();
```

The native side registers a handler using the same channel name. A method handler returns one result; an event handler emits values over time.

## 3. What is the difference between the UI thread and the raster thread?

The UI thread runs Dart code to build a frame, including widget building, layout, and paint-command generation. It produces a layer tree. The raster thread takes that tree and renders the frame using Flutter's graphics backend before presenting it. Expensive work on either thread can delay frames, but each thread has a different responsibility; the raster thread is not simply the GPU.

## 4. What types of trees does Flutter use?

- **Widget tree:** Immutable configuration describing the UI.
- **Element tree:** Persistent runtime instances that connect widgets to rendered output and manage lifecycle and state.
- **Render-object tree:** Handles layout, hit testing, and painting.

Flutter also builds a **layer tree** as part of compositing a frame.

## 5. What is tree shaking?

Tree shaking is a build-time optimization that removes code unreachable from the app's entry points. It helps reduce release-build size; it does not generally remove code just because a runtime branch was not taken.

## 6. What are isolates, and how are they different from `compute`?

An isolate is an independent Dart execution context with its own memory and event loop. Isolates do not share mutable objects; they communicate by passing messages. They are useful for CPU-heavy work that would otherwise block the UI isolate.

`compute` is a convenience API that runs a callback with a message and returns its result. On native platforms it runs the work in another isolate. On web, it runs on the current event loop, so it does not provide the same parallel execution.

```dart
final result = await compute(parseJson, jsonString);
```

Use it for substantial CPU-bound tasks, such as parsing a large JSON document—not for small operations where isolate startup and messaging overhead may outweigh the benefit.
