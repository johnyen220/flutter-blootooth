# flutter-bluetooth

A simple Flutter Bluetooth example using [`flutter_blue_plus`](https://pub.dev/packages/flutter_blue_plus).

## 1) Add dependency

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_blue_plus: ^1.35.5
```

## 2) Minimal example

```dart
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:flutter_blue_plus/flutter_blue_plus.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: BluetoothExamplePage(),
    );
  }
}

class BluetoothExamplePage extends StatefulWidget {
  const BluetoothExamplePage({super.key});

  @override
  State<BluetoothExamplePage> createState() => _BluetoothExamplePageState();
}

class _BluetoothExamplePageState extends State<BluetoothExamplePage> {
  StreamSubscription<List<ScanResult>>? _scanSubscription;
  final List<ScanResult> _results = [];
  bool _isScanning = false;

  @override
  void dispose() {
    _scanSubscription?.cancel();
    super.dispose();
  }

  Future<void> _startScan() async {
    if (_isScanning) return;

    setState(() {
      _results.clear();
      _isScanning = true;
    });

    _scanSubscription = FlutterBluePlus.scanResults.listen((results) {
      setState(() {
        _results
          ..clear()
          ..addAll(results);
      });
    });

    await FlutterBluePlus.startScan(timeout: const Duration(seconds: 5));
    await FlutterBluePlus.isScanning.where((v) => v == false).first;

    if (mounted) {
      setState(() {
        _isScanning = false;
      });
    }
  }

  Future<void> _stopScan() async {
    await FlutterBluePlus.stopScan();
    if (mounted) {
      setState(() {
        _isScanning = false;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Flutter Bluetooth Example')),
      body: Column(
        children: [
          const SizedBox(height: 12),
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              ElevatedButton(
                onPressed: _isScanning ? null : _startScan,
                child: const Text('Start scan'),
              ),
              const SizedBox(width: 12),
              ElevatedButton(
                onPressed: _isScanning ? _stopScan : null,
                child: const Text('Stop scan'),
              ),
            ],
          ),
          const SizedBox(height: 12),
          Expanded(
            child: ListView.builder(
              itemCount: _results.length,
              itemBuilder: (context, index) {
                final r = _results[index];
                final name = r.device.platformName.isEmpty
                    ? '(unknown)'
                    : r.device.platformName;
                return ListTile(
                  title: Text(name),
                  subtitle: Text(r.device.remoteId.str),
                  trailing: Text('RSSI: ${r.rssi}'),
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
```

## 3) Platform permissions

Bluetooth permissions differ by OS version.

### Android (`android/app/src/main/AndroidManifest.xml`)

```xml
<!-- Android 12+ -->
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<!-- Needed unless BLUETOOTH_SCAN is declared with android:usesPermissionFlags="neverForLocation" -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<!-- Android 11 and below -->
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### iOS (`ios/Runner/Info.plist`)

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string>This app needs Bluetooth to scan and connect to devices.</string>
<!-- Deprecated in iOS 13; required for iOS 12 and below -->
<key>NSBluetoothPeripheralUsageDescription</key>
<string>This app needs Bluetooth to scan and connect to devices.</string>
```