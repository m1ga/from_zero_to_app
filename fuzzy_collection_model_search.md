# _WORK IN PROGRESS - not done yet_

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span>

# Fuzzy search in Collections/Models

* Place <a href="https://github.com/krisk/Fuse">fuse.min.js</a> inside `/app/lib/`
* Create model `/app/models/myCollection.js`
```javascript
exports.definition = {
	config: {
		columns: {
			"id": "INTEGER PRIMARY KEY",
		    "name": "TEXT"
		},
		adapter: {
			type: "sql",
			idAttribute: "id",
			remoteBackup: false,
			collection_name: "myCollection"
		}
	},
	extendModel: function(Model) {
		_.extend(Model.prototype, {});

		return Model;
	},
	extendCollection: function(Collection) {
		_.extend(Collection.prototype, {
		});

		return Collection;
	}
};
```

* add Fuse to the beginning of myCollection.js `var Fuse = require("/fuse.min");`
* extend Collection with the following functions:
```javascript
buildSearchIndex: function(options) {
	options = (typeof options !== 'undefined') ? options : {};

	var defaultOptions = {
		keys: ["name"],
		threshold: 0.15,
		distance: 50,
		minMatchCharLength: 2
	};
	// just get attributes of every model
	var data = _.pluck(this.models, 'attributes');
	return this.fuse = new Fuse(data, _.defaults(options, defaultOptions));
},
// returns copy of collection with search results
search: function(query) {
	var searchResults = this.fuse.search(query);
	// get all inner items
	searchResults = _.pluck(searchResults, 'item');
	// returned models
	return this.filter(function(model) {
		return _.findWhere(searchResults, {
			id: model.get('id')
		});
	}, this);
}
```
* Add `<Collection src="myCollection"/>` to XML
* In your controller call
```javascript
var myCollection = Alloy.Collections.myCollection;
myCollection.fetch();
myCollection.buildSearchIndex();
```

* Textfield to search:
```javascript
var data = hallen.search($.tf_gym.value);
var names = _.map(data, function(i, o) {
	return {
		name: i.get("name"),
		id: i.get("id")
	};
});
console.log(names);
```

* Add dummy data
```javascript
Alloy.createModel('myCollection', {name:'foo'}).save();
Alloy.createModel('myCollection', {name:'foobar'}).save();
Alloy.createModel('myCollection', {name:'bar'}).save();
Alloy.createModel('myCollection', {name:'myfoobartext'}).save();
```

Display the data in `$.view_output`

```javascript
var localTimer = null;

if (names.length > 0) {
	// got some results

	if (localTimer) {
		clearTimeout(localTimer);
	}

	// short delay
	localTimer = setTimeout(function() {
		$.view_output.removeAllChildren();

		_.each(names, function(item) {
			var lbl = Ti.UI.createLabel({
				text: item.name,
				left: 5,
				right: 5,
				height: 30,
				width: Ti.UI.FILL,
				color: "#000"
			});
			$.view_output.add(lbl);
		});
	}, 100);
} else {
	// no results - clear view
	$.view_output.removeAllChildren();
}
```
