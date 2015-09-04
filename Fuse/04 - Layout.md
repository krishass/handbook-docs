# $(Layout)

Fuse has a powerful layout system that works across all platforms and devices, whether you
are building with @(NativeTheme:native) elements or @(GraphicsTheme:graphics) based elements.

> #### Video introduction to layout

<div class="embed-responsive embed-responsive-16by9">
  <iframe width="420" height="315" class="embed-responsive-item" src="https://www.youtube.com/embed/x6CueKzTK68"></iframe>
</div>

<div class="embed-responsive embed-responsive-16by9">
  <iframe width="420" height="315" class="embed-responsive-item" src="https://www.youtube.com/embed/0hpL4KBfUy8"></iframe>
</div>

## $(Panels)

Panels can contain child UI elements and lay them out according to layout rules. There are several types of panels, each with different layouting rules.

For a good introductory example to basic layout, take a look [here](https://www.fusetools.com/developers/examples/layoutexample).

## $(Panel)
The most basic type of panel is the `Panel`. Children of a Panel will be default fill its entire space. If a panel contains several children, it simply layers them on top of each other. Combining this behavior with @(Alignment:alignment), @(Margin:margin) and @(Padding:padding) can be quite useful in many situations.

```
<Panel>
	<Text>This...</Text>
	<Text>...will be on top of this</Text>
	<Rectangle Alignment="BottomLeft" Height="20" Width="20" Fill="#678" />
</Panel>
```

Note that the element order in a `Panel` is the same as the layer order in popular graphics packages such as Photoshop; the layer that appears first in the UX-file will be layered on top of elements appearing later in the file.

## $(StackPanel)
The StackPanel places its children in a stack. The default layout is a vertical stack, but one can use the $(Orientation) property to specify that the stack should be layed out horizontally.

```
<StackPanel Orientation="Horizontal">
	... elements ...
</StackPanel>
```

## $(Grid)
The Grid places its children in a grid formation. The rows and columns can be specified explicitly by the @(Rows) and @(Columns) properties, or implicitly by assigning the @(RowCount) and @(ColumnCount) properties.

### $(RowCount) and $(ColumnCount)
If all that is needed is a grid of equally sized rows and columns, one can simply state the number of rows and columns using the RowCount and ColumnCount properties.
```
<Grid RowCount="4" ColumnCount="2"/>
```
### $(Rows) and $(Columns)
More fine grained control of how the rows and column sizes are calculated can be achieved with the Rows and Columns properties. These properties are assigned to a comma separated list of values which can take on a few different forms.
The values can either be absolute, relative or automatic.

Example of a Grid with 3 rows of size 10, 10 and 50 points.
```
<Grid Rows="10,10,50"/>
```

Example of a Grid with 3 rows where the first two each occupy 20% of the available space, and the last one occupies 60%:
```
<Grid Rows="1*,1*,3*"/>
```
The rows sizes here are calculated by first summing all the values (1+1+3 = 5).
Then we divide our value by the total (1/5 = 20%, 1/5 = 20%, 3/5 = 60%).

The following grid has 3 rows where the first two rows gets the size of its largest child and the last row takes up the remaining space:
```
<Grid Rows="auto,auto,1*"/>
```

### $(Grid.Row:Placing elements in a Grid) $(Grid.Column:)
By default, elements are placed in the grid by the order they appear in the UX, from left to right, top to bottom. One can specify per element which grid cell they should be placed in using the Row and Column like so:
```
<Grid RowCount="1" ColumnCount="2">
	<Rectangle Row="0" Column="1" Fill="Red"/>
	<Rectangle Row="0" Column="0" Fill="Blue"/>
</Grid>
```

## $(WrapPanel)
The `WrapPanel` lays out its children one after the other and wraps around whenever it reaches the end. One can specify which direction the elements are layed out in by assigning the $(FlowDirection) property. FlowDirection can either be `LeftToRight` or `RightToLeft`.

The following WrapPanel lays out its children horizontally from right ro left.
```
<WrapPanel FlowDirection="RightToLeft">
	<Each Count="10">
		<Rectangle Margin="5" Width="100" Height="100" Fill="Blue"/>
	</Each>
</WrapPanel>
```

The Orientation property can be used to make a vertical `WrapPanel` like so:
```
<WrapPanel Orientation="Vertical">
	<Each Count="10">
		<Rectangle Margin="5" Width="100" Height="100" Fill="Blue"/>
	</Each>
</WrapPanel>
```

<!-- TODO: Illustration -->

## $(DockPanel)
The DockPanel lays out its children by docking them to the different sides, one after the other. One can specify which side per element by using the $(Dock) property like so:
```
<Rectangle Dock="Left"/>
```
The Dock property can be assigned to be either `Left`, `Right`, `Top`, `Bottom` or `Fill` (which is the default).

```
<DockPanel>
	<Style>
		<Rectangle MinWidth="100" MinHeight="200"/>
	</Style>
	<Rectangle Fill="Red" Dock="Left"/>
	<Rectangle Fill="Green" Dock="Top"/>
	<Rectangle Fill="Blue" Dock="Right"/>
	<Rectangle Fill="Yellow" Dock="Bottom"/>
	<Rectangle Fill="Teal" />
</DockPanel>
```

The @(Style) is used to give the @(Rectangle:rectangles) a minimum size. The @(Rectangle) do not have any explicit default size, so when the `DockPanel` places them, it tries to use their minimum size, except for when `Dock` is set to `Fill`.

> ## $(Viewbox)

To make contents stretch to fit an area, you can use `Viewbox`:

	<Viewbox>
		<Rectangle Background="#808" Width="200" Height="100" />
	</Viewbox>

This will maintain its aspect ratio of 2:1 while stretching the `Rectangle` to be the size of the `Viewbox`.

You can set which directions you want the content to scale by setting the `StretchDirection`-property:

- `Both` - Allow both up- and downscaling
- `UpOnly` - Only upscale contents
- `DownOnly` - Only downscale contents

Note that any other setting than `DownOnly` might create pixel artifacts, as the `Viewbox` performs a bitmap stretch of its contents.

You can also set the @(StretchMode) for the contents, which defaults to `Uniform`.

## Element Layout

<!-- TODO: Link to video -->

Whenever a @(Panel) performs layout on its children, it has to ask each element about its layout properties.

Layout properties are assigned per element to control such things as the elements @(Width), @(Height), @(Margin) and @(Padding).
If an element doesn't specify these things, the panel performing layout on them will handle it.

<!-- TODO: Available space, @(points) (vs @(pixels)). -->

### $(Alignment)

When elements are positioned in a panel they may not require all of the space available to them. For example, a vertical stack panel will be as wide as its largest element, leaving extra space for the smaller elements. Elements can either be aligned within this space, or stretched to fill it.

We can align elements to the sides of its container by the @(Alignment) property.
In the following example, we have a @(Panel) which is 500x500 points in size, which contains a @(Rectangle) which is only 50x50 points in size. By setting alignment to BottomRight, this rectangle will be positioned by the bottom right corner of the panel.

```
<Panel>
	<Rectangle Width="50" Height="50" Alignment="BottomRight"/>
</Panel>
```

Alignment can be assigned to any one of the following values:
- $(Left)
- $(HorizontalCenter)
- $(Right)
- $(Top)
- $(VerticalCenter)
- $(Bottom)
- $(TopLeft)
- $(TopCenter)
- $(TopRight)
- $(CenterLeft)
- $(Center)
- $(CenterRight)
- $(BottomLeft)
- $(BottomCenterter)
- $(BottomRight)

If you don't assign an `Alignment` explicitly, the default alignment will be to stretch the control as much as the parent control requests. For a normal `Panel`, this means that the child control will try to fill the parent `Panel`, but other containing controls might ask the children to behave differently.

### $(Visibility)
The `Visibility` property can be used to `Show`, `Hide` or `Collapse` elements. It can be set to one of the following values:
- $(Visible) - The default state where the element is visible.
- $(Hidden) - The element is not drawn, but it still takes up space.
- $(Collapsed) - The element is not visible and is will not take up any space.

In the following example, only two of the @(Rectangle:rectangles) are visible. The second @(Rectangle) is collapsed, so it's not taking up any space at all. The third @(Rectangle) is hidden, so it takes up space, but is not visible. The last @(Rectangle) has no `Visibility` set, so it defaults to being visible.

```
<StackPanel>
	<Rectangle Visibility="Visible" Fill="Red" Height="50"/>
	<Rectangle Visibility="Collapsed" Fill="Green" Height="50"/>
	<Rectangle Visibility="Hidden" Fill="Blue" Height="50"/>
	<Rectangle Fill="Yellow" Height="50"/>
</StackPanel>
```

### $(Width) and $(Height)

You can combine @(Alignment) and other layout properties with `Width` and `Height`, which will set the size of the @(Element) in the desired @(Units:Unit).

### $(Margin) and $(Padding)
Each element can specify the amount of space between it and its parent or surrounding siblings by using its @(Margin) property.

Each element can also specify how much space should be between its borders and any element inside it by using the @(Padding) property.

Both @(Margin) and @(Padding) are specified using a comma separated list of 4 values which defines the left, top, right and bottom values in that order.
Here is an example of a @(Rectangle) with a margin of 20 to the left, 30 to the top, 50 to the right and 10 to the bottom:
```
<Rectangle Margin="20,30,50,10"/>
```
Here is a rectangle where all sides have the same margin:
```
<Rectangle Margin="50"/>
```
This rectangle has a margin of 50 for its left and right, and 20 for its top and bottom:
```
<Rectangle Margin="50,20"/>
```

### $(Units)
There are multiple ways of specifying values on layout properties, points, percent and pixels.
The following properties support units
- @(Width)
- @(Height)
- MinWidth
- MinHeight
- MaxWidth
- MaxHeight
- @(Offset)
- @(Anchor)

* Note that the column and row properties of @(Grid) use their own @(Rows:system).

### Specifying units in $(points)
When setting an elements Width to a number like so `<Element Width="50"/>`, the element will become 50 points wide.

Points are different from pixels in that they will represent multiple pixels on high density displays. This way, using points will give a you a consistent look on all different screen densities.

### Specifying units in $(Percentage:percent) (%)
Specifying values using the percent sign means that this value should be a certain percentage of available space.
For example, if we place an @(Element) inside a @(Panel) which is 500 points wide, and set the elements Width property to be 50%, we get an element which is 250 points wide.

### Specifying units in $(Pixels:pixels)
We can specify values in pixels using the px suffix like so:
```
<Rectangle Width="200px"/>
```
This rectangle will be exactly 200 pixels wide, which means it will be smaller when the screen density is high.

### $(Anchor) and $(Offset)
When a @(Panel) places its children, it assumes that the "center" of that element is in the middle. However, if we want the element to be placed as if its "center" was along its left side, we could use the @(Anchor) property like so:
```
<Rectangle Anchor="0,50%"/>
```
This puts the elements anchor in the middle of its left edge.

## $(Absolute positioning) $(X:) $(Y:)

If we want to give our elements an explicit position, we can assign their `X` and `Y` properties. The `X` property will move the element relative to the left side of its container, while the `Y` property moves it relative to the top.

Be aware that absolute positioning elements should generally be avoided in favor of using layout rules. This is because when real data is used, the absolute values used might no longer be meaningfull.


## Status bars

iOS and Android devices usually has a status bar aligned to the top of the screen which shows status information from the operating system like battery and network information. This status bar might or might not be visible while our app is running. We also might or might not be able to draw behind it. Android also often has an on-screen bar to handle navigation, and most devices show an on-screen keyboard when it accepts user input.

### $(StatusBarBackground)

The @(StatusBarBackground) element is used to compensate for the status bar. It will always have the same size as the status bar across all platforms and devices. We can use a @(DockPanel) to dock this element on the top of our app and "offset" the rest of our content so it fits within the visible parts of our screen.
```
<App>
	<DockPanel>
		<StatusBarBackground Dock="Top"/>
		<Panel>
			... our apps content ...
		</Panel>
	</DockPanel>
</App>
```

> ### Video introduction to OS elements

<div class="embed-responsive embed-responsive-16by9">
  <iframe width="420" height="315" class="embed-responsive-item" src="https://www.youtube.com/embed/S_syTU44jzw"></iframe>
</div>

### $(BottomBarBackground)
The BottomBarBackground element is quite similar to the @(StatusBarBackground) in that it takes on the same size as certain OS specific elements. The BottomBarBackground will take on the same size as the keyboard (whenever it is visible). Certain Android devices have their home button on the screen instead of as a physical button. The BottomBarBackground will also take this into account when sizing itself.
Here is how we can make sure our content is never covered by the keyboard or home button:
```
<App>
	<DockPanel>
		<Panel>
			... our apps content ...
		</Panel>
		<BottomBarBackground Dock="Bottom"/>
	</DockPanel>
</App>
```

You also have the option to not take the size of the keyboard into account:

	<BottomBarBackground IncludesKeyboard="false" />

> ### $(ClientPanel)

`ClientPanel` is a @(DockPanel) with both a @(StatusBarBackground) and a @(BottomBarBackground) docked to its top and bottom edges, respectively. It is included for convenience.