# $(Trigger:Triggers) and $(Animators)

Triggers provide a declarative way of creating animations with Fuse. At their most basic, triggers represent events that are triggered in response to user and/or program input. @(Trigger:Triggers) can contain @(Animators) and @(Actions) which are used to animate and manipulate elements as well as interacting with @(JavaScript).

@(Trigger)s are behaviors that live on a `Node` or UI @(Element), listen to events and perform animations and @(actions) in response.

For example, here is a @(Panel) with a @(WhilePressed) trigger causing the panel to rotate 90 degrees with a bouncy animation.
```
<Panel>
	<WhilePressed>
		<Rotate Degrees="90" Duration="0.5" Easing="BounceInOut"/>
	</WhilePressed>
</Panel>
```

> ### Video introduction to Triggers and Animators

<div class="embed-responsive embed-responsive-16by9">
  <iframe width="420" height="315" class="embed-responsive-item" src="https://www.youtube.com/embed/bT1npBvXEzw"></iframe>
</div>

<!-- TODO:
* Explain how triggers are a timeline, plays forwards/backwards, applies/unapplies -->

As well as animating properties, one can also use triggers to add and remove entire elements.

### $(Rest state) and deviation

The default layout and configuration of UX markup elements is called the rest state. Triggers define deviations from this rest state. Each trigger knows how to "un-apply" its own animation to return the rest state, even if interrupted mid-animation. This is great, because it means animation is completely separated from the logical state of your program, greatly reducing the complexity of dealing with combined animation on
multiple devices, screen sizes, with real data and real user input.


## Animators

Animators are used to specify which and how @(Element:elements) are to be animated when a @(Trigger:trigger) is triggered.
There are five pairs of properties which are important for controlling the exact result of an animation.

### $(Target)/Value
The `Target` property is used to identify the property which we intend to animate.
The `Value` property is the value of the result of an animation.

Because the task of setting a target and value is so common, UX has a special syntax for this. Instead of
```
<Change Target="target.Property" Value="Value"/>
```
one can do the following:
```
<Change target.Property="Value"/>
```

### $(Duration)/$(DurationBack)
Animations can have different behavior when animating forward and backward. When a trigger is activated, the animation is said to play forward. When the trigger is deactivated, the animation is played backward. Duration is used to set the duration for the animation. One can set a different duration for the backward animation by using the `DurationBack` property.

When there are multiple @(Animators:animators) inside a trigger, the total duration of the trigger will be the longest duration among the animators.

In the following example, the total duration of the @(WhileTrue) trigger will be 3 seconds. If we wanted the animations to happen one after the other, we could use @(Delay).

```
<WhileTrue>
	<Move X="10" Duration="2"/>
	<Rotate Degrees="90" Duration="3"/>
</WhileTrue>
```

### $(Delay)/$(DelayBack)
Setting the `Delay` property results in the actual animation being delayed by that amount of seconds. `DelayBack` is used to set a different delay on the backward animation. The total duration of the animation becomes the delay + the duration. The following @(Change:change) animator has a total duration of 7 seconds. It waits 5 seconds after being activated and then animates its @(Target) over 2 seconds.
```
<Change Delay="5" Duration="2" someElement.Height="100"/>
```

### $(Easing)/$(EasingBack)

Fuse comes with a standard set of predefined easing curves. Easing curves are used to control how an animation progresses over time. The default easing is set to `Linear`. With linear easing, the animation progresses at the same speed over its entire duration. This usually appears quite unnatural and fake. To gain a more natural feel, we can change the easing to `QuadraticInOut`, like so:
```
<Change Easing="QuadraticInOut" Duration="2" someElement.Property="SomeValue"/>
```
This animator will progress slowly in the beginning, faster in the middle, and then slow again in the end.

The following easing curves are defined:
- $(Linear)
- $(Quadratic)
- $(Cubic)
- $(Quartic)
- $(Quintic)
- $(Sinusoidal)
- $(Exponential)
- $(Circular)
- $(Elastic)
- $(Back)
- $(Bounce)

They each have three different versions: [easing]In, [easing]Out and [easing]InOut.
Assign easing to an animator like so:
```
<Move Easing="CubicIn" X="5" Y="10" Duration="0.5"/>
<Scale Easing="BounceOut" Factor="1.5" Duration="0.2"/>
<Rotate Easing="BounceOut" Degrees="10" Duration="0.4"/>
```

