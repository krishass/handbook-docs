# Handbook Writer's Guidelines

Make sure you read the [Markdown Extensions spec](Markdown%20Extensions.md) as well.

## Chronology

The handbook is a chronological document under the assumption that if I read from Chapter 1 and onwards,
I will learn or discover everything I need to know to understand the next chapter.

If I jump to chapter X and read something I don't understand, I should be able to find a definition,
explaination or link about that topic by scrolling back up.

## Tell a story

The handbook should explain the features of Fuse in the order they should be discovered and understood.

This means:

* Prefer simplicity and clarity over completeness and accuracy
* Focus on the concepts that are more likely to be interesting to people

For example, it is not important to communicate the class heirarchy or implementation details of Button.
Neither is it important to state the obvious - make it clear from context instead.

Focus on how a Button is used and *how* you acheive the most common tasks with it.

	## $(Button)

	Buttons are clickable @(Control:controls) that takes their look and feel from the @(Theme).

	(pictures of android, ios and some custom GL-based buttons side by side)

	This is how you handle a clicked event:

		<Button Clicked="{someFunction}" />

	> ### Tweaking the look and feel

	When using the native themes, `Button` are mapped to real native OS buttons.

	This means a button can look like many different things depending on the platform and
	custom style settings.

	Changing the look and feel of a button must be considered in context of the @(Theme). For example, in iOS the native
	buttons are just blue text labels, while on Android they have backgrounds and complex behavior when
	interacted with.

	Most themes offer theme-specific properties for controling the built-in platform looks, for example:

		<Button iOS.Color="Yellow" />

	See also:
		* @(iOS Native Theme) documentation
		* @(Android Native Theme) documentation

	> ### Creating custom button designs

	If you want to make substantial changes to the look and feel of a button ...

## Structure

This repo contains one markdown file for each chapter. A chapter is a broad, high level topic.

Use subheaders and subsections to hide detail. Keep the most interesting stuff close to the main headers.

Use subheaders to sort the topic chronologically, with prerequisites and more generally interesting things
first.

Deeper subheader = more specific / less likely to be interesting.

The deepest subheaders that contain verbose information should be hidden with a "fold" notation, like this:

	> ### Very specific things about Button


This will show up as a collapsed section in the docs, where only the header is visible.

Very deep/specific information or stand-alone articles should not be in the handbook at all, but pointed
to with external links.

Outline structure:

	# Chapter

	Intro to the topic here

	Links to external prerequisites:

	* Link
	* Link

	## Subchapter


    ### Subchapter (consider folding)

    > #### Subchaper (prefer folding)

	## Further reading
	* Links to external articles


## Add lots of code examples

The best way to learn is by example. Make sure your content is backed by actual code showing how its used in practice. The snippets dont have to compile on their own, but should be copy pastable into any project (if possible).

## Language entities

Many things map to language entities like classes, properties and enums. The handbook contains the main
go-to location for each language entity in the public facing UX API.

This means that all classes available in UX that we want people to be able to discover must have a
$(ClassName) somewhere in the handbook (written as used in UX, not with full namespace path).

The docs should foucs on the most used properties and their allowed values, but does not need to provide
exhaustive docs.

## Rules of thumb:

* Don't use XML brackets in headers, but use `code notation` around things that are XML tags in headers
