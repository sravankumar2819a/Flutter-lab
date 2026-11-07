# flutterlabexp1
import 'package:flutter/material.dart';

void main() {
  runApp(const FruitApp());
}

class FruitApp extends StatelessWidget {
  const FruitApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Fruit List App',
      theme: ThemeData(
        primarySwatch: Colors.green,
      ),
      home: const FruitHomePage(),
    );
  }
}

// Fruit data model
class Fruit {
  final String name;
  final String price;
  final String emoji;
  final String description;

  const Fruit({
    required this.name,
    required this.price,
    required this.emoji,
    required this.description,
  });
}

// Main screen
class FruitHomePage extends StatelessWidget {
  const FruitHomePage({super.key});

  final List<Fruit> fruits = const [
    Fruit(
      name: 'Apple',
      price: '₹120 / kg',
      emoji: '🍎',
      description: 'Fresh and juicy red apples',
    ),
    Fruit(
      name: 'Banana',
      price: '₹50 / dozen',
      emoji: '🍌',
      description: 'Sweet and nutritious bananas',
    ),
    Fruit(
      name: 'Orange',
      price: '₹80 / kg',
      emoji: '🍊',
      description: 'Rich in Vitamin C',
    ),
    Fruit(
      name: 'Grapes',
      price: '₹100 / kg',
      emoji: '🍇',
      description: 'Fresh and seedless grapes',
    ),
    Fruit(
      name: 'Watermelon',
      price: '₹40 / kg',
      emoji: '🍉',
      description: 'Refreshing summer fruit',
    ),
    Fruit(
      name: 'Mango',
      price: '₹150 / kg',
      emoji: '🥭',
      description: 'Delicious king of fruits',
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.green.shade50,
      appBar: AppBar(
        title: const Text(
          'Fresh Fruits',
          style: TextStyle(
            fontWeight: FontWeight.bold,
            color: Colors.white,
          ),
        ),
        centerTitle: true,
        backgroundColor: Colors.green,
      ),

      body: Column(
        children: [
          // Header container
          Container(
            width: double.infinity,
            padding: const EdgeInsets.all(20),
            margin: const EdgeInsets.all(12),
            decoration: BoxDecoration(
              color: Colors.green.shade100,
              borderRadius: BorderRadius.circular(15),
            ),
            child: const Column(
              children: [
                Text(
                  'Welcome to Fruit Store!',
                  style: TextStyle(
                    fontSize: 22,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                SizedBox(height: 8),
                Text('Explore our fresh and healthy fruits'),
              ],
            ),
          ),

          // Fruit list
          Expanded(
            child: ListView.builder(
              itemCount: fruits.length,
              itemBuilder: (context, index) {
                return FruitCard(fruit: fruits[index]);
              },
            ),
          ),
        ],
      ),
    );
  }
}

// Reusable custom widget
class FruitCard extends StatelessWidget {
  final Fruit fruit;

  const FruitCard({
    super.key,
    required this.fruit,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.symmetric(
        horizontal: 15,
        vertical: 8,
      ),
      elevation: 4,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(15),
      ),
      child: Padding(
        padding: const EdgeInsets.all(15),

        child: Row(
          children: [
            // Fruit emoji container
            Container(
              width: 65,
              height: 65,
              alignment: Alignment.center,
              decoration: BoxDecoration(
                color: Colors.green.shade50,
                borderRadius: BorderRadius.circular(12),
              ),
              child: Text(
                fruit.emoji,
                style: const TextStyle(fontSize: 35),
              ),
            ),

            const SizedBox(width: 15),

            // Fruit details
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    fruit.name,
                    style: const TextStyle(
                      fontSize: 18,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  const SizedBox(height: 5),
                  Text(
                    fruit.description,
                    style: TextStyle(
                      color: Colors.grey.shade600,
                      fontSize: 13,
                    ),
                  ),
                  const SizedBox(height: 5),
                  Text(
                    fruit.price,
                    style: const TextStyle(
                      color: Colors.green,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                ],
              ),
            ),

            const Icon(
              Icons.arrow_forward_ios,
              color: Colors.green,
              size: 18,
            ),
          ],
        ),
      ),
    );
  }
}
