# Rename .js to .tsx

**I suggest using [react-javascript-to-typescript-transform](https://github.com/lyft/react-javascript-to-typescript-transform). It's a smart tool for migrating from React with JavaScript to React with TypeScript which also maps `propTypes` to TypeScript `type`.**

---

This is a short script, which will rename all `.js` files to `.tsx` (TypeScript with JSX support).

```
Usage: rename-to-tsx [option]
 -h (help): Print this help message.
 -a (all): Rename files ending in .js and .jsx to .tsx.
 -r (respectively): Rename files ending in .js to .ts and .jsx to .tsx.
```

>  *Never execute scripts unless you know what will they do!*

Since this code is changing your file names and might not work correctly, **make sure to use source control or back up your work before running the script**.

Explanation of the script:

`find .`: find all files in current working directory (`.`) and recursive in all subfolders. Arguments:

 - `-type f`: Match only file types (ignores directories etc.).
 - `\( -iname '*.js' -or -iname '*.jsx' \)`: Use case insensitive name matching, match files ending in `.js` or `.jsx`. Note that surrounding brackets have to be escaped to be interpreted correctly.
 - `-not -wholename '*node_modules*'`: Match `node_modules` in whole path, negate matches, which means any path including `node_modules` will be ignored.
 - `-exec sh -c '[rename script]' _ {} \;`: will call given *rename script* as shell script, passing the full file path (`{}`) as the first argument (`$1`).

Renaming script when using `-a`:

 - `'mv "$1" "${1%.js*}.tsx"'`: Execute a `sh` command and pass it `{}`, which is current file path, as first argument (`$1`). The script uses `mv` (move files) command to move `$1` (found file [relative] path) to the same path, just with replaced `.js*` (`.js` or `.jsx`) extension with `.tsx` (TypeScript React). If the terminology *move* confuses you, don't worry, it's not really moving the files anywhere, just renaming them.

Renaming script when using `-r`:

  - ``'mv "$1" `sed -re "s/\.js(x)?$/\.ts\1/g" <<< "$1"\`'``: Command `sed` (stream editor) is used to replace matching regex `\.js(x)?$` (`.js` or `.jsx` at end of string) with `.ts(x)`, where the `x` is only added if it existed in old extension.

## As terminal commands

Instead of downloading and running the script, you can copy one of the scripts below directly into the terminal.

Same as `rename -a` (rename both `.js` and `.jsx` to `.tsx`):

``` sh
find . -type f \( -iname '*.js' -or -iname '*.jsx' \) -not -wholename '*node_modules*' -exec sh -c 'mv "$1" "${1%.js*}.tsx"' _ {} \;
```

Same as `rename -r` (rename `.js` and `.jsx` to `.ts` and `.tsx`, respectively):

``` sh
find . -type f \( -iname '*.js' -or -iname '*.jsx' \) -not -wholename '*node_modules*' -exec sh -c 'mv "$1" `sed -Ee "s/\.js(x)?$/\.ts\1/g" <<< "$1"`' _ {} \;
```

# Contributing

If you find a problem with using this script or have an idea which you believe will improve this script, share it as an issue on this repo.

If you're feeling extra helpful, pull requests are welcome!

## [License (MIT)](LICENSE.md)
