# api documentation for  [nodepdf (v1.3.2)](https://github.com/TJkrusinski/NodePDF)  [![npm package](https://img.shields.io/npm/v/npmdoc-nodepdf.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-nodepdf) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-nodepdf.svg)](https://travis-ci.org/npmdoc/node-npmdoc-nodepdf)
#### Down and dirty PDF rendering in Node.js

[![NPM](https://nodei.co/npm/nodepdf.png?downloads=true)](https://www.npmjs.com/package/nodepdf)

[![apidoc](https://npmdoc.github.io/node-npmdoc-nodepdf/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-nodepdf_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-nodepdf/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-nodepdf/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-nodepdf/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "TJ Krusinski",
        "email": "tjkrus@gmail.com"
    },
    "bugs": {
        "url": "https://github.com/TJkrusinski/NodePDF/issues"
    },
    "contributors": [
        {
            "name": "Steffen Persch",
            "email": "@n3o77"
        }
    ],
    "dependencies": {
        "shell-quote": "1.4.x"
    },
    "description": "Down and dirty PDF rendering in Node.js",
    "devDependencies": {
        "mocha": "*"
    },
    "directories": {},
    "dist": {
        "shasum": "d525b3e22bc34a01dac89e94411cb7bc87e4c6be",
        "tarball": "https://registry.npmjs.org/nodepdf/-/nodepdf-1.3.2.tgz"
    },
    "engines": {
        "node": "*"
    },
    "gitHead": "3553505b3c3e3261a049fd6edcde853f2a602d8e",
    "homepage": "https://github.com/TJkrusinski/NodePDF",
    "keywords": [
        "phantomjs",
        "pdf",
        "make a pdf"
    ],
    "license": "MIT",
    "main": "./index.js",
    "maintainers": [
        {
            "name": "tjkrusinski",
            "email": "tj@futura.io"
        }
    ],
    "name": "nodepdf",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/TJkrusinski/NodePDF.git"
    },
    "scripts": {
        "test": "mocha tests"
    },
    "version": "1.3.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module nodepdf](#apidoc.module.nodepdf)
1.  [function <span class="apidocSignatureSpan">nodepdf.</span>render (address, file, options, callback)](#apidoc.element.nodepdf.render)
1.  object <span class="apidocSignatureSpan">nodepdf.</span>child

#### [module nodepdf.child](#apidoc.module.nodepdf.child)
1.  [function <span class="apidocSignatureSpan">nodepdf.child.</span>exec (url, filename, options, cb)](#apidoc.element.nodepdf.child.exec)
1.  [function <span class="apidocSignatureSpan">nodepdf.child.</span>supports (cb, cmd)](#apidoc.element.nodepdf.child.supports)



# <a name="apidoc.module.nodepdf"></a>[module nodepdf](#apidoc.module.nodepdf)

#### <a name="apidoc.element.nodepdf.render"></a>[function <span class="apidocSignatureSpan">nodepdf.</span>render (address, file, options, callback)](#apidoc.element.nodepdf.render)
- description and source-code
```javascript
render = function (address, file, options, callback) {
  var filePath = process.env.PWD || process.cwd() || __dirname;

  if (typeof options == 'function') {
    callback = options;
    options = defaults;
  };

  options = merge(defaults, options);

  child.supports(function(support){
    if (!support) callback(new Error('PhantomJS not installed'));

    child.exec(address, file, options, function (ps) {
      ps.on('exit', function(c, d){
        if (c) return callback(new Error('Conversion failed with exit of '+c));

        var targetFilePath = file;

        if (targetFilePath[0] != '/')
          targetFilePath = filePath + '/' + targetFilePath;

        return callback(null, targetFilePath);
      });
    });
  });
}
```
- example usage
```shell
...

The callback API follows node standard callback signatures using the 'render()' method.

'''' javascript
var NodePDF = require('nodepdf');

// options is optional, sets the width and height for the viewport to render the pdf from. (see additional options)
NodePDF.render('http://www.google.com', 'google.pdf', options, function(err, filePath){
	// handle error and filepath
});

// use default options
NodePDF.render('http://www.google.com', 'google.pdf', function(err, filePath){
	// handle error and filepath
});
...
```



# <a name="apidoc.module.nodepdf.child"></a>[module nodepdf.child](#apidoc.module.nodepdf.child)

#### <a name="apidoc.element.nodepdf.child.exec"></a>[function <span class="apidocSignatureSpan">nodepdf.child.</span>exec (url, filename, options, cb)](#apidoc.element.nodepdf.child.exec)
- description and source-code
```javascript
exec = function (url, filename, options, cb){
  var key;
  var stdin = ['phantomjs'];
  var optsFile = __dirname + '/' + Date.now() + '.js';

  stdin.push(options.args);
  stdin.push(shq([
    __dirname+'/render.js',
    url,
    filename,
    optsFile,
  ]));

  fs.writeFile(optsFile, 'module.exports='+JSON.stringify(options), 'UTF-8', function(err) {
    if(err) {
      return console.log(err);
    }

    var c = child.exec(stdin.join(' '));
    c.on('exit', function () {
      fs.unlinkSync(optsFile);
    });

    c.on('error', function () {
      fs.unlinkSync(optsFile);
    });
    cb(c);
  });
}
```
- example usage
```shell
...
 *  Run the process
 *
 *  @method run
 */

Pdf.prototype.run = function() {
  var self = this;
  child.exec(this.url, this.fileName, this.options, function (ps) {
    ps.on('exit', function(c, d){
if (c != 0) return self.emit('error', 'PDF conversion failed with exit of '+c);

var targetFilePath = self.fileName;

if (targetFilePath[0] != '/') {
  targetFilePath = self.filePath + '/' + targetFilePath;
...
```

#### <a name="apidoc.element.nodepdf.child.supports"></a>[function <span class="apidocSignatureSpan">nodepdf.child.</span>supports (cb, cmd)](#apidoc.element.nodepdf.child.supports)
- description and source-code
```javascript
supports = function (cb, cmd) {
  var stream = child.exec(which+' '+(cmd || 'phantomjs'), function(err, stdo, stde){
    return cb(!!stdo.toString());
  });
}
```
- example usage
```shell
...
  var self = this;

  this.url = url;
  this.fileName = fileName;
  this.filePath = process.env.PWD || process.cwd() || __dirname;
  this.options = merge(defaults, opts);

  child.supports(function(support){
    if (!support) self.emit('error', 'PhantomJS not installed');
    if (support) self.run();
  });

  return this;
};
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