<!-- - MixOp
TODO/AUTH: Write about mixop-->

### $(Change)

`Change` temporarily changes the value of a property while its containing trigger is active. To permanently change a value, use the @(Set) animator.
The `Change` animator can be used to animate almost any property.

```
<Panel ux:Name="somePanel">
	<SolidColor ux:Name="someColor" Color="Red"/>
</Panel>
<Change somePanel.Opacity="0"/>
<Change someColor.Color="Blue" Duration="0.3"/>
<Change somePanel.Visibility="Collapsed"/>
```

One can also animate such properties as `Width`, `Height` and `Margin`, but because these properties might affect the entire layout of your UI, this can end up being quite costly in terms of performance. There are usually more effective ways to do animations that interact with layout. Check out @(LayoutAnimation) and @(MultiLayoutPanel) for some inspiration.

### $(Move)

The `Move` animator is used to move an element. `Move` does not affect layout, so the element will just get an offset from its actual location. By default, `<Move X="10"/>` will move the element by 10 points.
Sometimes, one wants an element to move relative to its own size or some other elements size. To control this, we can use the @(RelativeTo) property.

$(RelativeTo) can be set to the following values:
- `Local`(default): Moves the set amount of points in the X and/or Y direction.
- `Size`: Moves the set amount times the size of the element. So X="1" moves the element by its entire width in the X direction.
- `ParentSize`: Same as `Size` but uses the elements parents size instead.
- `LayoutChange`: Used in response to a @(LayoutAnimation) to move the element by the amount of a layout change.
- `Keyboard`: Moves the element relative to the size of the eyboard.

`<Move X="0.5" RelativeTo="Size"/>` will move the element by half of its own width in the x-direction.
Move corresponds to adding a @(Translation) on the element and using @(Change) to animate its X and Y values. The following two examples give the same result.
```
<Panel>
	<WhilePressed>
		<Move X="100" Duration="0.2"/>
	</WhilePressed>
</Panel>
```
```
<Panel>
	<Translation ux:Name="someTranslation"/>
	<WhilePressed>
		<Change someTranslation.X="100" Duration="0.2"/>
	</WhilePressed>
</Panel>
```

### $(Scale)

`Scale` works in the same way as @(Move) except that it scales the element. Note that scale doesn't actually change the elements size. This means that the rest of the UI layout wont be affected and the animation is guaranteed to be fast.

```
<Scale Factor="2" Duration="0.4"/>
```

### $(Rotate)
`Rotate` rotates an Element and is equal to adding a @(Rotation) and animating it with a @(Change).
```
<Rotate Degrees="90" Duration="0.5"/>
```

### $(Resize)

When used in concert with @(LayoutAnimation), `Resize` allows you to animate the size of an element:

	 <Resize RelativeTo="LayoutChange" Duration="0.5" Easing="CircularInOut" />


### $(Cycle)

`Cycle` continuously animates a property between two values at a given frequency.

	<Panel>
		<Translation ux:Name="someTranslation" />
		<WhilePressed>
			<Cycle Target="someTranslation.X" Low="-20" High="20" Frequency="2" />
		</WhilePressed>
	</Panel>

You may also specify a `Duration` to control the length of the animation.

### $(Spin)
`Spin` continuously rotates an element, given a `Frequency` measured in full rotations per second.

	<Panel>
		<WhilePressed>
			<Spin Frequency="2" />
		</WhilePressed>
	</Panel>

As with @(Cycle), you may also specify a `Duration` to control the length of the animation.


> ## Transforms
All @(Element:elements) can have transforms applied to them in order to move, scale or rotate.
It is worth mentioning that the order of these transforms affects the order of when they are applied to the element, and therefore can lead to different results.

```
<Panel Width="100" Height="50">
	<Translation X="100"/>
	<Rotation Degrees="45"/>
</Panel>
```
```
<Panel Width="100" Height="50">
	<Rotation Degrees="45"/>
	<Translation X="100"/>
</Panel>
```

The two examples have quite different results. In the first case, the panel is first moved 100 points to the right and then rotated 45 degrees. In the other case, the panel is first rotated 45 degrees. The positive `X`-direction is now 45 degrees downward, and so our panel ends up being moved toward the bottom right.

### $(Keyframe)
<!--AUTH:-->
There are situations where we don't simply want to animate from point a to point b. For the cases where we want to specify several steps for an animation, we can use @(Keyframe:keyframes).

