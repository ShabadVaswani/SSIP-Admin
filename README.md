
# Jan Seva Kendra Awarness App

An Awarness app that can help spread information about different services provided by Goverment Initiative called Jan-Seva-Kendra.



## Documentation

[SRS Document](https://drive.google.com/drive/folders/1KMYtJWDBf2umOZ9JUdwTJx_xKW9lYqXI?usp=sharing)


## Tech Stack

**Languages:** Dart, Flutter, Angular JS, TailwindCSS

**Database:** Firebase Firestore


## Features

- Available in Native Gujarati language
- Information can be updated in Real-time.
- Accessible User Interface
- Cross platform
- Provides Nearby Jan Seva Kendra Location


## Used By

This project can be used by :

- Mamlatdar Offices in Gujarat.
- People that can read and understand Gujarati Language.


---------------------------Rough-----------------------
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flutter/material.dart';
import 'package:font_awesome_flutter/font_awesome_flutter.dart';

import 'documentInfo.dart';

List<String> services = [
  "Certificates",
  "Magisterial",
  "Miscellaneous",
  "Revenue",
  "RTI",
  "Social Security",
  "Supply"
];
List<String> subServices = [];

class SearchDocuments extends StatefulWidget {
  const SearchDocuments({super.key});

  @override
  State<SearchDocuments> createState() => _SearchDocumentsState();
}

class _SearchDocumentsState extends State<SearchDocuments> {
  TextEditingController _searchController = new TextEditingController();
  bool _isSuffixIconVisible = false;
  List _resultsList = [];

  @override
  void initState() {
    // TODO: implement initState
    for (int i = 0; i < services.length; i++) {
      getListitems(services[i]);
      print("Called");
    }
    _resultsList = subServices;
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Search Documents Here"),
      ),
      body: SingleChildScrollView(
        child: Container(
          width: double.infinity,
          child: Column(
            children: [
              Padding(
                padding: const EdgeInsets.all(10.0),
                child: TextField(
                  controller: _searchController,
                  onChanged: (value) {
                    setState(() {
                      _isSuffixIconVisible = value.isNotEmpty;
                    });
                    searchResultsList();
                  },
                  decoration: InputDecoration(
                    filled: true,
                    fillColor: Colors.white,
                    hintText: 'Search',
                    hintStyle: TextStyle(
                      color: Colors.grey[800],
                      fontSize: 16,
                    ),
                    border: OutlineInputBorder(
                      borderRadius: BorderRadius.all(Radius.circular(10.0)),
                      borderSide: BorderSide(width: 0, style: BorderStyle.none),
                    ),
                    contentPadding:
                        EdgeInsets.symmetric(vertical: 20, horizontal: 20),
                    prefixIcon: Icon(
                      Icons.search,
                      color: Colors.grey[800],
                    ),
                    suffixIcon: AnimatedOpacity(
                      duration: Duration(milliseconds: 300),
                      opacity: _isSuffixIconVisible ? 1.0 : 0.0,
                      child: IconButton(
                        icon: Icon(Icons.clear),
                        onPressed: () {
                          setState(() {
                            _searchController.clear();
                          });
                        },
                      ),
                    ),
                  ),
                ),
              ),
              SingleChildScrollView(
                child: Container(
                  height: MediaQuery.of(context).size.height * 0.75,
                  child: ListView.builder(
                      itemCount: _resultsList.length,
                      itemBuilder: (BuildContext context, int index) {
                        return Padding(
                          padding: const EdgeInsets.only(
                              left: 8.0, right: 8.0, top: 5, bottom: 5),
                          child: GestureDetector(
                            onTap: () {
                              Navigator.push(
                                  context,
                                  MaterialPageRoute(
                                      builder: (context) => DocumentInfo(cName: widget.cName,
                                          docName: subServices[index])));
                            },
                            child: Card(
                              elevation: 10,
                              child: Container(
                                //height: 50,
                                margin: EdgeInsets.all(10),
                                child: Column(
                                  mainAxisAlignment: MainAxisAlignment.center,
                                  children: [
                                    Text(
                                      _resultsList[index],
                                      style: TextStyle(fontSize: 22.0),
                                    ),
                                  ],
                                ),
                              ),
                            ),
                          ),
                        );
                      }),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }

  searchResultsList() {
    var showResults = [];

    if (_searchController.text != "") {
      for (var subService in subServices) {
        var title = subService.toLowerCase();

        if (title.contains(_searchController.text.toLowerCase())) {
          showResults.add(subService);
        }
      }
    } else {
      showResults = List.from(subServices);
    }
    setState(() {
      _resultsList = showResults;
    });
  }

  Future<void> getListitems(String value) async {
    // Get docs from collection reference
    QuerySnapshot querySnapshot =
        await FirebaseFirestore.instance.collection(value).get();
    querySnapshot.docs
        .map((doc) => setState(() {
              subServices.add(doc.id.toString());
            }))
        .toList();
    print("Get List Items Called.");
  }
}
 