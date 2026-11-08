import 'package:flutter/material.dart';

void main() {
  runApp(const EcommerceApp());
}

class EcommerceApp extends StatelessWidget {
  const EcommerceApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'E-Commerce Catalog',
      theme: ThemeData(
        primarySwatch: Colors.deepPurple,
      ),
      home: const ProductHomePage(),
    );
  }
}

// Product Model
class Product {
  final String name;
  final String price;
  final String emoji;
  final double rating;

  const Product({
    required this.name,
    required this.price,
    required this.emoji,
    required this.rating,
  });
}

// Product Home Page
class ProductHomePage extends StatelessWidget {
  const ProductHomePage({super.key});

  final List<Product> products = const [
    Product(
      name: 'Headphones',
      price: '₹1,499',
      emoji: '🎧',
      rating: 4.5,
    ),
    Product(
      name: 'Smart Watch',
      price: '₹2,999',
      emoji: '⌚',
      rating: 4.3,
    ),
    Product(
      name: 'Laptop',
      price: '₹55,999',
      emoji: '💻',
      rating: 4.8,
    ),
    Product(
      name: 'Camera',
      price: '₹24,999',
      emoji: '📷',
      rating: 4.6,
    ),
    Product(
      name: 'Mobile Phone',
      price: '₹19,999',
      emoji: '📱',
      rating: 4.4,
    ),
    Product(
      name: 'Speaker',
      price: '₹2,499',
      emoji: '🔊',
      rating: 4.2,
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey.shade100,

      appBar: AppBar(
        title: const Text(
          'ShopEase',
          style: TextStyle(
            color: Colors.white,
            fontWeight: FontWeight.bold,
          ),
        ),
        backgroundColor: Colors.deepPurple,
        actions: const [
          Icon(Icons.shopping_cart, color: Colors.white),
          SizedBox(width: 20),
        ],
      ),

      body: LayoutBuilder(
        builder: (context, constraints) {

          int columns;

          if (constraints.maxWidth < 600) {
            columns = 2;
          } else if (constraints.maxWidth < 1000) {
            columns = 3;
          } else {
            columns = 4;
          }

          return Column(
            children: [

              // Header
              Container(
                width: double.infinity,
                padding: const EdgeInsets.all(20),
                margin: const EdgeInsets.all(12),
                decoration: BoxDecoration(
                  color: Colors.deepPurple.shade100,
                  borderRadius: BorderRadius.circular(15),
                ),
                child: const Column(
                  children: [
                    Text(
                      'Welcome to ShopEase!',
                      style: TextStyle(
                        fontSize: 22,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                    SizedBox(height: 8),
                    Text('Discover the latest products'),
                  ],
                ),
              ),

              const Padding(
                padding: EdgeInsets.all(12),
                child: Align(
                  alignment: Alignment.centerLeft,
                  child: Text(
                    'Our Products',
                    style: TextStyle(
                      fontSize: 20,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                ),
              ),

              // Responsive Product Grid
              Expanded(
                child: GridView.builder(
                  padding: const EdgeInsets.all(12),
                  itemCount: products.length,

                  gridDelegate:
                      SliverGridDelegateWithFixedCrossAxisCount(
                    crossAxisCount: columns,
                    crossAxisSpacing: 12,
                    mainAxisSpacing: 12,
                    childAspectRatio: 0.85,
                  ),

                  itemBuilder: (context, index) {
                    return ProductCard(
                      product: products[index],
                    );
                  },
                ),
              ),
            ],
          );
        },
      ),
    );
  }
}

// Reusable Product Widget
class ProductCard extends StatelessWidget {
  final Product product;

  const ProductCard({
    super.key,
    required this.product,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      elevation: 4,
      color: Colors.white,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(15),
      ),

      child: Padding(
        padding: const EdgeInsets.all(10),

        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [

            // Product Image
            Expanded(
              child: Container(
                width: double.infinity,
                decoration: BoxDecoration(
                  color: Colors.deepPurple.shade50,
                  borderRadius: BorderRadius.circular(12),
                ),
                child: Center(
                  child: Text(
                    product.emoji,
                    style: const TextStyle(fontSize: 55),
                  ),
                ),
              ),
            ),

            const SizedBox(height: 10),

            // Product Name
            Text(
              product.name,
              textAlign: TextAlign.center,
              style: const TextStyle(
                fontWeight: FontWeight.bold,
                fontSize: 16,
              ),
            ),

            const SizedBox(height: 5),

            // Product Price
            Text(
              product.price,
              style: const TextStyle(
                color: Colors.deepPurple,
                fontWeight: FontWeight.bold,
                fontSize: 15,
              ),
            ),

            const SizedBox(height: 5),

            // Product Rating
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                const Icon(
                  Icons.star,
                  color: Colors.orange,
                  size: 18,
                ),
                Text(
                  product.rating.toString(),
                  style: const TextStyle(fontSize: 13),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
