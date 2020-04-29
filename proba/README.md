
# Teszt

Itten tesztelodik az, hogy az ipynb-bol generalt markdown fajlokban a) megmaradnak-e a latex-es reszek, ill. b) hogy ezeket megjeleniti-e a github rendesen.

Sor szintu keplet: \\(3+4=7\\)

Keplet kulon sorban:

\\[3+4=7\\]

### Test

quntoken.**tokenize**(*inp=sys.stdin, form='tsv', mode='token',
word_break=False*)

<div style="text-indent: 2em;">
Entry point, returns an iterator object. Parameters:

*inp*: Input iterator, default: *sys.stdin*.

*form*: Format of output. Valid formats: `'tsv'` (default), `'json'`, `'xml'`
and `'spl'` (sentence per line).

*mode*: `'sentence'` (only sentence segmenting) or `'token'` (full
tokenization - default).

*word_break*: If `'True'`, eliminates word break from end of lines. Default:
`'False'`.
</div>

Example:

```py
from quntoken import tokenize

for tok in tokenize(open('input.txt')):
    print(tok, end='')
```

