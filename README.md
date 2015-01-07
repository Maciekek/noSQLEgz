#Zadanie 3

##Opis
###Przygotować funkcje map i reduce, które:

    1) wyszukają wszystkie anagramy w pliku word_list.txt
    2) wyszukają najczęściej występujące słowa z Wikipedia data PL aktualny plik z artykułami, ok. 1.3 GB

---


##1 
###Funkcje mapReduce

####MapFunction:
```js
mapFunction = function() {
	var splitedWords = this.author.split("").sort().join("");
	emit(splitedWords, this.author);
};
```

####ReduceFunction
```js
reduceFunction = function(key, values) {
	result = {
		"anagramsList": values,
		"count": values.length
	};
	return result;

};
```

####FinalizeFunction
```js
finalizeFunction = function(key, values) {
	if (values.count >= 1)
		return values;
};
```

####MapReduce
```js
db.wordList.mapReduce(
	mapFunction,
	reduceFunction, {
		out: "anagramsList",
		finalize: finalizeFunction

	}
);
```

#####Przykładowe anagramy

```json 
    {"_id":"aabdor","value":{"anagramsList":["abroad","aboard"],"count":2}}
    {"_id":"aablst","value":{"anagramsList":["basalt","tablas"],"count":2}}
    {"_id":"aabmnt","value":{"anagramsList":["bantam","batman"],"count":2}}
    {"_id":"aacetv","value":{"anagramsList":["caveat","vacate"],"count":2}}
    {"_id":"aacimn","value":{"anagramsList":["caiman","maniac"],"count":2}}
    {"_id":"aaclrs","value":{"anagramsList":["rascal","scalar"],"count":2}}
    {"_id":"aaclsu","value":{"anagramsList":["casual","causal"],"count":2}}
    {"_id":"aadmrs","value":{"anagramsList":["madras","dramas"],"count":2}}
    {"_id":"aaffir","value":{"anagramsList":["affair","raffia"],"count":2}}
    {"_id":"aailmn","value":{"anagramsList":["animal","lamina","manila"],"count":3}}
    {"_id":"aailrt","value":{"anagramsList":["atrial","lariat"],"count":2}}
    {"_id":"aailsv","value":{"anagramsList":["avails","saliva"],"count":2}}
    {"_id":"aaimnr","value":{"anagramsList":["airman","marina"],"count":2}}
    {"_id":"aainpt","value":{"anagramsList":["patina","pinata"],"count":2}}
    {"_id":"aalmnu","value":{"anagramsList":["alumna","manual"],"count":2}}
    {"_id":"aalrst","value":{"anagramsList":["altars","astral","tarsal"],"count":3}}
    {"_id":"aanrtt","value":{"anagramsList":["rattan","tantra","tartan"],"count":3}}
```

####[Cały plik wynikowy](./anagrams/anagrams.json)

##2

###Dane: http://dumps.wikimedia.org/plwiki/

###Import

Do wykonania zadania konieczny był import danych do bazy MongoDB. Ponieważ dane były zapisane w formacie xml, użyłem skryptu 
    [php](https://github.com/kodekrash/wikipedia.org-xmldump-mongodb)


####Czas importu

Ponieważ z różnych przyczyn importowałem dane kilka razy, średni czas importu wyniósł: 89 minut 39 sekund.


###MapReduce

####Map function
```js
var mapFunction = function() {
    var orderedText = this.revision.text.match(/[a-ząśżźęćńół]+/gi);
    if (orderedText) {
        for (var i = 0; i < orderedText.length; i++)
            emit(orderedText[i], 1)
    }
};
 
```

####Reduce function
```js
var reduceFunction = function(key, values) {
	var count = 0;
	for (var i = 0; i < values.length; i++) {
		count += values[i];
	}
	return count;
};
```

####MapReduce
```js
	db.wordsList.mapReduce(mapFunction, reduceFunction, {
		out: 'wordsCounter'
	});
```

###Czas
Skrypt wynonywał się bardzo długo. Puszczony dwa razy wykonywał się odpowienio ponad 11h i ponad 10h.
Dodatkowo komputer podczas pracy MapReduce praktycznie nie nadawał się do użytku. Użycie RAM-u sięgało 100%. Zapewne przy dysku SSD czas byłby znacznie krótszy. 

##3 najczęściej występujące wyrazy/słowa:
```json
	{ "_id" : "w", "value" : { "count" : 20292468 } }
	{ "_id" : "i", "value" : { "count" : 5780516 } }
	{ "_id" : "a", "value" : { "count" : 5407771 } }
```


#Dodatkowe MapReduce na innych danych

##Dane
Użyłem danych z których korzystałem przy okazji zaliczenia ćwiczeń z przedmiotu. [Dane](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) opisują ruch samolotów w Stanach Zjednoczonych. MapReduce pokazuje, które do którego lotniska lata najwięcej samolotów startujących ze Stanów Zjednoczonych.

##MapReduce

###Map function
```js
var map = function() {
    emit(
        {kraj: this.DEST}, {count: 1}
    );
};
```

###Reduce function
```js
 
var reduce = function(key , values){
   var total = 0;
    for (var i=0; i<values.length; i++){
       total += values[i];
    }
   return total;
};
```
	
###MapReduce function
```js
db.wordsList.mapReduce(mapFunction, reduceFunction, {
	out: 'wordsCounter'
});
```

####Wynik
```json
{ "_id" : { "kraj" : "Atlanta, GA" }, "value" : { "count" : 30784 } }
{ "_id" : { "kraj" : "Chicago, IL" }, "value" : { "count" : 28106 } }
{ "_id" : { "kraj" : "Dallas/Fort Worth, TX" }, "value" : { "count" : 23482 } }
{ "_id" : { "kraj" : "Houston, TX" }, "value" : { "count" : 19636 } }
{ "_id" : { "kraj" : "Los Angeles, CA" }, "value" : { "count" : 18474 } }
{ "_id" : { "kraj" : "Denver, CO" }, "value" : { "count" : 17955 } }
{ "_id" : { "kraj" : "New York, NY" }, "value" : { "count" : 16417 } }
{ "_id" : { "kraj" : "San Francisco, CA" }, "value" : { "count" : 13539 } }
{ "_id" : { "kraj" : "Phoenix, AZ" }, "value" : { "count" : 13335 } }
{ "_id" : { "kraj" : "Las Vegas, NV" }, "value" : { "count" : 11052 } }
{ "_id" : { "kraj" : "Washington, DC" }, "value" : { "count" : 10427 } }
{ "_id" : { "kraj" : "Charlotte, NC" }, "value" : { "count" : 9777 } }
{ "_id" : { "kraj" : "Newark, NJ" }, "value" : { "count" : 9255 } }
{ "_id" : { "kraj" : "Orlando, FL" }, "value" : { "count" : 9189 } }
{ "_id" : { "kraj" : "Salt Lake City, UT" }, "value" : { "count" : 8676 } }
{ "_id" : { "kraj" : "Boston, MA" }, "value" : { "count" : 8283 } }
{ "_id" : { "kraj" : "Minneapolis, MN" }, "value" : { "count" : 8085 } }
{ "_id" : { "kraj" : "Seattle, WA" }, "value" : { "count" : 7776 } }
{ "_id" : { "kraj" : "Detroit, MI" }, "value" : { "count" : 7550 } }
{ "_id" : { "kraj" : "Miami, FL" }, "value" : { "count" : 7426 } }
```
