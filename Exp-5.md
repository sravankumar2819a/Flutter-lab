import 'package:flutter/material.dart';

void main() {
  runApp(const BudgetApp());
}

class BudgetApp extends StatelessWidget {
  const BudgetApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Personal Budget Calculator',
      theme: ThemeData(
        primarySwatch: Colors.teal,
      ),
      home: const BudgetHomePage(),
    );
  }
}

class BudgetHomePage extends StatefulWidget {
  const BudgetHomePage({super.key});

  @override
  State<BudgetHomePage> createState() =>
      _BudgetHomePageState();
}

class _BudgetHomePageState extends State<BudgetHomePage> {

  final TextEditingController incomeController =
      TextEditingController();

  final TextEditingController foodController =
      TextEditingController();

  final TextEditingController travelController =
      TextEditingController();

  final TextEditingController shoppingController =
      TextEditingController();

  double income = 0;
  double totalExpenses = 0;
  double balance = 0;

  String financialStatus = 'Enter your budget details';

  Color statusColor = Colors.blue;

  // Calculate Budget
  void calculateBudget() {

    double? enteredIncome =
        double.tryParse(incomeController.text);

    double? food =
        double.tryParse(foodController.text);

    double? travel =
        double.tryParse(travelController.text);

    double? shopping =
        double.tryParse(shoppingController.text);

    if (enteredIncome == null ||
        food == null ||
        travel == null ||
        shopping == null ||
        enteredIncome < 0 ||
        food < 0 ||
        travel < 0 ||
        shopping < 0) {

      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Please enter valid non-negative values'),
        ),
      );

      return;
    }

    setState(() {

      income = enteredIncome;

      totalExpenses = food + travel + shopping;

      balance = income - totalExpenses;

      if (balance > 0) {
        financialStatus = 'Good Financial Status';
        statusColor = Colors.green;
      } else if (balance == 0) {
        financialStatus = 'Budget Balanced';
        statusColor = Colors.orange;
      } else {
        financialStatus = 'Overspending';
        statusColor = Colors.red;
      }

    });
  }

  // Input Field Widget
  Widget buildInputField(
      String label,
      IconData icon,
      TextEditingController controller) {

    return Padding(
      padding: const EdgeInsets.symmetric(vertical: 8),

      child: TextField(
        controller: controller,
        keyboardType: const TextInputType.numberWithOptions(
          decimal: true,
        ),

        decoration: InputDecoration(
          labelText: label,
          prefixIcon: Icon(icon),
          border: OutlineInputBorder(
            borderRadius: BorderRadius.circular(12),
          ),
          filled: true,
          fillColor: Colors.white,
        ),
      ),
    );
  }

  @override
  void dispose() {
    incomeController.dispose();
    foodController.dispose();
    travelController.dispose();
    shoppingController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      backgroundColor: Colors.teal.shade50,

      appBar: AppBar(
        title: const Text(
          'Personal Budget Calculator',
          style: TextStyle(
            color: Colors.white,
            fontWeight: FontWeight.bold,
          ),
        ),
        backgroundColor: Colors.teal,
        centerTitle: true,
      ),

      body: SingleChildScrollView(
        padding: const EdgeInsets.all(18),

        child: Column(
          children: [

            const Icon(
              Icons.account_balance_wallet,
              size: 75,
              color: Colors.teal,
            ),

            const SizedBox(height: 10),

            const Text(
              'Manage Your Monthly Budget',
              style: TextStyle(
                fontSize: 21,
                fontWeight: FontWeight.bold,
              ),
            ),

            const SizedBox(height: 20),

            // Income Section
            buildInputField(
              'Monthly Income (₹)',
              Icons.currency_rupee,
              incomeController,
            ),

            // Expense Section
            buildInputField(
              'Food Expenses (₹)',
              Icons.restaurant,
              foodController,
            ),

            buildInputField(
              'Travel Expenses (₹)',
              Icons.directions_bus,
              travelController,
            ),

            buildInputField(
              'Shopping Expenses (₹)',
              Icons.shopping_bag,
              shoppingController,
            ),

            const SizedBox(height: 15),

            // Calculate Button
            SizedBox(
              width: double.infinity,
              height: 50,

              child: ElevatedButton(
                onPressed: calculateBudget,

                style: ElevatedButton.styleFrom(
                  backgroundColor: Colors.teal,
                  foregroundColor: Colors.white,
                ),

                child: const Text(
                  'Calculate Budget',
                  style: TextStyle(
                    fontSize: 17,
                    fontWeight: FontWeight.bold,
                  ),
                ),
              ),
            ),

            const SizedBox(height: 25),

            // Result Section
            Container(
              width: double.infinity,
              padding: const EdgeInsets.all(20),

              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(15),
                boxShadow: const [
                  BoxShadow(
                    color: Colors.black12,
                    blurRadius: 5,
                  ),
                ],
              ),

              child: Column(
                children: [

                  const Text(
                    'Financial Summary',
                    style: TextStyle(
                      fontSize: 20,
                      fontWeight: FontWeight.bold,
                    ),
                  ),

                  const SizedBox(height: 15),

                  Text(
                    'Income: ₹${income.toStringAsFixed(2)}',
                    style: const TextStyle(fontSize: 16),
                  ),

                  const SizedBox(height: 10),

                  Text(
                    'Total Expenses: ₹${totalExpenses.toStringAsFixed(2)}',
                    style: const TextStyle(fontSize: 16),
                  ),

                  const Divider(height: 25),

                  Text(
                    'Remaining Balance: ₹${balance.toStringAsFixed(2)}',
                    style: TextStyle(
                      fontSize: 19,
                      fontWeight: FontWeight.bold,
                      color: balance >= 0
                          ? Colors.green
                          : Colors.red,
                    ),
                    textAlign: TextAlign.center,
                  ),

                  const SizedBox(height: 15),

                  Container(
                    padding: const EdgeInsets.all(12),

                    decoration: BoxDecoration(
                      color: statusColor.withOpacity(0.12),
                      borderRadius: BorderRadius.circular(10),
                    ),

                    child: Text(
                      financialStatus,
                      style: TextStyle(
                        color: statusColor,
                        fontWeight: FontWeight.bold,
                        fontSize: 17,
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
