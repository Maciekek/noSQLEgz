#Zadanie 3
---
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


