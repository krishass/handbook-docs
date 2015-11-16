# $(Observable)s

Observables are the key ingredients in a FuseJS application. Observables deal with @(Data Binding), reactive programming and asynchronous programming.

An `Observable` represents a value that can be observed. In Fuse, Observables are primarily used for data-binding between JavaScript and UX Markup.

Observables can hold a single value, or be treated as a list of values with 0 or more elements. When the value(s) of the Observable
changes, subscribers are notified.

Observables can be used for asynchronous and reactive programming, greatly simplifying data-driven UI programming.

> ## Video introduction to Observables

<div class="embed-responsive embed-responsive-16by9">
  <iframe width="420" height="315" class="embed-responsive-item" src="https://www.youtube.com/embed/mi8sWErrabI"></iframe>
</div>

### How to import

	var Observable = require('FuseJS/Observable');

### Basic use

Observables are created by calling the `Observable` function with zero or more initial values.

* `Observable(<initial values>)` - constructor

Examples:

	var emptyObservable = Observable();
	var isSomethingEnabled = Observable(true);

Empty observables will have `.value === undefined` and `.length === 0`.

#### Observable value

If the observable holds exactly one value, we can get or set that value using the `.value`
property:

	var someString = Observable('foobar');
	Console.Log(someString.value); // prints 'foobar'
	someString.value = 'barfoo'; // sets the value, notifies subscribers.

When the `.value` property is set, all subscribers are notified.

#### Observable lists

If we want to use the observable as a list of values, we manipulate it using methods like `.add(item)` and `.remove(item)`.
We can also query the number of values in the list through the `.length` property:

	var friends = Observable('Jake', 'Jane', 'Joe');
	Console.Log(friends.length); // prints 3

	friends.add('Gina');
	Console.Log(friends.length); // prints 4

See the full list of @(Observable.Members) to see what's possible with observable lists.


#### Observable functions
When an `Observable` is initialized with a function as its only argument, the `.value`
of the observable is computed by evaluating the function.

Reactive dependencies are automatically generated with all other observables touched while
evaluating the function.

Example:

	var firstName = Observable('John');
	var lastName = Observable('Doe');

	var fullName = Observable(function() {
		return firstName.value + ' ' + lastName.value;
	})

If the `firstName` or `lastName` changes, the `fullName` will now update automatically.

Yes, it's magic.

## $(Observable.Members:Members)

### $(Value operators)

#### $(value)
Gets or sets the current value of the @(Observable).

The `value` property acts as a shorthand for `getAt(0)` and `replaceAt(0)`.
It is most often used with single value Observables, although this is not a requirement.

	if (isSomethingEnabled.value) {
		doSomething();
	}
	isSomethingEnabled.value = false;

### List operators

#### $(length:length)
Returns the number of values in the @(Observable)

	var fruits = Observable('Orange', 'Apple', 'Pear');
	Console.Log(fruits.length); //output: 3

#### $(getAt:getAt(index))
Returns the value at the given `index`

	var seasons = Observable('Summer', 'Fall', 'Winter', 'Spring');
	Console.Log(seasons.getAt(2)); //output: 'Winter'

#### $(add:add(value))
Adds `value` to the @(Observable:observables) list of values.

	var colors = Observable('Red', 'Green');
	colors.add('Blue');

#### $(remove:remove(value))
Removes the first occurence of `value` from the @(Observable:observables) list of values.

	var shapes = Observable('Round', 'Square', 'Rectangular');
	shapes.remove('Rectangular');

#### $(tryRemove:tryRemove(value))
Tries to remove the first occurence of `value` from the @(Observable:observables) list of values.
Returns true if successful, and false otherwise.

	var shapes = Observable("Round", "Square", "Rectangular");
	if(shapes.tryRemove("Rectangular")) {
		Console.Log("success");
	}

#### $(removeAt:removeAt(index))
Remove the value at the given `index`.

	var shapes = Observable('Round', 'Square', 'Rectangular');
	shapes.removeAt(2);

#### $(removeWhere:removeWhere(func))
Removes all values for which `func` is true.

	var hotPlaces = Observable(
		{name: "Oslo", temperature: 30},
		{name: "New York", temperature: 24},
		{name: "California", temperature: 27},
		{name: "Sydney", temperature: 10}
	).removeWhere(function(place){
		return place.temperature < 20;
	}); //Removes Sydney from the list

#### $(forEach:forEach(func))
Invokes `func` on every value in the @(Observable).

	var numbers = Observable(10, 2, 50, 3);
	numbers.forEach(function(number) {
		Console.Log(number + " is a nice number!");
	});

#### $(replaceAt:replaceAt(index, value))
Replaces the value at `index` with `value`

	var ingredients = Observable('sugar', 'milk', 'potato');
	ingredients.replaceAt(2, 'flour'); //Replaces 'potato' with 'flour'

#### $(replaceAll:replaceAll(array))
Replaces the Observables values with values from the `array`.

	var colors = Observable("Red", "Green", "Blue");
	colors.replaceAll(["Orange", "Cyan", "Pink"]);

#### $(clear:clear())
Removes all values from the @(Observable).

	var colors = Observable("Red", "Green");
	colors.clear();

#### $(indexOf:indexOf(value))
Returns the index of the first occurrence of `value`.

	var seasons = Observable("Summer", "Fall", "Winter", "Spring");
	var index = seasons.indexOf("Winter"); // 2


