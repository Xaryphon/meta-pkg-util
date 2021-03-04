# meta-pkg-util

A python script to create meta packages and repos easier(*-ish*) for Arch (btw)

# Dependencies

`# pacman -S python python-zstandard python-gnupg`

# Usage
`meta-pkg-util --config <path/to/config.json>`

# Config File
## Example
### config.json
```jsonc
{
    // Declare user variables to use in other places
    // optional
    "variables": {
        // $cfgdir expands to the directory the config file is in
        "IN": "$cfgdir",
        "OUT": "$cfgdir/out",
        "KEY": "<pgpkeyhere>"
    },

    // Declare defaults for all repositories
    // Keys will be explained later
    // optional
    "repository_defaults": {
        "infile": "$IN/$name.json",
        "outdir": "$OUT/$name",
        "signkey": "$KEY"
    },

    // Declare defaults for all packages
    // Keys will be explained later
    // optional
    "package_defaults": {
        "packager": "Example <example@example.com>",
        "signkey": "$KEY"
    },

    // Declare repositories
    // required
    "repositories": [
        {
            // Name of the repository
            // Declares the variable $name
            // Will not inherit from repository_defaults
            // Required
            "name": "example",

            // JSON file based on which the repository is built with
            // Like most keys would've been inherited from reposity_defaults
            // but let's override it anyways
            // Required
            "infile": "$IN/$name.json",

            // The directory where all generated files will be placed in
            // Required
            "outdir": "$OUT/$name",

            // PGP key id used to sign the repository
            // NOTE: Packages are not signed with this key
            // Optional
            "signkey": "$KEY",

            // Defaults for all packages
            // Values will override the ones from the global package_defaults
            // Keys will be explained in example.json
            // Optional
            "package_defaults": {
                "url": "https://github.com/Xaryphon/meta-pkg-util"
            }
        }
    ]
}
```
### example.json
```jsonc
{
    // Default values for packages
    // Got left from early developement but no reason to remove it
    // Override config.json package_defaults
    // Optional
    "_default_": {
        "groups": "example-meta-packages"
    },

    // Defines a package named example-metapackage
    // Supports some of the PKGBUILD variables
    // At the time of writing the following variables are supported
    //     pkgdesc -> description
    //     url, replaces, groups, depends, optdepends,
    //     provides, conflicts, replaces
    // Also the following key is supported
    //     signkey, packager
    // All keys are optional
    "example-metapackage": {
        "description": "A meta package for anything the user could ever ask for",
        "depends": ["base", "linux", "nano"],
        "conflicts": ["vim", "nano"],

        // NOTE: in this example signkey would be inherited from
        // config.json package_defaults likewise for packager and url
        // but let's override them anyways

        // The packager just like url not really used by pacman just displayed
        // Optional
        "packager": "Example <example@example.com>",

        // PGP key id used to sign the package
        // Optional
        "signkey": "<keyidhere>"
    }
}
```
