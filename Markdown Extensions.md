# Handbook markdown extensions

The markdown extensions allows us to easily link to concepts across the whole handbook.
There are two special directives which are used to create link targets and links.

**$(Foo)** defines where Foo is documented. Any links to Foo will point here.
Note that there can only be one such point in the entire handbook, so make sure you're not adding an existing label.

**@(Foo)** creates a link to **$(Foo)**.

**$(some-label:Foo bar)** defines where "Foo bar" is documented, but you can use label as a
shorthand in references, i.e.

* **@(some-label)** - renders <a>some-label</a>
* **@(some-label: the fancy Foo bar feature)** - renders <a> the fancy Foo bar feature</a`>

* The links names and labels are case insensitive.


```
> ## Title
```

Indicates a collapsible section where only the title is visible

# Planned features

Any code in `quotes` or indented blocks should automatically hotlink any words that
are documented.

If Foo is a proper name for an UX entity, the location of **$(Foo)** should display a right-aligned button
next to it that links to the corresponding Unodoc page.

The following syntax should also link to the corresponding Unodoc:

* @unodoc(Fuse.Controls.Button: see the full Button reference) - renders <a> see the full Button reference</a>
* When references to exhaustive docs are neded, use the @unodoc macro.
