# bulka

...is yet another bulk rename tool that lets you rename selected files in your `$EDITOR` / vi.

## Installation

Requires python 3.6+

Just grab [`bulka`](bulka) script and put somewhere in your `$PATH`, don't forget to `chmod +x` it.

## Usage

**`bulka -h`**:

1. run `bulka file1 file2 file3 ...` or e.g. `find ... | bulka`

2. your `$EDITOR` will open: edit names, save & close editor

then, if all _checks\*_ are passed, it will rename your files:
  1. to temporary names, to avoid conflicts like A -> B and B -> A at the same time
  2. from temporary to desired new names (creating missing folders if needed)


#### (\*) Checks that:
  1. there are no duplicates
  2. nothing will get overwritten
  3. no lines are missing
  4. names actually changed

#### Limitations:
  * doesn't check permissions beforehand

## Motivation

### lf

I use it as replacement for native `rename` command in [lf file manager](https://github.com/gokcehan/lf).

```sh
cmd bulk ${{
    bulka $(basename -a -- $fx)
    lf -remote 'send load'
    lf -remote 'send unselect'
}}

map r bulk
```

Why? Because:
1. I like vim
2. I _really_ don't like emacs style editing
3. I believe that `rename` and _bulk_ rename **shouldn't** be 2 different commands that use different editing hotkeys

But of course, `bulka` is perfectly usable on its own.

### So what about alternatives?

Why write your own? Sadly, none of them were satisfying to me.

* [**vidir**](https://github.com/madx/moreutils/blob/master/vidir) is good, but:
  * prefixes each line with a number which makes macros and `:norm` / `g` commands
harder to apply
  * by design **can and will delete your files, if you accidentally deleted some lines** (no, you can't turn it off)

* [**mmv**](https://github.com/itchyny/mmv/) is even better:
  * no prefixes
  * aborts if lines are missing
  * but it **can overwrite files not included in rename** without any warning, which is [not a bug](https://github.com/itchyny/mmv/issues/16).

* [**vimv**](https://github.com/thameera/vimv/):
  * smallest of them all
  * written in bash
  * no prefixes
  * aborts if lines are missing
  * can use `git mv`
  * but sometimes your files **will get silently overwritten** because:
    * it [doesn't handle](https://github.com/thameera/vimv/issues/39) cyclic renames (A -> B and B -> A)
    * it [doesn't check for duplicates](https://github.com/thameera/vimv/issues/38)

So yes, most of the cases here were me going _"wtf, where did that file go?"_.

Note, that GNU's `mv` by default will also overwrite files, the problem with the
above is that unlike with `mv`, you can't force it to check before overwriting.

_But there is another tool_:

* [**dmulholl/vimv**](https://github.com/dmulholl/vimv/) - same name, but has nothing to do with the above:
  * probably the most feature rich and better designed of them all:
    * can delete files, but you have to explicitly enable it
    * can overwrite files, but you have to explicitly enable it
    * can use `git mv`, but you have to explicitly enable it
    * supports cyclic renames
    * written in rust
  * if I’d known about it, I probably wouldn't bother with `bulka`

This is really good tool, and I suggest you to give it a try. `bulka`'s only
advantage compared to it is the fact that it is written in ubiquitous scripting
language, and you don't have to compile it (which is good for e.g. portable
configuration that you use across multiple architectures), and performance
wise - doesn't matter since renaming is just i/o bound anyway.
