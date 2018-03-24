Using ghci with stack on debian

First you will want to load stack from you local installation, so dont say `stack`, say `~/.local/bin/stack`.
better `export PATH=~/.local/bin/:$PATH`.

If you want to load a non exported function in ghci, use `:l *Module` [see guide](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/ghci.html).

For quasiquoting use [Text-RawString-QQ](https://hackage.haskell.org/package/raw-strings-qq-1.1/docs/Text-RawString-QQ.html)
add [`{-# LANGUAGE QuasiQuotes #-}`](https://www.schoolofhaskell.com/school/advanced-haskell/building-a-file-hosting-service-in-yesod/part%201)
