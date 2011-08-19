Smarty
------

Assetic can easily be integrated with Smarty with this Smarty plugin. Just drop this plugin in the Assetic/Extensions/Smarty folder.

First, you have to add Assetic/Extension/Smarty to your Smarty plugins_dir:

``` php
$smarty = new Smarty();
// ...
$smarty->plugins_dir[] = 'path/to/Assetic/Extension/Smarty';
```

A simple usage example:

``` html
{assetic 
    assets="style/reset.css,style/common.css,style/other.css" 
    output="css" 
    config_path="config" 
    build_path="style/build" 
    debug=false 
    filters="yui_css,less" 
    asset_url=asset_url}
    <link rel="stylesheet" href="{$asset_url}">
{/assetic}
```

Here is a list of the possible parameters:

* assets: A coma-separated list of files to include in the build (can be CSS or JS files)
* bundle: A bundle name. We will talk about it later. Incompatible with the "assets" parameter, you have to choose between the two.
* output: The kind of output you want, can be "css" or "js"
* config_path: The folder where the plugin will find the configuration files (we will talk about these ones later)
* build_path: The folder where the plugin will store your all-in-one asset
* debug: If set to true, the plugin will not combine your assets to allow easier debug
* filters: A coma-separated list of filters to apply. Currently, only LESS and YuiCompressor (both CSS and JS) are supported
* asset_url: The variable name that will be used to pass the asset URL to the &lt;link&gt; tag

There are 3 external files you need to provide:

config.json:

``` json
{
    "autoload_path": "/path/to/autoload.php",
    "yuicompressor_path": "/path/to/yuicompressor.jar",
    "java_path": "/usr/bin/java,
    "document_root": "/your/document/root"
}
```

Following your preferences, you can choose one of the 2 other files:

bundles.json allows you to simply tell which files you want to include in a bundle:

``` json
{
    "css":
    {
        "main":
        [
            "css/reset.css",
            "css/common.css",
            "css/other.css"
        ]
    }
}
```

This file would allow you to use the "main" bundle name in the "bundle" parameter rather than just giving the list of file names to the "assets" parameter

dependencies.json is a bit more complicated but allows you to benefit from the AssetReference Assetic's feature. Very useful for JS assets:

``` json
{
    "js":
    {
        "references":
        {
            "awesomelib": "js/awesome.lib.js",
            "anotherlib": "js/another.lib.js",
            "somejs": "js/just.some.js.file.js"
        },
        "assets":
        {
            "js/foo.js": ["awesomelib","anotherlib"],
            "js/bar.js": ["somejs"],
            "js/no.dependencies.js": []
        }
    }
}
```

This allows you to keep track of dependencies between JS files, which can be very handy. Then, you only specify the names of your "useful" JS files in the "assets" parameter. The plugin will take care of including only the necessary dependencies for you, without any duplicates.
So if you write "js/foo.js,js/no.dependencies.js" in the assets parameter, the plugin will combine js/awesome.lib.js, js/another.lib.hs, js/foo.js and js/no.dependencies.js into the build.
