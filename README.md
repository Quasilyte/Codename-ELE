# Codename-ELE
Work in progress. Details will be uncovered later, when planned concepts are impemented.

## Feature reference by examples

### Local var declarations

```elisp
;; With `var'.
(%progn
  (var x 1)
  (var y (+ x x))
  (if cond
      nil
    (var a) ;; Init expression is optional
    (var b)
    (setq a (+ 1 x)
          b (+ 2 y))
    (cons a b)))

;; ...without
(progn
  (let* ((x 1)
         (y (+ x x)))
    (if cond
        nil
      (let (a
            b)
        (setq a (+ 1 x)
              b (+ 2 y))
        (cons a b)))))

;; Note that `var' is hosited.
;; Declarations are "rised" to the beginning of the block.
;; Initializations, hovewer, are not.
(%progn
  (setq x 1)
  (var x)
  x) ;; Returns 1
```

**pros**:
+ Code nesting is lower
+ Less parentheses

### Package prefix omission

```elisp
(defconst global 1)

;; With package prefix omission.
(%defconst my-pkg pkg-local 1)
(%defun my-pkg add (x y) (+ x y))
(%defun my-pkg bar (x)
  (add x x pkg-local &global))

;; ...without
(defconst my-pkg-pkg-local 1)
(defun my-pkg-add (x y) (+ x y))
(defun my-pkg-double (x)
  (my-pkg-add x x my-pkg-pkg-local global))

;; 1. `%defun' (and `%defconst') defines `(format "%s-%s" pkg-name fn-name)'
;;     function (variable for `%defconst').
;; 2. All symbols inside `%defun' body are mangled.
;;    - Keyword symbols are exception. They are never mangled.
;;    - There is exception list:
;;      - Functions that match `(subrp f)' (builtins)
;;      - Symbols that are present in the extendable `%nomangle-list'
;;    - Mangling can be disabled for symbol by using "&" prefix: `(&foo 1)'
;;      calls global `foo'.
```

**pros**:
+ Makes Emacs Lisp packages with proper prefix ("namespace") development less painful
+ Package renaming is easier

### Clojure-style control structures

```elisp
;; Clojure-style `cond'.
(%cond (= x 1) "one"
       (= x 2) (progn (message "multiple forms")
                      "two")
       t "?")

;; Emacs Lisp `cond'.
(cond ((= x 1) "one")
      ((= x 2)
       (message "multiple forms")
       "two")
      (t "?"))
```

```elisp
;; Clojure-style `case'.
(%case x
   1 "one"
   2 "two"
   "?")

;; Emacs Lisp closest attempt.
(pcase x
  (`1 "one")
  (`2 "two")
  (_ "?"))
```

**pros**:
+ Prettier (look at formatting)
+ Less parentheses

### Potential features

List that enumerates features that may be included.

- Reader macros
