# $(Data Binding)

Fuse provides first class support for creating data driven apps with UX tags through direct binding, iteration and branching. UX can also do referencing deep inside complex data structures, so you do not have to do tedious data massaging in code. 

## JavaScript module as data source

The simplest way to create a data source is through JavaScript, here is a databound "Hello world" minimal example:

	<App>		
		<JavaScript>	
			module.exports = {
				greeting: "Hello databound world!"
			};
		</JavaScript>
		<Text Value="{greeting}" />			
	</App>

Similarly, you can bind to collections:

	<App>		
		<JavaScript>
			var data = ["1", "2", "3"];

			module.exports = {
				data: data
			};
		</JavaScript>
		<StackPanel>
			<Each Items="{data}">
				<Text Value="{}" />
			</Each>
		</StackPanel>
	</App>

This will predictably list out the text strings 1, 2 and 3. When binding the `Text Value` `{}` means _this data context_. Typically, you will bind to more complex data sources, so each element will have something interesting to bind to:

	<App Theme="Basic">		
		<JavaScript>
			var Observable = require("FuseJS/Observable");

			var data = Observable(
				{name: "Hubert Cumberdale", age: 12}, 
				{name: "Marjory Stewart-Baxter", age: 43}, 
				{name: "Jeremy Fisher", age: 25});

			module.exports = {
				data: data
			};
		</JavaScript>
		<StackPanel>
			<Each Items="{data}">
				<DockPanel>
					<Text Dock="Right" Value="{age}" />
					<Text Value="{name}" />
				</DockPanel>
			</Each>
		</StackPanel>
	</App>

In this case, we have also made the data source @(Observable). This means that it supports propagating changes to the data source at runtime. In this case, the collection itself is `Observable`, but the items are not. You can bind to the children, but if they were to change, these changes would not be reflected in the UI. If you wanted to make the children also propagate their changes to the UI, you would make them `Observable` also:

	<JavaScript>	
		var Observable = require("FuseJS/Observable");
		var data = Observable(
			{ name: Observable("Hubert") }, 
			{ name: Observable("Marjory") });		
		module.exports = {
			data: data
		};
	</JavaScript>
	<StackPanel>
		<Each Items="{data}">
			<Text Value="{name}" />
		</Each>
	</StackPanel>

You can also bind to a path:

	<JavaScript>	
		var complex = {
			user: {
				userinfo: {
					name: "Bob"
				}
			}
		};
		module.exports = {
			complex: complex
		};
	</JavaScript>
	<Text Value="{complex.user.userinfo.name}" />		

This is very useful when binding to arbitrary data sources such as those returned from a REST service as JSON, as it often allows you to bind directly to complex data without processing the data in code first. See here for an in-depth example of just that: https://www.fusetools.com/developers/examples/newsfeed

### Binding to JavaScript functions

You can hook up event handlers to call JavaScript functions with similar syntax:

	<JavaScript>			
		module.exports = {
			clickHandler: function (args) { 
				console.log("I was clicked: " + JSON.stringify(args)); 
			}
		};
	</JavaScript>
	<Button Clicked="{clickHandler}" Text="Click me!" />
	
You can read more about this in the @(FuseJS) section.


## $(Data Context)

At any point in a Fuse `Node` tree, there is a *data context*. A data binding on any node will be relative to the current data context on the node. By default, this data context is `null`, and any data binding will just return null or empty values. 

To set the data context, you typically add a *behavior* to a node that provides the data context, such as a `<JavaScript>` tag. When using a `<JavaScript>` tag, the `module.exports` from the module specified will become the data context of the node the.

Some behaviors, such as `<Each>` will take objects from the current data context and specify different data
contexts for each child node.

> ## Other data sources

The data binding system in Fuse from an @(Uno) implementation point of view is called `Fuse.Reactive`. It consists of a set of interfaces (primarily `IObservable`) that can be implemented on top of any data source in @(Uno) code, which in turn can be used as @(Data Context) on nodes in Fuse. Fuse provdides out-of-the-box implementations on top of @(Observable) in @(FuseJS).

In the @(Fuse Roadmap:roadmap for Fuse), we are planning to provide observable data types for Swift, Java and other languages that can be used for databinding directly in UX markup.

Users who write @(Uno) code can implement `IObservable` by hand to use Fuse's data binding system on top of custom native data sources.

## $(Each)

