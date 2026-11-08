import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );

  runApp(const SearchApp());
}

// Main Application
class SearchApp extends StatelessWidget {
  const SearchApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Student Search',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: const StudentSearchPage(),
    );
  }
}

// Student Search Screen
class StudentSearchPage extends StatefulWidget {
  const StudentSearchPage({super.key});

  @override
  State<StudentSearchPage> createState() =>
      _StudentSearchPageState();
}

class _StudentSearchPageState
    extends State<StudentSearchPage> {

  final TextEditingController searchController =
      TextEditingController();

  final CollectionReference<Map<String, dynamic>> students =
      FirebaseFirestore.instance.collection('students');

  String searchText = '';
  String searchBy = 'name';

  // Firestore Search Query
  Query<Map<String, dynamic>> get searchQuery {

    String keyword = searchText.trim();

    if (keyword.isEmpty) {
      return students.orderBy('name');
    }

    if (searchBy == 'rollno') {
      return students.where(
        'rollno',
        isEqualTo: keyword,
      );
    }

    return students
        .orderBy('name')
        .startAt([keyword])
        .endAt(['$keyword\uf8ff']);
  }

  @override
  void dispose() {
    searchController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      backgroundColor: Colors.indigo.shade50,

      appBar: AppBar(
        title: const Text(
          'Student Search',
          style: TextStyle(
            color: Colors.white,
            fontWeight: FontWeight.bold,
          ),
        ),
        backgroundColor: Colors.indigo,
        centerTitle: true,
      ),

      body: Column(
        children: [

          const SizedBox(height: 20),

          const Icon(
            Icons.cloud_search,
            size: 75,
            color: Colors.indigo,
          ),

          const SizedBox(height: 10),

          const Text(
            'Search Student Records',
            style: TextStyle(
              fontSize: 21,
              fontWeight: FontWeight.bold,
            ),
          ),

          const SizedBox(height: 20),

          // Search Filter
          Padding(
            padding: const EdgeInsets.symmetric(
              horizontal: 15,
            ),

            child: DropdownButtonFormField<String>(
              value: searchBy,

              decoration: const InputDecoration(
                labelText: 'Search By',
                prefixIcon: Icon(Icons.filter_list),
                border: OutlineInputBorder(),
                filled: true,
                fillColor: Colors.white,
              ),

              items: const [
                DropdownMenuItem(
                  value: 'name',
                  child: Text('Student Name'),
                ),
                DropdownMenuItem(
                  value: 'rollno',
                  child: Text('Roll Number'),
                ),
              ],

              onChanged: (value) {
                setState(() {
                  searchBy = value!;
                });
              },
            ),
          ),

          const SizedBox(height: 15),

          // Search Input
          Padding(
            padding: const EdgeInsets.symmetric(
              horizontal: 15,
            ),

            child: TextField(
              controller: searchController,

              decoration: InputDecoration(
                hintText: searchBy == 'name'
                    ? 'Enter student name...'
                    : 'Enter roll number...',

                prefixIcon: const Icon(Icons.search),

                suffixIcon: IconButton(
                  icon: const Icon(Icons.clear),
                  onPressed: () {
                    searchController.clear();

                    setState(() {
                      searchText = '';
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
                  searchText = value;
                });
              },
            ),
          ),

          const SizedBox(height: 20),

          // Search Results
          const Padding(
            padding: EdgeInsets.symmetric(horizontal: 18),

            child: Align(
              alignment: Alignment.centerLeft,

              child: Text(
                'Search Results',
                style: TextStyle(
                  fontSize: 20,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),
          ),

          const SizedBox(height: 10),

          Expanded(
            child: StreamBuilder<QuerySnapshot<Map<String, dynamic>>>(
              stream: searchQuery.snapshots(),

              builder: (context, snapshot) {

                if (snapshot.hasError) {
                  return const Center(
                    child: Text(
                      'Error retrieving records!',
                    ),
                  );
                }

                if (snapshot.connectionState ==
                    ConnectionState.waiting) {
                  return const Center(
                    child: CircularProgressIndicator(),
                  );
                }

                final records = snapshot.data!.docs;

                if (records.isEmpty) {
                  return const Center(
                    child: Text(
                      'No matching student records found!',
                      style: TextStyle(
                        fontSize: 16,
                        color: Colors.grey,
                      ),
                    ),
                  );
                }

                return ListView.builder(
                  itemCount: records.length,

                  itemBuilder: (context, index) {

                    final data = records[index].data();

                    return Card(
                      margin: const EdgeInsets.symmetric(
                        horizontal: 15,
                        vertical: 7,
                      ),

                      elevation: 3,

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
                      ),
                    );
                  },
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
