# $(LayoutAnimation) and $(MultiLayoutPanel)

There is a certain kind of animation which deserves special attention. When an @(Element) has certain properties like @(Width), @(Height) or @(Margin) (collectively reffered to as "layout properties") changed or when its location in the visual tree changes, we can trigger a `LayoutAnimation`.

Calculating layout for a large UX-document can be quite costly. When animating layout properties with @(Change) animators, we run the risk of forcing a new layout to be calculated each frame. This can very easily lead to frame drops.

Fuse offers a neat alternative to this: the @(LayoutAnimation) trigger.
For example, instead of animating the @(Width) of an @(Element) using @(Change), we can use @(Set) and react to this change using a @(LayoutAnimation). Inside `LayoutAnimation` we specify how our element should move/resize from its previous position to its new position.
Here is a quick example:

	<Panel>
		<Panel ux:Name="panel" Width="50" Height="50" Background="Teal">
			<LayoutAnimation>
				<Resize X="1" Y="1" RelativeTo="LayoutChange" Duration="0.5"/>
				<Move X="1" Y="1" RelativeTo="LayoutChange" Duration="0.5"/>
			</LayoutAnimation>
			<Clicked>
				<Set panel.Width="200"/>
				<Set panel.Height="300"/>
			</Clicked>
		</Panel>
	</Panel>

The @(Clicked) trigger has two @(Set) actions in it. They set the @(Width) and the @(Height) of the @(Panel).
The @(Panel) has a @(LayoutAnimation) on it with a @(Resize) and @(Move) animator. Both these animators have their `RelativeTo` property set to `LayoutChange`. This means that they will animate from whatever the value was before the new layout was calculated, to whatever the new values are. Because changing the @(Width), and @(Height) of the @(Panel) leads to it having its position changed, we also use a @(Move) animator to make a smooth animation.

## Animating other layout properties

@(LayoutAnimation) opens up a whole world of possibilities. We can for example use it to animate an @(Element:elements) alignment:

	<Panel>
		<Panel ux:Name="panel" Width="50" Height="50" Background="Teal" Alignment="Center">
			<LayoutAnimation>
				<Move X="1" Y="1" RelativeTo="LayoutChange" Duration="0.5"/>
			</LayoutAnimation>
			<Clicked>
				<Set panel.Alignment="BottomRight"/>
			</Clicked>
		</Panel>
	</Panel>

In this example, we use a @(Set) action to change the @(Alignment) of a @(Panel) from @(Center) to @(BottomRight). We use a @(LayoutAnimation) with a @(Move) in order to make it move to its new position.

## $(MultiLayoutPanel)

$(MultiLayout) allows us to move @(Element:elements) between different @(Layout:layouts). This allows us to @(Move) elements between different locations in the visual tree, and also switch between certain layouts on the fly.

## $(Placeholder)
The `Placeholder` element is used to reference other @(Element:elements) in the visual tree. A `Placeholder` is itself an @(Element) and it can have its own layout properties.

The `Placeholder` is used when we want to define two different locations or layouts for the same @(Element). We use the placeholders `Target` property to reference the @(Element) we want to make a plceholder for. The @(Element) definition itself has to also be wrapped in a `Placeholder`. Think of it as an @(Element) being tossed from one `Placeholder` to another.

	<Placeholder Height="50">
		<Rectangle ux:Name="rectangle1" Fill="Red"/>
	</Placeholder>

	<Placeholder Target="rectangle1" Heigth="100"/>

Notice that the second `Placeholder` references the @(Rectangle), not the other `Placeholder`.

To switch between the two placeholder, we need to use @(MultiLayout). We can apply `MultiLayout` to any @(Panel) type or use the @(MultiLayoutPanel). Here is how we can apply the example above to a @(StackPanel):

	<StackPanel>
		<MultiLayout LayoutElement="placeholder2"/>
		<Placeholder Name="placeholder1" Height="70">
			<Rectangle Name="rectangle1" Fill="Red" Margin="25,5"/>
		</Placeholder>
		<Placeholder Name="placeholder2" Target="rectangle1" Height="100"/>
	</StackPanel>

By changing the `LayoutElement` property of the MultiLayout, we can move the @(Rectangle) between "placeholder1" and "placeholder2". Notice that the @(Rectangle) has its @(Margin) property set. Therefore that property will still exist when it's moved to the other @(Placeholder). The @(Height) however, is set on the @(Placeholder:placeholders) themselves, and will therefore change.


Here is a quick example, showing how we can move a @(Rectangle) from one @(Panel) to another. In this example we use a @(MultilayoutPanel) with a `DockLayout`. We put three @(Panel:panels) inside it, docket do different locations. We use @(Placeholder:placeholders) to reference one @(Rectangle) and move it between the three @(Panel:panels).

	<MultiLayoutPanel ux:Name="multiLayoutPanel">
		<DockLayout/>
		<Panel ux:Name="panel1">
			<Placeholder Width="50" Height="50">
				<Rectangle ux:Name="rect" Fill="Teal">
					<LayoutAnimation>
						<Move X="1" Y="1" RelativeTo="LayoutChange" Duration="1"/>
						<Resize X="1" Y="1" RelativeTo="LayoutChange" Duration="1"/>
					</LayoutAnimation>
				</Rectangle>
				<Clicked>
					<Set multiLayoutPanel.LayoutElement="panel2"/>
				</Clicked>
			</Placeholder>
		</Panel>
		<Panel ux:Name="panel2" Dock="Right" Width="200">
			<Placeholder Target="rect">
				<Clicked>
					<Set multiLayoutPanel.LayoutElement="panel3"/>
				</Clicked>
			</Placeholder>
		</Panel>
		<Panel ux:Name="panel3" Dock="Bottom" Height="100">
			<Placeholder Target="rect">
				<Clicked>
					<Set multiLayoutPanel.LayoutElement="panel1"/>
				</Clicked>
			</Placeholder>
		</Panel>
	</MultiLayoutPanel>

Check out these examples for some inspiration on using @(LayoutAnimation) and @(MultiLayout).
* [ScrollingAnimation](https://www.fusetools.com/developers/examples/scrollinganimation)
* [Tabbar using MultiLayout](https://www.fusetools.com/developers/examples/tabsmultilayout)
