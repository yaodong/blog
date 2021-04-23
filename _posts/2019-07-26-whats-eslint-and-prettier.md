---
title: What is ESLint and Prettier
tags: ESLint,JavaScript
category: journal
---

## ESLint and Prettier

Linting is the process of running a program that will analyze code for **potential errors** [^1] by detecting problematic patterns or code that doesn't adhere to particular guideline. It is a type of **static analysis**[^2]. ESLint is a JavaScript linting utility. 

Prettier is a code formatter to make our code looks good.

Both ESLint and Prettier can run independently. 

## Integrating

Both ESLint and Prettier ship with format rules/options. However, some of them are defined by the two parties' conflict with each other. For instance, ESLint requires parentheses around arguments in all cases, while Prettier prefer to avoid them. 

Hence, the first step of integrating is disabling any existing formatting rules in ESLint. It can be done by using `eslint-config-prettier`. Then, we can add format rules from Perttier by using `eslint-plugin-prettier`. The detailed steps can be found [here](https://prettier.io/docs/en/integrating-with-linters.html).

## Style Guide

A code style guide is a set of rules or guidelines used when writing the code. We can implement one by customizing rules in Prettier. There is one code guide for JavaScript[^3] maintained by Airbnb, which has been well accepted by the open source community. But we don't have to manually overwrite these rules to our ESLint + Prettier workflow because maintainers have already done this for us. We can enable it by put `eslint-config-airbnb` into our `package.json`. The detailed instructions can be found [here]().

## Further Reading

- [Integrating and Enforcing Prettier & ESLint](https://silvenon.com/blog/integrating-and-enforcing-prettier-and-eslint)
- [ESLint + Prettier + VS Code — The Perfect Setup](https://www.youtube.com/watch?v=lHAeK8t94as)

## Conclusion

- Use ESLint to analyze codes for potential errors.
- Use Prettier to format codes. 
- Use Airbnb style guide to avoid duplication of work.



[^1]:  [What is Linting](https://stackoverflow.com/questions/8503559/what-is-linting)
[^2]: [About ESLint](https://eslint.org/docs/about/)
[^3]: [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)