```
<Move RelativeTo="ParentSize">
	<Keyframe X="10" Time="0.5"/>
	<Keyframe X="15" Time="1"/>
	<Keyframe X="5" Time="2"/>
</Move>
```

This @(Move) animator will first animate X to 10 over 0.5 second, then from 10 to 15 over 0.5 second. Finally, it will go from an X of 15 to 5 over 1 second.
Here is an example of using @(Keyframe:keyframes) with a @(Change) animator:

	<Page>
		<SolidColor ux:Name="background" Color="#f00"/>
		<ActivatingAnimation>
			<Change Target="background.Color">
				<Keyframe Value="#0f0" TimeDelta="0.25"/>
				<Keyframe Value="#f00" TimeDelta="0.25"/>
				<Keyframe Value="#ff0" TimeDelta="0.25"/>
				<Keyframe Value="#0ff" TimeDelta="0.25"/>
			</Change>
		</ActivatingAnimation>
	</Page>

This time we use `TimeDelta` instead of time. With `TimeDelta` we can specify time as a relative term instead of absolute. This means that the order of the @(Keyframe:keyframes) matter, but it lets us reason about the keyframes in terms of their duration instead of their absolute time on the timeline.

<!-- TODO: Interpolation -->

### $(Translation)
`Translation` moves the element in the specified X, Y, and Z direction. The follwing example shows a @(Rectangle) which is moved 100 points in the X-direction and 50 points in the Y-direction.
```
<Rectangle Width="50" Height="50">
	<Translation X="100" Y="50"/>
</Rectangle>
```

You can set the translation value for `Translation` with:

 * `X` or `Y`, to seperately set the X and Y translations
 * `XY`, to set the translation of both axes at once
 * `Z`, to set the translation in the Z axis.

The coordinates default to being relative to the elements original position(`TranslationModes.Local`), but this can be changed using the property `RelativeTo`. Further, you can make the transform relative to another element by using `RelativeNode`.

Additionally, `IsFlat` will return true if the `Translation` only translates on the X and Y axis.

### $(Scaling)
`Scaling` enlarges or shrinks the element by the factor specified. The following example will make the @(Rectangle) twice as big as the original size:
```
<Rectangle Width="100" Height="100">
	<Scaling Factor="2"/>
</Rectangle>

```

`Scaling` can be utilized using:

 * `Factor`, to set an universal scale on all axes
 * `Vector`, to set the scale of all three axes at once
 * `X`, `Y`, and `Z`, for control of individual axes.

While the property `IsFlat` exists, it will allways return true as `Scaling` can't add depth, and is only there for consistency reasons.

<!-- TODO: Document Vector -->

### $(Rotation)
`Rotation` rotates the element by the degrees specified. Here is an example of a rectangle which is rotated by 90 degrees.
```
<Rectangle Width="100" Height="50">
	<Rotation Degrees="90"/>
</Rectangle>
```

You can rotate an element using:

 * `Degrees`, controlling rotation around the Z axis
 * `DegreesX`, `DegreesY`, `DegreesZ`, giving you individual control of all 3 axes.
 * `EulerAngle` and `EulerAngleDegrees`, letting you set the euler angles of the element in radians or degrees, respectively.

Additionally, you can check if the `Rotation` is strictly around the Z axis by using the property `IsFlat`.
 
### $(Shear)

The `Shear` animator can be used to perform a shear mapping on an element. One can use `DegreesX` and `DegreesY` to set the shear on one axis, or `Degrees` and `Vector` to set the shear in both the X and Y plane, using degrees or radians.

While the effect is strictly 2D, the `IsFlat` property is there for consistency reasons, and will allways return true due to this.


## $(Attractor)

The `Attractor` is used to give a more natural movement to animations. It acts as an intermediary between an animator and its target. An `Attractor` will continuously animate its target towards its `Value` using a simple form of physics simulation. We can combine this behavior with animation by animating the attractor's `Value` property.
```
<Panel ux:Name="somePanel">
	<Translation ux:Name="someTranslation"/>
	<Attractor ux:Name="someAttractor" Target="someTranslation.X"/>
	<WhilePressed>
		<Change someAttractor.Value="100"/>
	</WhilePressed>

</Panel>
```

## $(Actions)

Triggers can contain actions too, which are one-off events that fire at a particular point in the trigger's timeline.

