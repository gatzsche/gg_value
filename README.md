# GgValue - A simple value representation for Dart

GgValue represents a value in the memory of a dart application alongside with
the following features:

- A stream that provides updates on the value.
- A mechanism preventing many updates on multiple changes of the value.
- A custom transform function keeping the value in the desired range.
- A custom compare function, making sure only changes are delivered.

## Usage

```dart
import 'package:gg_value/gg_value.dart';

void main() async {

  // ...........................
  // Get synchronously set value
  var v = GgValue<int>(seed: 5, spam: false);
  print('Sync: ${v.value}');

  // Outputs:
  // Sync: 5

  // ...........................
  // When spam is set to false, stream only delivers last change.
  v.spam = false;
  v.stream.listen((val) => print('Async: $val'));lib
  v.value = 1;
  v.value = 2;
  v.value = 3;
  await Future.delayed(Duration(microseconds: 1));

  // Outputs:
  // Async: 3

  // ...........................
  // When spam is set to true, stream delivers each change.
  v.spam = true;
  v.value = 7;
  v.value = 8;
  v.value = 9;
  await Future.delayed(Duration(microseconds: 1));

  // Outputs:
  // Async: 7
  // Async: 8
  // Async: 9

  // ..................................
  // Check or transform assigned values
  final ensureMaxFive = (int v) => v > 5 ? 5 : v;
  var v2 = GgValue<int>(seed: 0, transform: ensureMaxFive);
  v2.value = 4;
  print('Transformed: ${v2.value}');
  v2.value = 10;
  print('Transformed: ${v2.value}');
  await Future.delayed(Duration(microseconds: 1));

  // Outputs:
  // Transformed: 4
  // Transformed: 5


  // ...............................................
  // Deliver only updates when values have changed.
  // The param 'isEqual' allows specifying an own comparison function.
  final haveSameFirstLetters =
      (String a, String b) => a.substring(0, 1) == b.substring(0, 1);

  var v3 = GgValue<String>(
    seed: 'Karl',
    isEqual: haveSameFirstLetters,
    spam: true,
  );

  final receivedUpdates = [];
  v3.stream.listen((val) => receivedUpdates.add(val));

  v3.value = 'Anna';
  v3.value = 'Arno';
  v3.value = 'Berta';
  v3.value = 'Bernd';

  await Future.delayed(Duration(microseconds: 1));

  print(receivedUpdates.join(', '));

  // Outputs:
  // Anna, Berta
}

```

## Features and bugs

Please file feature requests and bugs at the [GitHub][[tracker](https://github.com/gatzsche/gg_value)].

