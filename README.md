# `adsbib`

A simple bash script that uses standard unix utilities `sed`, `tr`,
`sort`, `uniq`, and `grep`, as well as `curl` and `jq`, to generate a
BibTex database automatically from a LaTeX "`[doc].aux`" file.

The syntax is simply

    adsbib [doc].aux

It also checks for "`[doc].map`" and uses it as a key map.

Because ADS now requires API keys, user should provide their keys in
the "`~/.adsbibrc`" file.