Note that actions, contrary to @(Animators:animators) are not reversible. This means it is not neccessarily possible to return to the @(rest state) if the trigger is reversed.

### $(Action.Delay:Delay)

Like @(Animators:animators), `Actions` can have a `Delay`. This specifies a number of seconds from the @(Trigger) is activated to the `Action` is fired.


### $(Action.AtProgress:AtProgress)

`Actions` also has a property called `AtProgress` which can be set to a value between 0 and 1. It has a similar function as `Delay`, but is instead relative to the full @(Duration) of the @(Trigger). Setting `AtProgress` to 0, means the action is fired as soon as the @(Trigger) is actiated. Setting it to 0.5 means it is fired half way through and so on.

> ### $(Set)

Permanently changes the value of a property. If you want to just change it temporarily, use @(Change). When using `Set` on a property, the value will not be reverted back when the containing trigger is deactivated. In the following example we change the color of a rectangle by setting the value of its `SolidColor` @(Element). Multiple activations of the @(Clicked) trigger won't have any additional effect.

```
<Rectangle>
	<SolidColor ux:Name="color" Color="#00f"/>
</Rectangle>
<Clicked>
	<Set color.Color="#f00"/>
</Clicked>
```

> ### $(Callback)

The `Callback` action is used to call a JavaScript function (see @(Data Binding)) when a trigger is activated.

```
<JavaScript>
	var someJSFunction = function () {
		Console.Log("some function called");
	}
	module.exports = { someJSFunction: someJSFunction };
</JavaScript>
<Rectangle Width="100" Height="50" Alignment="Center" Fill="Red">
	<WhilePressed>
		<Callback Handler="{someJSFunction}"/>
	</WhilePressed>
</Rectangle>
```

### $(GoForward)
Tell a @(Navigation) or a @(WebView) to step forward in its navigation history.

`<GoForward WebView="myWebView" />`  
`<GoForward Context="myNavigation" />`

### $(GoBack)
Tell a Navigation context or a @(WebView) to go backwards in its navigation history.

`<GoBack WebView="myWebView" />`  
`<GoBack Context="myNavigation" />`

> ### $(Toggle)
`Toggle` is used to toggle a boolean value between `true` and `false`. If inside a @(Switch) it will toggle the value of the @(Switch). `Toggle` can also be used to activate/deactive @(WhileTrue) and @(WhileFalse) triggers like so:

```
<WhileTrue ux:Name="trueTrigger">
	...
</WhileTrue>
<Panel>
	<Clicked>
		<Toggle Target="trueTrigger"/>
	</Clicked>
</Panel>
```

> ### $(BringIntoView)
The `BringIntoView` action is used together with the @(ScrollView) control. By setting its `TargetNode` property, we can instruct the @(ScrollView) to go to a position so that that that `Node` becomes visible.

This example shows how to use `BringIntoView` to make a @(ScrollView) automatically scroll between the top and the bottom by clicking a button:

```
<App Theme="Basic" ClearColor="#eeeeeeff">
	<DockPanel>
		<StatusBarBackground DockPanel.Dock="Top" />
		<ScrollView ClipToBounds="true">
			<StackPanel>
				<Panel ux:Name="panel1" Height="80" Background="#F44336" />
				<Panel Height="200" Background="#ddd"/>
				<Panel Height="200" Background="#bbb"/>
				<Panel Height="200" Background="#999"/>
				<Panel Height="200" Background="#777"/>
				<Panel Height="200" Background="#444"/>
				<Panel ux:Name="panel2" Height="80" Background="#3949AB" />
			</StackPanel>
		</ScrollView>
		<StackPanel Dock="Bottom" Height="60" Orientation="Horizontal" Alignment="Center">
			<Button Text="To the top">
				<Clicked>
					<BringIntoView TargetNode="panel1" />
				</Clicked>
			</Button>
			<Button Text="To the bottom">
				<Clicked>
					<BringIntoView TargetNode="panel2" />
				</Clicked>
			</Button>
		</StackPanel>
	</DockPanel>
</App>
```

<!--  ### $(BringToFront)
AUTH: TODO: Do we need to discuss Z-ordering? -->

## WebView-specific triggers & actions

### $(PageBeginLoading)
Triggers once the @(WebView) begins loading new content.

```
<WebView Url="http://fusetools.com">
	<PageBeginLoading>
		<DebugAction Message="Page began to load!"/>
	</PageBeginLoading>
</WebView>
```

