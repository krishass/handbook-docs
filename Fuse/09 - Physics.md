# $(Physics)

Some types of animation ([like this one](https://www.fusetools.com/developers/examples/swipeplaces)) are best described using Physics simulation. Fuse comes with a set of physics based triggers and behaviors which can be used for these situations.

## $(Physics Rules)

Physics simulation is achieved by using different types of physics rule behaviors like `Spring` or @(PointAttractor). Each rule knows how to perform a certain kind of simulation and how to apply this to each frame of the animation. We can add additional customization to the animation by using the related @(Trigger:triggers) to animate based on certain physics @(ForceField triggers:events).

### $(Draggable)

The `Draggable` behavior lets you move an @(Element) using the pointer by dragging it.

```
<Panel>
	<Rectangle Width="100" Height="100" Fill="Red">
		<Draggable/>
	</Rectangle>
</Panel>
```

#### $(WhileDragging)

The `WhileDragging` trigger is active while the @(Element) is being dragged and deactivates as it gets released.

```
<Panel>
	<Rectangle Background="Blue" Width="50" Height="100">
		<Draggable/>
		<WhileDragging>
			<Rotate Degrees="90" Duration="0.8"
			        Easing="BounceOut" EasingBack="BounceIn"/>
		</WhileDragging>
	</Rectangle>
</Panel>
```

### $(PointAttractor)

The `PointAttractor` `Rule` is one of the `ForceField` rules and can be used to define points which @(Element:elements) are attracted towards. The `PointAttractor` can be customized with the following properties:
- Offset - Set the position of the `PointAttractor` relative to its container
- Radius - The radius from the point where the attractor will have an effect.
- Strength - How much attraction is applied to the affected elements.
- Exclusive - Whether this attractor will be exclusively applied when it is considered the strongest @(Physics Rules:rule) applying force.

The [Swipe places](https://www.fusetools.com/examples/swipeplaces) example is a good example of the `PointAttractor` in use.

<!-- ### $(Spring)
The Spring @(Rule:rule) is used

- Target
- Length
- Stiffness
AUTH: Buggy?
-->

## $(ForceField triggers)
`ForceField` triggers are used to animate based on whether an @(Element) is affected by a `ForceField` rule or not.

- ForceField - The `ForceField` needed to enter to activate this @(Trigger).

### $(EnteredForceField)

`EnteredForceField` is a pulse triger which which activates when the @(Element) enters the specified `ForceField`.
Being a pulse trigger, means that the @(Animation:animations)/@(Actions:actions) inside the trigger will be played forwards and then backwards in one continuous run. One can modify the `Threshold` property to specify how close the @(Element) has to be to activate the trigger.

### $(ExitedForceField)
`ExitedForceField` works like @(EnteredForceField), but activates when elements leave the specified `ForceField`.

### $(InForceFieldAnimation)

`InForceFieldAnimation` is used to animate @(Element:elements) as they enter a @(ForceField triggers:forcefield). One can modify the `From` and `To` properties to control where in the `ForceField` the animation should occur.

- `From` & `To` - Number between 0 and 1, 0 being the edge of the forcefield and 1 at the center.

	<Panel>
		<Panel Alignment="Top">
			<Rectangle Background="Blue" Width="50" Height="100" Margin="0,100">
				<Draggable/>
				<InForceFieldAnimation ForceField="attractor" From="0.5" To="1">
					<Rotate Degrees="360"/>
				</InForceFieldAnimation>
				<InForceFieldAnimation ForceField="attractor" From="0" To="1">
					<Change circleColor.Color="#f00"/>
				</InForceFieldAnimation>
			</Rectangle>
		</Panel>
		<Panel Alignment="BottomCenter" MaxHeight="10000" MaxWidth="10000"
		       Height="800" Width="800" Y="50%">
			<Panel>
				<PointAttractor ux:Name="attractor" Radius="400" Strength="200"/>
			</Panel>
			<Circle>
				<SolidColor ux:Name="circleColor" Color="#ddd"/>
			</Circle>
		</Panel>
	</Panel>
