# iso_countries_example

Demonstrates how to use the iso_countries plugin.

```
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  List<Country> countryList;
  @override
  void initState() {
    super.initState();
    prepareDefaultCountries();
  }

  // Platform messages are asynchronous, so we initialize in an async method.
  Future<void> prepareDefaultCountries() async {
    List<Country> countries;
    // Platform messages may fail, so we use a try/catch PlatformException.
    try {
      countries = await IsoCountries.iso_countries;
    } on PlatformException {
      countries = null;
    }
    // If the widget was removed from the tree while the asynchronous platform
    // message was in flight, we want to discard the reply rather than calling
    // setState to update our non-existent appearance.
    if (!mounted) return;

    setState(() {
      countryList = countries;
    });
  }

  // Platform messages are asynchronous, so we initialize in an async method.
  Future<void> prepareLocaleSpecificCountries() async {
    List<Country> countries;
    // Platform messages may fail, so we use a try/catch PlatformException.
    try {
      // If you need country names in a specific language please pass language code sample
      // fr-fr, en-en, de-de... IMPORTANT: In Android there seem to be some issue with case
      // so passing fr-FR wont work
      countries = await IsoCountries.iso_countries_for_locale("fr-fr");
    } on PlatformException {
      countries = null;
    }
    // If the widget was removed from the tree while the asynchronous platform
    // message was in flight, we want to discard the reply rather than calling
    // setState to update our non-existent appearance.
    if (!mounted) return;

    setState(() {
      countryList = countries;
    });
  }
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          actions: <Widget>[
            FlatButton(
              textColor: Colors.white,
              onPressed: prepareLocaleSpecificCountries,
              child: Text("fr-fr"),
              shape: CircleBorder(side: BorderSide(color: Colors.transparent)),
            ),
            FlatButton(
              textColor: Colors.white,
              onPressed: prepareDefaultCountries,
              child: Text("Default"),
              shape: CircleBorder(side: BorderSide(color: Colors.transparent)),
            ),
          ],
          title: const Text('Plugin example app'),
        ),
        body: _buildListOfCountries()
      ),
    );
  }

  Widget _buildListOfCountries(){

    return ListView.builder(itemBuilder: (BuildContext context, int index){
      final Country country = countryList[index];
      return ListTile(
        title: Text(country.name),
        subtitle: Text(country.countryCode),

      );
    },
    itemCount: countryList != null ? countryList.length : 0,);
  }
}

```
**New API Added**

Passing in a country code( 2 letter) and an optional localeIdentifier( eg 'de-de', 'fr-fr'), you can get a country object with a translated name.

```
  // Platform messages are asynchronous, so we initialize in an async method.
  // IMPORTANT: Make sure the country code passed in is valid, in Android passing
  // in a wrong country code, returns the country name as passed in country code not sure why.
  Future<void> getCountryForCodeWithIdentifier(
      String code, String localeIdentifier) async {
    // Platform messages may fail, so we use a try/catch PlatformException.
    try {
      country = await IsoCountries.iso_country_for_code_for_locale(code,
          locale_identifier: localeIdentifier);
    } on PlatformException {
      country = null;
    }
    // If the widget was removed from the tree while the asynchronous platform
    // message was in flight, we want to discard the reply rather than calling
    // setState to update our non-existent appearance.
    if (!mounted) return;

    setState(() {
      print(country.name);
    });
  }
```