### $(WhilePageLoading)
Becomes active when a @(WebView) Url changes, and stays active until it has completed loading content.

### $(PageLoaded)
Triggers once the @(WebView) has completed loading content from its current Url.

```
<WebView Url="http://fusetools.com">
	<PageLoaded>
		<DebugAction Message="Arrived at page!"/>
	</PageLoaded>
</WebView>
```

### $(Reload)
The `Reload` action lets you tell a given WebView to reload its current location. 

`<Reload WebView="myWebView" />`

### $(LoadUrl)
The `LoadUrl` action lets you tell a given WebView to navigate to a location. 

`<LoadUrl WebView="myWebView" Url="http://mypage.com" />`

> ### $(EvaluateJS)

The WebView offers limited execution of arbitrary JavaScript in the currently loaded web environment. This is done with the `<EvaluateJS/>` action. Let's look at a simplified example.

```XML
<EvaluateJS Handler="{onPageLoaded}">
	var result = {
		url : document.location.href
	};
	return result;
</EvaluateJS>
```

Note the use of a `return` statement in the script body. Implementations of JavaScript evaluation APIs generally act like a JavaScript [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop), and when evaluating multiple lines of JS the result of the last statement of the script becomes the returned value. For instance, "1+5" is completely valid JS when evaluated and returns the expected value of "6".

This can result in odd-feeling JS, where referencing an object becomes an implicit return statement, whereas an explicit return is not allowed.

```JavaScript
var result = {};
result.foo = "bar";
result; // using return here is invalid JS
```

To make this feel better and allow return, we currently inject the user's JS in the form of a function:

```JavaScript
(USER_JS)();
```

### Reading the result value

