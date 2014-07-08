# json-cmd

`json-cmd` used to be called `json-command`, and then it was called `json`, and then it was called `json-command` again and there was a totally different package called `json` on npm, and there was much confusion. Like, who names their package `json`? Worse, who replaces a package on npm with a similar package using the same bin name, but a completely different API?

If you want something faster and more powerful, use [jq](http://stedolan.github.io/jq/).
Unfortunately, `jq`'s syntax is often like trying to use awk when all I need is grep. `json-cmd` is quick and dirty.


## Installation

    npm install -g json-cmd

The remainder of this README is derived primarily from @zpoley's original [`json-command`](https://github.com/zpoley/json-command).


## Synopsis

    json [options] [fields]


## Options

    -h                    print help info and exit
    -v (-V | --version)   print version number and exit
    -u                    print ugly json output, each object on a single line
    -d                    print debugging output including exception messages
    -o object.path        specify the path to an array to be iterated on
    new.key=old_key       move old_key to new.key in output object
    -a                    input object is an array, process each element separately
    -c "js conditional"   js conditional to be run in the context of each
                          object that determines whether an object is printed
    -C                    print the output fields as tab delimited columns in
                          the order specified
    -e "js expression"    execute arbitrary js in the context of each object.
    -i                    use node's util.inspect instead of JSON.stringify.
    -H                    print headers, if they are supplied. Useful for
                          output from curl -i.


## Examples

Following examples parse json output from twitter http requests and output the
requested json fields.

    curl http://search.twitter.com/search.json?q=node.js | json

    curl http://search.twitter.com/search.json?q=node.js | json -o results

    curl http://search.twitter.com/search.json?q=node.js | json -o results from_user metadata

    curl http://search.twitter.com/search.json?q=node.js | json -o results new_id=id

    curl http://search.twitter.com/search.json?q=node.js | json -o results -C from_user from_user_id

Note that the last two examples require you to enter your username and password.

    curl http://stream.twitter.com/1/statuses/sample.json -uAnyTwitterUser:Password | json user.name user.id

    curl http://stream.twitter.com/1/statuses/sample.json -uAnyTwitterUser:Password | json user.name user.id -c "entities.user_mentions.length > 0"


## Fields

Any number of fields can be specified to be printed from each json object.
By default the structure of the original json object is maintained, however options
like -e and foo=bar allow for transforming object structure.

To remove a particular key, assign it to undefined using the -e flag.


## Standard Input Formats

* line delimited json objects
* back to back json objects.
    e.g. { obj1 : body }{ obj2 : body }
* file separated json objects.
    e.g. cat files/* | json
* when using -a, a single JSON array


## Order of operations

1. objects are parsed from stdin
2. any non-existing requested keys are instantiated
3. key transforms are applied to the object
4. expressions are run against the object
5. conditionals are checked against the object. the object is discarded if conditionals do not pass
6. the requested keys are pulled from the object and output


## Limitations

* All input is handled through standard in
* The current version is not particularly slow, but is NOT optimized for speed in any way


## Documentation

The npm package includes a man page that can be accessed using:

    man json


## Thanks

This project respectfully uses code from and thanks the authors of:

* [node](http://github.com/ry/node)
* [npm](http://npmjs.org/)
