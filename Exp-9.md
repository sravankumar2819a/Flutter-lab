import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );

  runApp(const StudentApp());
}

// Main Application
class StudentApp extends StatelessWidget {
  const StudentApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Update Student Records',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: const StudentUpdatePage(),
    );
  }
}

// Student Update Screen
class StudentUpdatePage extends StatefulWidget {
  const StudentUpdatePage({super.key});

  @override
  State<StudentUpdatePage> createState() =>
      _StudentUpdatePageState();
}

class _StudentUpdatePageState
    extends State<StudentUpdatePage> {

  final nameController = TextEditingController();
  final rollController = TextEditingController();
  final departmentController = TextEditingController();

  final formKey = GlobalKey<FormState>();

  final CollectionReference<Map<String, dynamic>> students =
      FirebaseFirestore.instance.collection('students');

  String? selectedDocumentId;

  bool isUpdating = false;

  // Select Existing Student
  void selectStudent(
      String documentId,
      Map<String, dynamic> data) {

    setState(() {
      selectedDocumentId = documentId;

      nameController.text = data['name'] ?? '';
      rollController.text = data['rollno'] ?? '';
      departmentController.text = data['department'] ?? '';
    });
  }

  // UPDATE Operation
  Future<void> updateStudent() async {

    if (!formKey.currentState!.validate()) {
      return;
    }

    if (selectedDocumentId == null) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Please select a student to update'),
        ),
      );
      return;
    }

    setState(() {
      isUpdating = true;
    });

    try {

      await students.doc(selectedDocumentId).update({
        'name': nameController.text.trim(),
        'rollno': rollController.text.trim(),
        'department': departmentController.text.trim(),
      });

      if (!mounted) return;

      setState(() {
        selectedDocumentId = null;
        isUpdating = false;
      });

      nameController.clear();
      rollController.clear();
      departmentController.clear();

      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Student updated successfully!'),
        ),
      );

    } catch (e) {

      if (!mounted) return;

      setState(() {
        isUpdating = false;
      });

      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text('Update failed: $e'),
        ),
      );
    }
  }

  // Cancel Editing
  void clearForm() {
    setState(() {
      selectedDocumentId = null;
    });

    nameController.clear();
    rollController.clear();
    departmentController.clear();
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
          'Update Student Records',
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
              Icons.edit_note,
              size: 75,
              color: Colors.indigo,
            ),

            const SizedBox(height: 10),

            const Text(
              'Student Information Management',
              style: TextStyle(
                fontSize: 20,
                fontWeight: FontWeight.bold,
              ),
              textAlign: TextAlign.center,
            ),

            const SizedBox(height: 20),

            // Update Form
            Form(
              key: formKey,

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
                      onPressed: isUpdating
                          ? null
                          : updateStudent,

                      icon: const Icon(Icons.update),

                      label: Text(
                        isUpdating
                            ? 'Updating...'
                            : 'Update Student',
                      ),

                      style: ElevatedButton.styleFrom(
                        backgroundColor: Colors.indigo,
                        foregroundColor: Colors.white,
                      ),
                    ),
                  ),

                  if (selectedDocumentId != null)
                    TextButton(
                      onPressed: clearForm,
                      child: const Text('Cancel Editing'),
                    ),
                ],
              ),
            ),

            const SizedBox(height: 25),

            // Existing Student Records
            const Align(
              alignment: Alignment.centerLeft,
              child: Text(
                'Existing Student Records',
                style: TextStyle(
                  fontSize: 20,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),

            const SizedBox(height: 10),

            // Retrieve Records from Firestore
            StreamBuilder<QuerySnapshot<Map<String, dynamic>>>(
              stream: students.snapshots(),

              builder: (context, snapshot) {

                if (snapshot.hasError) {
                  return const Text(
                    'Error loading student records',
                  );
                }

                if (snapshot.connectionState ==
                    ConnectionState.waiting) {
                  return const CircularProgressIndicator();
                }

                final records = snapshot.data!.docs;

                if (records.isEmpty) {
                  return const Padding(
                    padding: EdgeInsets.all(20),
                    child: Text(
                      'No student records found.',
                    ),
                  );
                }

                return ListView.builder(
                  shrinkWrap: true,
                  physics:
                      const NeverScrollableScrollPhysics(),

                  itemCount: records.length,

                  itemBuilder: (context, index) {

                    final document = records[index];
                    final data = document.data();

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
                          data['name'] ?? '',
                          style: const TextStyle(
                            fontWeight: FontWeight.bold,
                          ),
                        ),

                        subtitle: Text(
                          'Roll No: ${data['rollno']}\n'
                          'Department: ${data['department']}',
                        ),

                        isThreeLine: true,

                        trailing: IconButton(
                          icon: const Icon(
                            Icons.edit,
                            color: Colors.indigo,
                          ),

                          onPressed: () {
                            selectStudent(
                              document.id,
                              data,
                            );
                          },
                        ),
                      ),
                    );
                  },
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
