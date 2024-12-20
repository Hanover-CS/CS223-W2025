# CS223-W2025
Site for Hanover-CS CS223 class for Winter 2025

Site is live [here](https://hanover-cs.github.io/CS223-W2025/)

## Codespace setup

```shell
bundle install
bundle exec jekyll serve
```

Other theme options [here](https://pages.github.com/themes/)

Some gotchas:

- Filenames can't have any weird characters in them or the links won't work. Had to change `CS223 Syllabus.md` to `CS223Syllabus.md` for example.
- "definition lists" like the one used in the syllabus don't work, as they are an extension to the normal markdown. Using `:` instead of `~` seems to work though.