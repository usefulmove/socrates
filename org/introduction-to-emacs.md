Doom is a configuration of [GNU Emacs](https://en.wikipedia.md/wiki/GNU_Emacs) that relies heavily on evil-mode (Vim emulation) consistently throughout the interface. As a result, it operates as a hybrid of Vim and Emacs. In general, Emacs is unbelievably customizable and easy to tailor to your needs.

# Basic Keybindings and Navigation

- efficient keyboard-based text editing
- modal editor (normal mode, insert mode, visual mode)
- cursor (point) movement with j, k, h, l
- jump between words and sentences
- search within files
- bulk search and replace
- jump between open documents (buffers)

# Integrated Environment

- edit multiple documents in a single session
- terminal (term)
- directory navigation (dired)
- scratch pad
- git integration (magit)

# Org Mode

- document editing and organization (similar to an interactive Markdown)
- interactive tables
- links between <span class="spurious-link" target="~/repos/enc/src/enc.el">*documents*</span> and out to online [sites](https://chat.openai.com/?model=gpt-4)
- export org documents to various formats including HTML, PDF, and more.
- embed code blocks with support for multiple programming languages

``` commonlisp
(load-file "~/repos/cora/src/cora.el")

(_thread 5
  'sqrt
  (lambda (n)
    (- n 1))
  (lambda (n)
     (/ n 2)))

(/ (- (sqrt 5)
      1)
   2)
```

| title        | role                 | worth it? |
|--------------|----------------------|-----------|
| Senior       | Director of Assholes | def. not  |
| Longer title |                      | whatever  |
|              |                      |           |

# Emacs Lisp

- Emacs built in C (30%) and Emacs Lisp (70%)
- extendable using Emacs Lisp
- example: enc
- example: custom keybinding