`Each` is used to repeat pieces of UX markup for each item in a collection.

The `Each` behavior maintains one copy of its subtree per item in its $(Items) collection, and adds and removes these from the parent node accordingly. The `Items` collection can be an @(Observable) that can be changed dynamically.

When using `Each`, we typically data-bind the `Items` property to an array data source to produce one visual 
node per object in the data source.

	<Each Items="{items}">
		<Rectangle Width="{width}" Height="{height}" Fill="#808" />
	</Each>

Observable add/remove operations on the `Items` collection can be animated using @(AddingAnimation), @(RemovingAnimation) and @(LayoutAnimation)

It is also possible to nest `Each` behaviors:
	
	<JavaScript>
		var Observable = require("FuseJS/Observable");

		module.exports = {
			items: [ 
				{ 
					inner: [ 
						{ child: "John" },
						{ child: "Paul" } 
					]
				}, 
				{ 
					inner: [ 
						{ child: "Ringo" }, 
						{ child: "George" }
					]
				}
			]			
		};
	</JavaScript>
	<ScrollViewer>
		<StackPanel>
			<Each Items="{items}">
				<StackPanel Orientation="Horizontal">
					<Each Items="{inner}">
						<Text Value="{child}" Margin="10" />
					</Each>
				</StackPanel>
			</Each>
		</StackPanel>
	</ScrollViewer>

> Note: it is not possible to nest two `Each` behaviors directly inside each other without an intermediary container, like `Panel` or `StackPanel` as above.

You can also just use `Each` as a simple repeater:

	<Grid ColumnCount="3">
		<Each Count="9">
			<Rectangle Margin="10" Fill="#610" />
		</Each>
	</Grid>


## $(WhileCount) and $(WhileEmpty)

The `WhileEmpty` and `WhileCount` @(Trigger:triggers) can be used to act on the number of items in a collection:

	<Each Items="{friends}">
		<!-- ... List friend ... -->
	</Each>
	<WhileEmpty Items="{friends}">
		<Text>No friends. :(</Text>
	</WhileEmpty>

`WhileEmpty` is a special case of `WhileCount` where the `EqualTo`-property is set to `0`. `WhileCount` accepts the following properties:

- `EqualTo` - Active when the count of the collection is equal to the provided value
- `GreaterThan` - Active when the count of the collection is greater than the provided value
- `LessThan` - Active when the count of the collection is less than the provided value

For example:

	<WhileCount Items="{things}" EqualTo="3" GreaterThan="3" >
		<Text>You have 3 or more things.</Text>
	</WhileCount>

## $(Select)

If you have a complex data context and want to narrow the data context down, you can use `Select`:

	<JavaScript>
		module.exports = {
			complex: {
				item1: {
					subitem1: { name: "Spongebob", age: 32 }
				}
			}
		};	
	</JavaScript>
	<Select Data="{complex.item1.subitem1}">
		<Text Value="{name}" />
		<Text Value="{age}" />
	</Select>


## $(Match) and $(Case)

You can drive which subtree should be active using `Match` and `Case`:

	<JavaScript>
		module.exports = {
			active: "blue"
		};	
	</JavaScript>
	<Match Value="{active}">
		<Case String="red">
			<Rectangle Fill="#f00" Height="50" Width="50" />
		</Case>
		<Case String="blue">
			<Rectangle Fill="#00f" Height="50" Width="50" />
		</Case>
	</Match>

Valid match properties for `Case` are:

- `String` - match a string
- `Number` - match a number
- `Bool` - match a boolean

## $(DataToResource)

You can bind to a defined resource using @(DataToResource):

	<FileImageSource ux:Key="picture" File="Pictures/Picture1.jpg" />		
	<JavaScript>
		module.exports = {
			picture: "picture"
		}
	</JavaScript>			
	<Image Source="{DataToResource picture}" />			

> ## Ensuring order

Note that `Each` does not necessarily insert the children at the exact location in the tree where the `Each` node resides. To ensure child order, wrap the `Each` in some sort of @(Panel):

	<StackPanel>
		<Text>I go first!</Text>
		<StackPanel>
			<Each Items="{strings}">
				<Text Value="{}" />
			</Each>
		</StackPanel>
		<Text>I go last!</Text>
	</StackPanel>

We are considering adding more tricks to allow more flexibility in this case, but for now this is the recommended approach.