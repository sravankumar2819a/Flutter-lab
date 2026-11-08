import 'package:flutter/material.dart';
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

void main() {
  runApp(const StudentApp());
}

// Main Application
class StudentApp extends StatelessWidget {
  const StudentApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Student Management',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: const StudentHomePage(),
    );
  }
}

// Database Helper Class
class DatabaseHelper {
  static final DatabaseHelper instance = DatabaseHelper._init();

  static Database? _database;

  DatabaseHelper._init();

  Future<Database> get database async {
    if (_database != null) return _database!;

    _database = await _initDB();
    return _database!;
  }

  Future<Database> _initDB() async {
    String path = join(
      await getDatabasesPath(),
      'students.db',
    );

    return await openDatabase(
      path,
      version: 1,
      onCreate: (db, version) async {
        await db.execute('''
          CREATE TABLE students (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            rollno TEXT NOT NULL,
            department TEXT NOT NULL
          )
        ''');
      },
    );
  }

  // CREATE Operation
  Future<int> insertStudent(
      Map<String, dynamic> student) async {
    Database db = await instance.database;

    return await db.insert(
      'students',
      student,
    );
  }

  // READ Operation
  Future<List<Map<String, dynamic>>> getStudents() async {
    Database db = await instance.database;

    return await db.query(
      'students',
      orderBy: 'id DESC',
    );
  }
}

// Student Home Page
class StudentHomePage extends StatefulWidget {
  const StudentHomePage({super.key});

  @override
  State<StudentHomePage> createState() =>
      _StudentHomePageState();
}

class _StudentHomePageState extends State<StudentHomePage> {

  final nameController = TextEditingController();
  final rollController = TextEditingController();
  final departmentController = TextEditingController();

  List<Map<String, dynamic>> students = [];

  final _formKey = GlobalKey<FormState>();

  @override
  void initState() {
    super.initState();
    loadStudents();
  }

  // Retrieve Student Records
  Future<void> loadStudents() async {
    final data = await DatabaseHelper.instance.getStudents();

    if (!mounted) return;

    setState(() {
      students = data;
    });
  }

  // Insert Student Record
  Future<void> addStudent() async {
    if (!_formKey.currentState!.validate()) {
      return;
    }

    await DatabaseHelper.instance.insertStudent({
      'name': nameController.text.trim(),
      'rollno': rollController.text.trim(),
      'department': departmentController.text.trim(),
    });

    nameController.clear();
    rollController.clear();
    departmentController.clear();

    await loadStudents();

    if (!mounted) return;

    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(
        content: Text('Student added successfully!'),
      ),
    );
  }

  @override
  void dispose() {
    nameController.dispose();
    rollController.dispose();
    departmentController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.indigo.shade50,

      appBar: AppBar(
        title: const Text(
          'Student Records',
          style: TextStyle(
            color: Colors.white,
            fontWeight: FontWeight.bold,
          ),
        ),
        backgroundColor: Colors.indigo,
        centerTitle: true,
      ),

      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16),

        child: Column(
          children: [

            const Icon(
              Icons.school,
              size: 75,
              color: Colors.indigo,
            ),

            const SizedBox(height: 10),

            const Text(
              'Student Management System',
              style: TextStyle(
                fontSize: 21,
                fontWeight: FontWeight.bold,
              ),
            ),

            const SizedBox(height: 20),

            // Student Input Form
            Form(
              key: _formKey,

              child: Column(
                children: [

                  TextFormField(
                    controller: nameController,
                    decoration: const InputDecoration(
                      labelText: 'Student Name',
                      prefixIcon: Icon(Icons.person),
                      border: OutlineInputBorder(),
                      filled: true,
                      fillColor: Colors.white,
                    ),
                    validator: (value) {
                      if (value == null ||
                          value.trim().isEmpty) {
                        return 'Enter student name';
                      }
                      return null;
                    },
                  ),

                  const SizedBox(height: 12),

                  TextFormField(
                    controller: rollController,
                    decoration: const InputDecoration(
                      labelText: 'Roll Number',
                      prefixIcon: Icon(Icons.numbers),
                      border: OutlineInputBorder(),
                      filled: true,
                      fillColor: Colors.white,
                    ),
                    validator: (value) {
                      if (value == null ||
                          value.trim().isEmpty) {
                        return 'Enter roll number';
                      }
                      return null;
                    },
                  ),

                  const SizedBox(height: 12),

                  TextFormField(
                    controller: departmentController,
                    decoration: const InputDecoration(
                      labelText: 'Department',
                      prefixIcon: Icon(Icons.business),
                      border: OutlineInputBorder(),
                      filled: true,
                      fillColor: Colors.white,
                    ),
                    validator: (value) {
                      if (value == null ||
                          value.trim().isEmpty) {
                        return 'Enter department';
                      }
                      return null;
                    },
                  ),

                  const SizedBox(height: 18),

                  SizedBox(
                    width: double.infinity,
                    height: 48,

                    child: ElevatedButton.icon(
                      onPressed: addStudent,
                      icon: const Icon(Icons.add),
                      label: const Text(
                        'Add Student',
                        style: TextStyle(fontSize: 16),
                      ),
                      style: ElevatedButton.styleFrom(
                        backgroundColor: Colors.indigo,
                        foregroundColor: Colors.white,
                      ),
                    ),
                  ),
                ],
              ),
            ),

            const SizedBox(height: 25),

            // Student Records
            const Align(
              alignment: Alignment.centerLeft,
              child: Text(
                'Stored Student Records',
                style: TextStyle(
                  fontSize: 20,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),

            const SizedBox(height: 10),

            students.isEmpty
                ? const Padding(
                    padding: EdgeInsets.all(25),
                    child: Text(
                      'No student records found.',
                      style: TextStyle(
                        fontSize: 16,
                        color: Colors.grey,
                      ),
                    ),
                  )

                : ListView.builder(
                    shrinkWrap: true,
                    physics:
                        const NeverScrollableScrollPhysics(),
                    itemCount: students.length,

                    itemBuilder: (context, index) {
                      final student = students[index];

                      return Card(
                        elevation: 3,
                        margin: const EdgeInsets.symmetric(
                          vertical: 7,
                        ),

                        child: ListTile(
                          leading: CircleAvatar(
                            backgroundColor:
                                Colors.indigo.shade100,
                            child: const Icon(
                              Icons.person,
                              color: Colors.indigo,
                            ),
                          ),

                          title: Text(
                            student['name'],
                            style: const TextStyle(
                              fontWeight: FontWeight.bold,
                            ),
                          ),

                          subtitle: Text(
                            'Roll No: ${student['rollno']}\n'
                            'Department: ${student['department']}',
                          ),

                          isThreeLine: true,
                        ),
                      );
                    },
                  ),
          ],
        ),
      ),
    );
  }
}
