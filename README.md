
# Flutter-Provider Authentication Example

This is a basic example of using the **Provider** package for authentication in a Flutter project.

---

## Overview

The example demonstrates:

- **State Management**: Using Provider for managing authentication state.
- **Authentication Flow**: Login, logout, and checking the current user.

---

## Steps

### 1. Add Dependencies

Include the `provider` package in your `pubspec.yaml` file:

```yaml
dependencies:
  flutter:
    sdk: flutter
  provider: ^5.0.0
```

Run `flutter pub get` to install the package.

---

### 2. Create the Authentication Provider

Create a `AuthProvider` class to manage authentication logic.

```dart
import 'package:flutter/material.dart';

class AuthProvider with ChangeNotifier {
  String? _user;

  String? get user => _user;

  bool get isAuthenticated => _user != null;

  // Simulate login
  Future<void> login(String username, String password) async {
    await Future.delayed(const Duration(seconds: 2)); // Simulate API call
    _user = username; // In a real app, use tokens or user info
    notifyListeners();
  }

  // Logout
  void logout() {
    _user = null;
    notifyListeners();
  }
}
```

---

### 3. Wrap Your App with the Provider

In `main.dart`, wrap your app with the `ChangeNotifierProvider`:

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'auth_provider.dart';
import 'home_page.dart';

void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => AuthProvider(),
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: const HomePage(),
    );
  }
}
```

---

### 4. Create Login and Home Screens

#### `LoginPage`:

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'auth_provider.dart';

class LoginPage extends StatelessWidget {
  const LoginPage({super.key});

  @override
  Widget build(BuildContext context) {
    final authProvider = Provider.of<AuthProvider>(context, listen: false);

    return Scaffold(
      appBar: AppBar(title: const Text("Login")),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              decoration: const InputDecoration(labelText: "Username"),
            ),
            TextField(
              decoration: const InputDecoration(labelText: "Password"),
              obscureText: true,
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: () async {
                await authProvider.login("test_user", "password123");
                Navigator.pushReplacementNamed(context, '/home');
              },
              child: const Text("Login"),
            ),
          ],
        ),
      ),
    );
  }
}
```

#### `HomePage`:

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'auth_provider.dart';

class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    final authProvider = Provider.of<AuthProvider>(context);

    return Scaffold(
      appBar: AppBar(
        title: const Text("Home"),
        actions: [
          IconButton(
            onPressed: () {
              authProvider.logout();
              Navigator.pushReplacementNamed(context, '/login');
            },
            icon: const Icon(Icons.logout),
          ),
        ],
      ),
      body: Center(
        child: Text("Welcome, ${authProvider.user ?? "Guest"}!"),
      ),
    );
  }
}
```

---

### 5. Define Routes

In `main.dart`, add routes for navigation:

```dart
return MaterialApp(
  initialRoute: '/login',
  routes: {
    '/login': (context) => const LoginPage(),
    '/home': (context) => const HomePage(),
  },
);
```

---

## Key Features

- **State Management**: Provider listens for changes in `AuthProvider` and updates the UI accordingly.
- **Seamless Navigation**: Login and logout actions are handled with route navigation.
- **Scalable Architecture**: Easily extendable for more complex auth flows like token-based authentication.