When we evaluate the JavaScript we are currently bound by platform restrictions in a key way: String is the only allowed return value type on Android, our lowest common denominator. Android allows for parity with iOS as of API level 19, which denies us good backwards compatibility. For now we must rely on the comparatively ancient [addJavaScriptInterface](http://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object, java.lang.String)) API for backwards compatibility. 

What this means is that any return value passed from the evaluated script must by necessity be returned as JSON and parsed back from it on the Fuse end. Even if all you want is the result of some arithmetic, you'd still receive it as a string and require a cast. Instead of forcing you to routinely `return JSON.stringify(foo)` from your own JS we handle this by *always* wrapping your JS in JSON.stringify before evaluation:

```JavaScript
JSON.stringify( (USER_JS)(); );
```

The returned JSON string here is then put into a result object with the `json` key. This is for clarity, so you never forget that the data you are receiving is a JSON string that you will need to parse.

```XML
<JavaScript>
	module.exports = {
		onPageLoaded : function(result) 
		{
			var url = JSON.parse(result.json).url;
		}
	};
</JavaScript>
``` 

Note that of course return is optional. If you don't return anything from your evaluated JS the return value of the expression will simply be "null".

## $(State groups)

State groups allow you to define completely custom states, with custom events.

### $(State)
A `State` consists of a set of @(Animators:animators) inside a `State` object. It acts as a normal @(Trigger), but is activated by its containing @(StateGroup).
```
<State>
	<Rotate Degrees="200" Duration="0.4"/>
	<Move X="10" Duration="0.4"/>
</State>
```

### $(StateGroup)
A `StateGroup` is used to group a set of @(State:states) together and switch between them. `StateGroup` has an `Active` property, which is used to assign which @(State) is currently active in that group. One can also specify the `TransitionType`, which can be either `Exclusive` or `Parallel`. `Exclusive` means that each state will have to be fully deactivated before the next state becomes active. `Parallel` means that as one state deactivates, the next one will become active and whatever properties they animate will be interpolated between them.

Here is an example of how to use a `StateGroup` to switch the color of a @(Rectangle) between three states:
```
<StackPanel>
	<Panel Width="100" Height="100">
		<SolidColor ux:Name="someColor"/>
	</Panel>
	<StateGroup ux:Name="stateGroup">
		<State ux:Name="redState">
			<Change someColor.Color="#f00" Duration="0.2"/>
		</State>
		<State ux:Name="blueState">
			<Change someColor.Color="#00f" Duration="0.2"/>
		</State>
		<State ux:Name="greenState">
			<Change someColor.Color="#0f0" Duration="0.2"/>
		</State>
	</StateGroup>
	<Grid ColumnCount="3">
		<Button Text="Red">
			<Clicked>
				<Set stateGroup.Active="redState"/>
			</Clicked>
		</Button>
		<Button Text="Blue">
			<Clicked>
				<Set stateGroup.Active="blueState"/>
			</Clicked>
		</Button>
		<Button Text="Green">
			<Clicked>
				<Set stateGroup.Active="greenState"/>
			</Clicked>
		</Button>
	</Grid>
</StackPanel>
```

## Data triggers

These triggers react to data changes, either from data binding or from the control context.

### $(WhileTrue)
`WhileTrue` is active while its `Value` property is `True` and inactive while it's false.

### $(WhileFalse)
`WhileFalse` is active while its `Value` property is `False` and inactive while it's true.

<!-- ### WhileFailed
TODO: I dont know what it does -->

## $(Gestures)

Following are triggers which react to pointer gestures.

### $(WhilePressed)
`WhilePressed` is active while its containing element is being pressed and the pointer is inside its bounds.
```
<Panel Width="50" Height="50">
	<WhilePressed>
		<Scale Factor="2" Duration="0.2"/>
	</WhilePressed>
</Panel>
```

### $(Clicked)
`Clicked` is activated in response to a click @(Gestures:gesture). What constitutes a click-event can be platform specific, but usually means that the pointer was pressed and released within the bounds of the containing element.

* Note: `Clicked`-triggers can be placed on any @(Element), not just @(Button:buttons).

```
<Panel Width="50" Height="50">
	<Clicked>
		<Scale Factor="2" Duration="0.2"/>
	</Clicked>
</Panel>
```

### $(Tapped)
The `Tapped`-trigger is quite similar to the @(Clicked)-trigger. Where a click just means that the pointer has to be pressed and released on the element, a tap means that the pointer has to be released within a certain time after the pointer is pressed.

### $(DoubleClicked)
`DoubleClicked` is activated when the element has been @(Clicked) twice within a certain timeframe.

### $(DoubleTapped)
As with @(DoubleClicked), `DoubleTapped` is activated when the element has been @(Tapped) twice within a certain timeframe.

### $(WhileHovering)
`WhileHovering` is active while the pointer is within the bounds if its containing @(Element).

* Note: `WhileHovering` only has value when the device supports a hovering pointer, like the mouse pointer on desktop machines. For most smart phones this trigger won't have much value.

## $(Control triggers)

### $(WhileEnabled)
The `WhileEnabled` trigger is active whenever its containing @(Element:elements) `IsEnabled` property is set to `True`.

```
<Panel  Width="50" Height="50" Background="Red" >
	<WhileEnabled>
		<Rotate Degrees="45" Duration="0.5"/>
	</WhileEnabled>
</Panel>
```

### $(WhileDisabled)
The `WhileDisabled` trigger is active whenever its containing @(Element:elements) `IsEnabled` property is set to `False`.


## $(Platform triggers)

Sometimes it can be necessary with platform specific code. This can be done by using the platform triggers `Android` and `iOS`.

In the following example, we place a red @(Panel) if on an Android device and a blue @(Panel) if on an iOS device:

```
<Panel>
	<Android>
		<Panel Background="#f00" Alignment="Center" Width="150" Height="150"/>
	</Android>
	<iOS>
		<Panel Background="#00f" Alignment="Center" Width="150" Height="150"/>
	</iOS>
</Panel>
```

### $(WhileKeyboardVisible)
`WhileKeyboardVisible` is active whenever the on-screen keyboard is visible.

<!-- TODO: Example -->

<!-- ### WhileWindowAspect
TODO: Did we change the name of this?-->

## Special $(Animation)s

In Fuse terminology, *animations* are a certain category of triggers that animate in response to a higher level interpretation
of input, events and logical state changes.

Animation triggers are designed to solve quite specific animation problems that would otherwise be hard to pull off using the simpler triggers.
The flipside is that these triggers can be somewhat hard to understand and use, so make sure you read the docs and study examples before spending
too much time scratching your head.

### LayoutAnimation

The `LayoutAnimation` is triggered in response to a layout change. A layout change happens whenever an element gets a new layout by the layout system. `LayoutAnimation` is commonly used together with @(MultiLayoutPanel) for some pretty interesting animations. See @(LayoutAnimation) for more in depth documentation.

<!-- TODO: consider linking to external article here-->

### $(AddingAnimation)

<!-- TODO: Link to examples -->

`AddingAnimation` is triggered whenever the element is added to the visual tree. Adding animation is by default a backward animation, meaning it will animate from progress 1 back to 0.

<!-- TODO: Example -->

### $(RemovingAnimation)
`RemovingAnimation` is similar to @(AddingAnimation), but is triggered whenever an @(Element) is being removed from the visual tree.

`RemoveAnimation` is similar to @(AddingAnimation) but is triggered whenever the element is removed from its parent. `RemoveAnimation` progresses normally from 0 to 1 over the specified @(Duration).

In the following example, a rectangle will move in from the right side by the width of its parent container over one second when it is added to the visual tree by the @(Switch). It will move out to the left by the same distance when it is removed.
```
<App Theme="Basic" ClearColor="#eeeeeeff">
	<StackPanel Background="#ddd" Margin="10">
		<Switch>
			<WhileTrue>
				<Change whileTrue.Value="true"/>
			</WhileTrue>
		</Switch>
		<WhileTrue ux:Name="whileTrue">
			<Panel Height="50" Width="50" Background="Red">
				<AddingAnimation>
					<Move RelativeTo="ParentSize" X="1" Duration="1" Easing="BounceIn"/>
				</AddingAnimation>
				<RemovingAnimation>
					<Move RelativeTo="ParentSize" X="-1" Duration="0.5"/>
				</RemovingAnimation>
			</Panel>
		</WhileTrue>
	</StackPanel>
</App>
```

### $(ActivatingAnimation)

`ActivatingAnimation` allows for animating based on which @(Page) is active. `ActivatingAnimation` will progress from 0 to 1 as a @(Page) is being navigated to. If @(SwipeNavigate) is used, one can observe that `ActivatingAnimation` progressed from 0 as soon as the @(Page) is entering, stays at 1 as long as the @(Page) is active, and then progresses towards 0 again as the @(Page) is exiting.

### $(ScrollingAnimation)

`ScrollingAnimation` lets us create animations in response to a @(ScrollView) being scrolled. By using the `From` and `To` properties one can define an interval on the @(ScrollView) where the trigger gets activated.

In the following example, we use `ScrollingAnimation` to @(Scale) the @(ScrollView) as it is being scrolled.
```
<ScrollView>
	<StackPanel Background="#ddd" Margin="10">
		<Panel Height="200" Background="Red"/>
		<Panel Height="200" Background="Blue"/>
		<Panel Height="200" Background="Green"/>
		<Panel Height="200" Background="Purple"/>
		<Panel Height="200" Background="Teal"/>
	</StackPanel>
	<ScrollingAnimation From="0" To="400">
		<Scale Factor="0.3" />
	</ScrollingAnimation>
</ScrollView>
```

### $(WhileScrollable)

`WhileScrollable` is used to animate based on whether a @(ScrollView) can be scrolled or not. Use the `ScrollDirections` property to filter the activation based which directions we care about.

`ScrollDirections` can take on any one of the following values:
- All
- Both
- Down
- Horizontal
- Left
- Right
- Up
- Vertical

In the following example, our background changes color when we reach the bottom of our @(ScrollView):
```
<ScrollViewer>
	<SolidColor ux:Name="color" Color="#000"/>
	<StackPanel Margin="10">
			<Each Count="10">
				<Panel Height="200" Background="Red" Margin="2"/>
			</Each>
		</StackPanel>
	<WhileScrollable ScrollDirections="Down">
		<Change color.Color="#ddd" Duration="0.4"/>
	</WhileScrollable>
</ScrollViewer>
```

### $(ProgressAnimation)

`ProgressAnimation` can be used together with a slider to animate elements as one slides its thumb. `ProgressAnimation` always goes from 0 to 1 as one slides the slider from its minimum value to its maximum value.

```
<Image File="someImage.png">
	<Blur ux:Name="blur" Radius="0"/>
</Image>
<Slider Width="50%" Alignment="Bottom" Margin="0,80">
	<ProgressAnimation>
		<Change blur.Radius="10"/>
	</ProgressAnimation>
</Slider>
```

<!-- ## $(Timeline)

TODO: Might need edaqua for this one.

Timelines are used to create custom animations that can be played, paused, seeked etc. in response to events.-->

<!-- ## Advanced trigger usage

TODO: Show examples of
* Document Bypass
* Triggers within triggers
* Elements/nodes within triggers
* Styles within triggers (? not sure if this is currently possible) -->
