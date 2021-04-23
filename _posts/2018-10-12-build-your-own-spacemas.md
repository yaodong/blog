---
title: How to get started to build your Spacemacs
tags: emacs
category: journal
---

I have been a Vim user for quite a while. I love it, so the first thing when I set up a new server is installing VIM. I heard a lot about Emacs and tried it. It's better at almost everything except text editing. So I stayed with Vim until Spacemacs was released. The Vim key bindings of Spacemacs are so excellent that I switched.

However, Spacemacs has its problem: it boots up slowly and crashes frequently. Upgrading packages became very challenging. Finally, I have to use the develop branch because the master branch is not working anymore. People began to complain and requested to speed up the release from the master branch. Considering its dependencies and numerous open issues, I have no hope for this.

![screenshot of spacemacs on Github](https://d.pr/i/4jbVf6+)
<center><small>Screenshot taken on Oct. 31, 2018, Spacemacs has 2008 open issues and the last update of master branch is Mar 3, 2018.</small></center>
<p></p>
I decided to build my Spacemacs.

The features of Spacemacs I rely on most are the evil layer and the convenient key-bindings. Other features such as navigating, window management, and programming language support can be added later. Then I did it and found that It's much easier than I thought.

Assuming you are a proficient Emacs user. If not, start from [here](https://www.masteringemacs.org/article/beginners-guide-to-emacs) or pick one [starter-kit](https://www.emacswiki.org/emacs/StarterKits).

The first step is managing the packages with macro [use-package](https://github.com/jwiegley/use-package). For this step, you need to 

1. [Installation of use-package](https://jwiegley.github.io/use-package/installation/)
2. [learn how to use it](https://github.com/jwiegley/use-package/blob/master/README.md)

Then, we need three packages:

1. [evil](https://melpa.org/#/evil)
2. [which-key](https://melpa.org/#/which-key)
3. [general](https://melpa.org/#/general)

If any package of the above is new to you, stop reading and Google it.

```lisp
(use-package evil
  :ensure t
  :config
  (progn
    (evil-mode 1)
    (setq evil-default-cursor t)))

(use-package general
  :ensure t)

(use-package which-key
  :ensure t
  :config
  (progn
    (setq which-key-idle-delay 0.5)
    (which-key-mode +1)))
```

That's all you need to get started. Pretty easy.

I used the global leader key `SPC` and major mode leader key `,`  a lot in Specemacs. Let's define two key sequences for demonstration.

- `SPC w /` which can split the selected window into two windows, positioned side by side.
- `, h s` which can insert a subheading when and only when the major mode is org-mode.

To define a global key sequence with `general`, using `general-define-key`:

```lisp
(general-define-key
   :state 'normal
   :prefix "SPC"
   "w/" 'split-window-right)
```

Now pressing `SPC w / ` can call the function `split-window-right`, but if you pause typing after typed `SPC` you will see a hint: `w +prefix`. That's because Emacs doesn't know what's `SPC w` means. To fix this, we need to provide a clue for `which-key`.

```lisp
(which-key-add-key-based-replacements
   "SPC w"
   "windows")
```

Now, pressing `SPC w`, you can see `w +windows` instead of `w +prefix`. 

The configuration of major mode leader key is similar:

```lisp
(general-define-key
   :prefix ","
   :states 'normal
   :keymaps 'org-mode-map
   "hs" 'org-insert-subheading)

(which-key-add-major-mode-key-based-replacements
   'org-mode
   ",h"
   "headings")
```

But writing multiple key bindings is still not convenient, so I wrote some helpers:

```lisp
(setq-default
  leader-key "SPC"
  leader-for-major-mode ",")

(defun set-keys-for-major-mode (maps key def &rest bindings)
  (while key
    (general-define-key 
      :prefix "," 
      :states 'normal 
      :keymaps maps 
      key def)
    (setq key (pop bindings) def (pop bindings))))

(defun set-keys (key def &rest bindings)
  (while key
    (general-define-key 
      :states 'normal
      :prefix user/leader-key
      key def)
    (setq key (pop bindings) def (pop bindings))))

(defun declare-prefixes (prefix name &rest bindings)
  (while prefix
    (which-key-add-key-based-replacements (concat leader-key " " prefix) name)
    (setq prefix (pop bindings) name (pop bindings))))

(defun declare-prefixes-for-major-mode (mode key def &rest bindings)
  (while key
    (which-key-add-major-mode-key-based-replacements mode (concat leader-for-major-mode key) def)
    (setq key (pop bindings) def (pop bindings))))
```

With these helpers, defining multiple prefixes can be rewritten to:

```lisp
(declare-prefixes 
  "a"   "applications"
  "b"   "buffers"
  "e"   "errors"
  "f"   "files"
  ;; omitted
  "w"   "windows")

(set-keys
  "SPC" 'helm-M-x
  "!"   'shell-command
  "v"   'er/expand-region
  "ac"  'calc-dispatch
  "aP"  'proced
  ;; omitted
  "au"  'undo-tree-visualize)
```

The define of multiple major mode key bindings can be:

```lisp
(declare-prefixes-for-major-mode 
 'org-mode
 "e"  "export"
 "x"  "text"
 "h"  "headings"
 "i"  "insert"
 "t"  "tables"
 ;; omitted
 "tt" "toggle")

(set-keys-for-major-mode 'org-mode-map 
 "'" 'org-edit-special
 "c" 'org-capture
 "d" 'org-deadline
 ;; omitted
 "b" 'org-tree-to-indirect-buffer
 "A" 'org-archive-subtre)
```

The above steps may take you one or two hours. Then you will be able to start to build yourself a tremendous Spacemacs-like experience and don't need to wait for one of the thousands of issues to be fixed.

By the way, [here](https://github.com/yaodong/emacs.d) is my `emacs.d`. Free feel to use it.
