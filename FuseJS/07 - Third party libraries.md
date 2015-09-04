# $(Third party libraries)

Fuse is working hard to make it possible to reuse exiting JavaScript libraries from third parties. To make more third party
libraries work, we are constantly growing and revising our $(Polyfills) to improve compatibilit.

This chapter contains a list of libraries that are known to work or known to not work, and explains workarounds/caveats.

If you have tested a library and found it to work/not work/a work-around, please [share your experience on our forums](https://www.fusetools.com/community/forums) or send an email to support@fusetools.com.

> This list will be expanded as FuseJS develops.

## Working

The following third party JavaScript libraries are tested and known to work

* [Parse SDK](https://parse.com/docs/js/guide)
* [Moment.js](http://momentjs.com)

<!-- TODO: Add this section as we uncover incompatible libraries
## Known incompatibilities

(nothing yet)
-->


# $(Parse:Integrating Parse)

Download the latest Parse JS SDK [here](https://www.parse.com/docs/downloads).

To be able to use Parse in our JavaScript code, we need to import it as a global resource.

```
<JavaScript File="latest-parse.js" ux:Global="Parse"/>
```

Place this in the top level of your UX markup. Parse can now be used from any of your JavaScript files using `require('Parse').Parse`.

Create a Parse App from Parse' website by going to your dashboard and clicking "Create a new app". Go to the Keys tab under settings and find your "Application ID" and "JavaScript Key". These keys are used to identify you app using `Parse.initialize(appID, jsKey)`.

```
<JavaScript>
	var Parse = require('Parse').Parse;
	Parse.initialize(appID, jsKey);
</JavaScript>
```

You are now ready to start using parse. If you are new to Parse, take a look at their excellent [guide](https://www.parse.com/docs/js/guide).
For a more complete example of using Parse as a backend for Fuse, take a look [here](https://www.fusetools.com/community/examples/todoparseexample).
