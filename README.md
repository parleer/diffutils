# diff with support for excluding directories

The standard GNU `diff` tool does not allow exclusion of specific directories when doing a recursive comparison. 

Say you have the following directory structure that contains two directories named `d` at different paths within `a/` (i.e. `a/c/d` and `a/d`):

```
a/
  c/
    d/
      txt
  d/
    txt
  txt
b/
  c/
    d/
      txt
```

A standard recursive  shows 3 changes:

```
diff -r a b
```
```
Only in a/c/d: txt
Only in a: d
Only in a: txt
```

In GNU `diff` there's no way to exclude ONLY `a/d` from the comparison. Trying to exclude `d` and will exclude both `a/d` and `a/c/d`:

```
diff -r --exclude=d a b
```
```
Only in a: txt
```


## The solution provided by this patch

This patch adds the option `--exclude-directory` which changes the way exclude patterns are matched by the `--exclude=PAT` or `--exclude-from=FILE` options. Instead of only matching the filename or directory name, the pattern is matched against the entire path. In the example folder structure above, you can exclude `a/d` but NOT `a/c/d` using options `--exclude-directory --exclude=a/d`:
```
diff -r --exclude-directory --exclude=a/d 
```
```
Only in a/c/d: txt
Only in a: txt
```




## Building from source

### Linux/Unix

Follow instructions in `INSTALL`

### Mac

Install GNU build environment pre-requisites. (The following commands use [MacPorts](https://www.macports.org/))

```
sudo port install autoconf
sudo port install automake
sudo port install help2man
```

Then go through standard GNU build process. If you're on a newer Mac running CLang, rather than GNU GCC, you'll need to disable certain warnings in your `./configure` step:

```
./bootstrap
./configure CFLAGS='-Wno-cast-align -Wno-string-plus-int -Wno-missing-braces'
make
make install
```

## Thanks

1. [How can I make 'diff -X' ignore specific paths and not file names?](https://superuser.com/questions/644680/how-can-i-make-diff-x-ignore-specific-paths-and-not-file-names) 
1. [bug#23596: Add exclude directories feature
](http://lists.gnu.org/archive/html/bug-diffutils/2016-05/msg00008.html) 
