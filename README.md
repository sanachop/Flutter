# Flutter
import 'dart:convert';
import 'dart:io';
import 'package:http/http.dart' as http;

void main() async {
  List<Map<String, dynamic>> users = [];

  try {
    users = await getUsers();
  } catch (e) {
    print('Failed to load users: $e');
    return; // Exit early if fetching users fails
  }

  while (true) {
    print('\n==== User Manager Menu ====');
    print('1. Show all usernames');
    print('2. Show user details by ID');
    print('3. Filter users by city');
    print('4. Exit');
    stdout.write('Enter your choice: ');
    String? choice = stdin.readLineSync();

    if (choice == '1') {
      showUsernames(users);
    } else if (choice == '2') {
      showUserById(users);
    } else if (choice == '3') {
      filterUsersByCity(users);
    } else if (choice == '4') {
      print('Goodbye!');
      break;
    } else {
      print('Invalid choice. Try again.');
    }
  }
}

Future<List<Map<String, dynamic>>> getUsers() async {
  var url = Uri.parse('https://jsonplaceholder.typicode.com/users');
  var response = await http.get(url);
  var data = jsonDecode(response.body);
  return List<Map<String, dynamic>>.from(data);
}

void showUsernames(List<Map<String, dynamic>> users) {
  print('\nUsernames:');
  for (var user in users) {
    print('- ${user['username']}');
  }
}

void showUserById(List<Map<String, dynamic>> users) {
  stdout.write('Enter user ID: ');
  String? input = stdin.readLineSync();
  int? id = int.tryParse(input ?? '');

  if (id == null) {
    print('Invalid ID.');
    return;
  }

  var user = users.firstWhere((u) => u['id'] == id, orElse: () => {});

  if (user.isEmpty) {
    print('User not found.');
    return;
  }

  print('\nUser Details:');
  print('Name    : ${user['name']}');
  print('Username: ${user['username']}');
  print('Email   : ${user['email']}');
  print('City    : ${user['address']['city']}');
  print('Company : ${user['company']['name']}');
}

void filterUsersByCity(List<Map<String, dynamic>> users) {
  stdout.write('Enter city name: ');
  String? city = stdin.readLineSync();

  if (city == null || city.isEmpty) {
    print('City name cannot be empty.');
    return;
  }

  var filtered = users.where((user) =>
      user['address']['city'].toString().toLowerCase() == city.toLowerCase());

  if (filtered.isEmpty) {
    print('No users found in this city.');
  } else {
    print('\nUsers in $city:');
    for (var user in filtered) {
      print('- ${user['name']} (${user['username']})');
    }
  }
}
