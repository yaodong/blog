---
title: Incorrect config file for javascript-eslint in flycheck
category: debug
tags: emacs, flycheck, eslint
---

When using flycheck in Emacs,  `javascript-eslint` can not be enabled because of the incorrect config file. The output of `M-x flycheck-verify-setup` is as follows:

```
Syntax checkers for buffer NavBar.js in rjsx-mode:

  javascript-eslint (disabled)
    - may enable:  Automatically disabled!
    - executable:  Found at /usr/local/bin/eslint
    - config file: missing or incorrect
```

The reason for this is that the command `eslint --print-config` no longer works when given a directory [^1] if the ESLint version is 6, which is considered an error in flycheck.

```
$ eslint --print-config .
Oops! Something went wrong! :(
ESLint: 6.1.0.
```

Downgrading to ESLint v5 fixed this issue.

```
$ npm install -g eslint@5
```

Then javascript-eslint checker can be enabled:

```
Syntax checkers for buffer NavBar.js in rjsx-mode:

  javascript-eslint (disabled)
    - may enable:  yes
    - executable:  Found at /usr/local/bin/eslint
    - config file: found
```

If not, try `eslint --print-config .` again to find the error. In my case, I am using `eslint-config-airbnb` in a project, so it is still broken until I installed all required config and plugin packages globally.

```
$ eslint --print-config .        
Error: Cannot find module 'eslint-config-airbnb'
```

However, it is best to force `javascript-eslint` to use the local ESLint if it exists. This can be done by adding a new hook [^2].

```elisp
(defun my/use-eslint-from-node-modules ()
  (let* ((root (locate-dominating-file
                (or (buffer-file-name) default-directory)
                "node_modules"))
         (eslint
          (and root
               (expand-file-name "node_modules/.bin/eslint"
                                 root))))
    (when (and eslint (file-executable-p eslint))
      (setq-local flycheck-javascript-eslint-executable eslint))))

(add-hook 'flycheck-mode-hook #'my/use-eslint-from-node-modules)
```

The above issue has been fixed in the master branch of flycheck [^3]. Hopefully, they can release a new version soon.

[^1]: [eslint/eslint issue #11874](https://github.com/eslint/eslint/issues/11874)

[^2]: [Flycheck with file relative eslint executable](https://emacs.stackexchange.com/questions/21205/flycheck-with-file-relative-eslint-executable)
[^3]: [flycheck/flycheck pull request #1588](https://github.com/flycheck/flycheck/pull/1588)