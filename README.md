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

####[Plik wynikowy](./anagrams/anagram.json)


