import 'package:flutter/material.dart';

void main() {
  runApp(const ProductApp());
}

class ProductApp extends StatelessWidget {
  const ProductApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Product Search',
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
  final String category;
  final String price;
  final String emoji;

  const Product({
    required this.name,
    required this.category,
    required this.price,
    required this.emoji,
  });
}

// Main Screen
class ProductHomePage extends StatefulWidget {
  const ProductHomePage({super.key});

  @override
  State<ProductHomePage> createState() =>
      _ProductHomePageState();
}

class _ProductHomePageState extends State<ProductHomePage> {

  final TextEditingController searchController =
      TextEditingController();

  String searchQuery = '';
  String selectedCategory = 'All';

  final List<Product> products = const [
    Product(
      name: 'Laptop',
      category: 'Electronics',
      price: '₹55,999',
      emoji: '💻',
    ),
    Product(
      name: 'Smartphone',
      category: 'Electronics',
      price: '₹19,999',
      emoji: '📱',
    ),
    Product(
      name: 'Headphones',
      category: 'Electronics',
      price: '₹1,499',
      emoji: '🎧',
    ),
    Product(
      name: 'T-Shirt',
      category: 'Clothing',
      price: '₹799',
      emoji: '👕',
    ),
    Product(
      name: 'Shoes',
      category: 'Clothing',
      price: '₹2,499',
      emoji: '👟',
    ),
    Product(
      name: 'Watch',
      category: 'Accessories',
      price: '₹3,999',
      emoji: '⌚',
    ),
    Product(
      name: 'Backpack',
      category: 'Accessories',
      price: '₹1,299',
      emoji: '🎒',
    ),
  ];

  final List<String> categories = [
    'All',
    'Electronics',
    'Clothing',
    'Accessories',
  ];

  // Search and Filter Logic
  List<Product> get filteredProducts {
    return products.where((product) {

      final matchesSearch = product.name
          .toLowerCase()
          .contains(searchQuery.toLowerCase());

      final matchesCategory =
          selectedCategory == 'All' ||
          product.category == selectedCategory;

      return matchesSearch && matchesCategory;

    }).toList();
  }

  @override
  void dispose() {
    searchController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey.shade100,

      appBar: AppBar(
        title: const Text(
          'Product Finder',
          style: TextStyle(
            color: Colors.white,
            fontWeight: FontWeight.bold,
          ),
        ),
        backgroundColor: Colors.deepPurple,
        centerTitle: true,
      ),

      body: Column(
        children: [

          // Search Input
          Padding(
            padding: const EdgeInsets.all(15),
            child: TextField(
              controller: searchController,
              decoration: InputDecoration(
                hintText: 'Search products...',
                prefixIcon: const Icon(Icons.search),
                suffixIcon: IconButton(
                  icon: const Icon(Icons.clear),
                  onPressed: () {
                    searchController.clear();
                    setState(() {
                      searchQuery = '';
                    });
                  },
                ),
                border: OutlineInputBorder(
                  borderRadius: BorderRadius.circular(12),
                ),
                filled: true,
                fillColor: Colors.white,
              ),

              onChanged: (value) {
                setState(() {
                  searchQuery = value;
                });
              },
            ),
          ),

          // Category Filter
          Padding(
            padding: const EdgeInsets.symmetric(
              horizontal: 15,
            ),
            child: DropdownButtonFormField<String>(
              value: selectedCategory,
              decoration: InputDecoration(
                labelText: 'Filter by Category',
                prefixIcon: const Icon(Icons.filter_list),
                border: OutlineInputBorder(
                  borderRadius: BorderRadius.circular(12),
                ),
                filled: true,
                fillColor: Colors.white,
              ),

              items: categories.map((category) {
                return DropdownMenuItem(
                  value: category,
                  child: Text(category),
                );
              }).toList(),

              onChanged: (value) {
                setState(() {
                  selectedCategory = value!;
                });
              },
            ),
          ),

          const SizedBox(height: 15),

          // Product Count
          Padding(
            padding: const EdgeInsets.symmetric(
              horizontal: 18,
            ),
            child: Align(
              alignment: Alignment.centerLeft,
              child: Text(
                'Products Found: ${filteredProducts.length}',
                style: const TextStyle(
                  fontSize: 17,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),
          ),

          const SizedBox(height: 10),

          // Product List
          Expanded(
            child: filteredProducts.isEmpty
                ? const Center(
                    child: Text(
                      'No products found!',
                      style: TextStyle(
                        fontSize: 18,
                        color: Colors.grey,
                      ),
                    ),
                  )
                : ListView.builder(
                    itemCount: filteredProducts.length,
                    itemBuilder: (context, index) {
                      return ProductCard(
                        product: filteredProducts[index],
                      );
                    },
                  ),
          ),
        ],
      ),
    );
  }
}

// Reusable Product Card
class ProductCard extends StatelessWidget {
  final Product product;

  const ProductCard({
    super.key,
    required this.product,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.symmetric(
        horizontal: 15,
        vertical: 7,
      ),
      elevation: 3,

      child: ListTile(
        leading: CircleAvatar(
          backgroundColor: Colors.deepPurple.shade50,
          child: Text(
            product.emoji,
            style: const TextStyle(fontSize: 25),
          ),
        ),

        title: Text(
          product.name,
          style: const TextStyle(
            fontWeight: FontWeight.bold,
          ),
        ),

        subtitle: Text(product.category),

        trailing: Text(
          product.price,
          style: const TextStyle(
            color: Colors.deepPurple,
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    );
  }
}