#### $(contains:contains(value))
Returns true if `value` exists in the `var`.

	Observable seasons = Observable("Summer", "Fall", "Winter", "Spring");
	var winterExists = seasons.contains("Winter"); // true

#### $(refreshAll:refreshAll(newValues, compareFunc, updateFunc, mapFunc))
Updates all items in the @(Observable) with the values from `newValues`.
`compareFunc` is used to determine whether two items are equal. `updateFunc` is used to update an existing item with new values when a match is found by `compareFunc`.
`mapFunc` is called whenever a new item is found and allows it to be mapped to a new object.

	var items = Observable({
		{id: 1, text: "one" },
		{id: 2, text: "two" },
		{id: 3, text: "tres" },
	})
	var newItems = [
		{id: 3, text: "three" },
		{id: 4, text: "four" },
		{id: 5, text: "five" }
	]
	items.refreshAll(newItems, 
		//Compare on ID
		function(oldItem, newItem){
			return oldItem.id == newItem.id;
		},
		// Update text
		function(oldItem, newItem){
			oldItem.text.value = newItem.text;
		},
		// Map to object with an observable version of text
		function(newItem){
			return { id:newItem.id, Observable(newItem.text)
		}
	);


### $(Reactive operators)

FuseJS comes with set of reactive operators that return @(Observable:Observables) from other @(Observable:Observables). This means that if the original `Observable` changes, any `Observable`s that are created as a result of applying a reactive operator will also change automatically.

> Note! It is important to understand that the result of a reactive operator will only be computed if the resulting `Observable` is *consumed*, i.e. databound and its value is needed. If you @(map(func)) over an `Observable` collection and try to `console.log` from the mapping function, these contents might not be displayed because the resulting `Observable` is not databound. If you run into this problem, you can manually add a subscriber for debugging purposes, as described @(addSubscriber:here).

#### $(where:where(condition))
Returns a new @(Observable) with only the values for which `condition` returns true.

The new @(Observable:observable) observes the old @(Observable:observable), and will therefore update whenever a value changes in the original observable.

	fruits = Observable(
		{ name: 'Apple' , color: 'red'    },
		{ name: 'Lemon' , color: 'yellow' },
		{ name: 'Pear'  , color: 'green'  },
		{ name: 'Banana', color: 'yellow' });

	goodFruits = fruits.where(function(e){
		return e.color === 'yellow';
	});


If the `condition` returns an `Observable`, the `where` operator will also observe the condition.

#### $(map:map(func))
Invokes `func` on every value in the @(Observable) returning a new @(Observable) with the results.

	var numbers = Observable(1, 4, 9);
	var roots = numbers.map(function(number) {
		return Math.sqrt(number);
	});

The values of `roots` becomes the square root of the numbers in `numbers`. The values in `numbers` remain unchanged.

#### $(count:count())
Returns the number of values in the @(Observable) as an observable number. Whenever an item is added or removed from the @(Observable), the `count` changes.

	books = Observable("UX and you",
		"Observing the observer",
	    "Documenting the documenter");
	numBooks = books.count(); //result: 3

#### $(countCondition:count(condition))
Returns an observable number of values for which `condition` is true.

	var tasks = Observable(
		{ title: "Learn Fuse", isDone: true },
		{ title: "Learn about Observables", isDone: true },
		{ title: "Make awesome app", isDone: false }
	);
	var tasksDone = tasks.count(function(x){
		return x.isDone;
	}); // 2

#### $(not:not())
Returns an @(Observable) that has the inverse value of the @(Observable) you are calling it on. If the @(Observable) is `true`, the returned one will be `false`, and vice versa.

	falseValue = Observable(false);
	trueValue = falseValue.not();

<!--
#### $(inner:inner())
TODO: Write inner -->

#### $(filter:filter(condition))
Returns an observable that will only propagate values that pass the given `condition`, otherwise it retains its previous value.

This method only considers the first (single) value of an observable.

<!-- TODO: Make example -->

#### $(expand:expand(func))
When an @(Observable) contains only a single array, expand will return an @(Observable) containing the values from that array.

Observable([1,2,3]).expand() -> Observable(1,2,3)


### Subscribing to updates

#### $(addSubscriber:addSubscriber(func))
To manually react to changes in an @(Observable), one can use the `addSubscriber` method.
`func` will be run whenever the @(Observable) changes.

#### $(removeSubscriber:removeSubscriber(func))
When you are done consuming the values from the Observable, it is important to clean up by removing
the subscription, otherwise we can accumulate memory garbage over time:

	username.removeSubscriber(usernameLogger);

### Other

#### $(depend:depend())

#### $(failed:failed(message))

#### $(setValueExclusive:setValueExclusive(value, excludingObserver))
Sets the value of the @(Observable) without notifying `excludingObserver`

	var observable = Observable(1);

    var shouldGetNotification = function() { }
	var shouldNotGetNotification = function() { }

	observable.addSubscriber(shouldGetNotification);
	observable.addSubscriber(shouldNotGetNotification);

	observable.setValueExclusive(2, shouldNotGetNotification);

#### $(toString:toString())
Returns a string representation of an observable and its contents.

	var testObservable = Observable(1, "two", "3");
	testObservable.toString(); // "(observable) 1,two,3"

## Topics
<!-- TODO: Fill inn here!! -->

Observables can be used for a lot of things:

* @(Data Binding)
* Asynchronous programming
* Reactive programming

<!-- TODO: Here are articles about each topic -->
