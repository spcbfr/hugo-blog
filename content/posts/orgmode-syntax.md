+++
title = "A noob's guide into the orgmode syntax"
description = "Getting started with org-mode"
date = 2021-11-01
tags = ["orgmode"]
draft = false
author = "Youssef Bouzekri"
cover = "/img/orgmode-syntax-basics.png"
+++

So you've been hearing a lot about emacs's killer feature, orgmode and how powerful and customizable it is, so you decided to give it a try. you download doom emacs but then get stuck because you are unsure of how to start. In this guide I'll demonstrate how to use orgmode and how to get the most out of it.


## Headings {#headings}

Unlike markdown where you would use the pound sing # to set headings, in orgmode we use the asktrisk sign followed by a space, this would only work if you have the asktrisk at the start of the line, as per other markup languages 1 astrisk means a heading 1, 2 astrisks means a heading 2,etc..
here is how headings look in action

{{< figure src="/img/org-headings.png" alt="Headings Example" position="center" style="border-radius: 8px;" caption="Figure 1: How headings look in action" >}}

as you can see, headings are automatically indented, and astrisks are replaced with unicode characters using the [Org Superstar package](https://github.com/integral-dw/org-superstar-mode).
You can change the current heading hierarchy by pressing `M-leftarrow` or `M-rightarrow` when you are directly above a heading. you can also move headings along with their content across the document using `M-uparrow` or `M-downarrow`. pressing `C-enter` when directly above a heading will create another heading of the same hierarchy.


## Emphasis {#emphasis}

| Feature       | orgmode                                                                     | markdown                                                                     |
|---------------|-----------------------------------------------------------------------------|------------------------------------------------------------------------------|
| Bold          | `*text*`                                                                    | `**text**`                                                                   |
| Italic        | `/text/`                                                                    | `*text*`                                                                     |
| Inline Code   | `~code~`                                                                    | `` `code` ``                                                                 |
| Highlight     | `=text=` (usually rendered in green)                                        | This feature does not exist in markdown unless you use the html tag `<mark>` |
| Underline     | `_text_`                                                                    | This is also not supported in markdown unless using the html `<ins>` tag     |
| Strikethrough | `+deleted text+`                                                            | `~~deleted text~~`                                                           |
| Quote         | `#+begin_quote your quote #+end_quote` you can also just type `quote <TAB>` | > your quote                                                                 |
| Hyperlinks    | `[[Link][Description]]`                                                     | `[Description][Link]`                                                        |

> **TIP**: you can add `(setq org-hide-emphasis-markers t)` to your configuration, to hide markup elements in orgmode files
