# SPOSE
# Simple Parsing Of String Encodings

## About

SPOSE is a very simple serialization of strings.

It's intent is to means of reliabily editting and encoding settings and configuration files in human-edited repositories and the like.

## Note

For the purpose of this document, a *program* is a application using SPOSE formatted data but a *library* is a set of tools for parsing SPOSE text. Libraries should be bound by the rules of SPOSE. A program, however, can do whatever it wants as long as it inputs and outputs valid SPOSE. Most programs would use a library to help with this.

For example, a library should maintain the order of an associative list, but program can resort that list it however it wants.

## Goals

The goals are as follows:

1. Easily normalized into a repeatable and predictable format.
   i. normalizing an already-normalized file must create the exact same file (in all libraries in platforms)
   ii. normalizing any file must create the exact same file regardless of library used
   iii. reading a SPOSE file into a program and then writing it out again (without modification) should create the exact same file

2. Easily modified by humans.
   i. must be visually parsable by a typical human
   ii. must use as few rules as possible
   iii. must not use recursion or references
   iv. must not encode data-typing; in this case, everything a string
   v. spacing only used when visually obvious (such as indenting)

3. Well-tracked in line-oriented version-control systems such as `git`.
   i. line-oriented: one line should encode one assertion
   ii. keeps order. A

4. Be generic enough to be used on any platform, any human language, and with any programming language.

Fast parsing by a computer is NOT a goal as it is presumed that this is not going to be used for mass storage.

## Example of SPOSE

An example (a reinterpretation of the example used for YAML in wikipedia):

```
## spose 1.0
"receipt": "Oz-Ware Purchase Invoice"
"date": "2012-08-06"
"customer": *
    "first_name": "Dorothy"
    "family_name": "Gale"
"items": *
    .: *
        "part_no": "A4786"
        "descrip": "Water Bucket (Filled)"
        "price": "1.47"
        "quantity": "4"
    .: *
        "part_no": "E1628"
        "descrip": "High Heeled \"Ruby\" Slippers"
        "size": "8"
        "price": "133.7"
        "quantity": "1"
"address": *
    "key": "id001"
    "street": |
        "123 Tornado Alley"
        "Suite 16"
    "city": "Ease Centerville"
    "state": "KS"
"bill-to": "id001"
"ship-to": "id001"
"specialDelivery": >
    "Follow the Yellow Brick "
    "Road to the Emerald City. "
    "Pay no attention to the "
    "man behind the curtain."
```

A few points of note:

* As shown, "items" is a _single_ entry containing a list with two unnamed entries. Alternatively, it could have also been done as:
```
"item": *
    "part_no": "A4786"
    "descrip": "Water Bucket (Filled)"
    "price": "1.47"
    "quantity": "4"
"item": *
    "part_no": "E1628"
    "descrip": "High Heeled \"Ruby\" Slippers"
    "size": "8"
    "price": "133.7"
    "quantity": "1"
```
If done that way, then there would be _multiple_ "item" entries; each containing their own characteristics. Both are legitimate methods since names are not unique, but are ordered, in SPOSE.
* YAML uses a node and anchors in their example (see the "id001" references). SPOSE specifically does NOT support such structures. Instead, for this example, the program would have to _interpret_ the references in "bill-to" and "ship-to" as a function of the program. Alternatively, we could have simply repeated the same address in both "bill-to" and "ship-to".
* The "specialDelivery" field is not shown in normalized format. Since the name/string would fits into the 132-column limit, it simply would be normalized to:
```
"specialDelivery": "Follow the Yellow Brick Road to the Emerald City. Pay no attention to the man behind the curtain."
```