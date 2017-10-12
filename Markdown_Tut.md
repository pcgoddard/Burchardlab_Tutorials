# Markdown Tutorial
#### Pagé Goddard
#### Sep 1, 2017
---
## Contents
* [Sublime Add-ons](#add-ons)
* [Headers](#headers)
* [Emphasis](#emphasis)
* [Lists](#lists)
    - [unordered lists](#unordered)
    - [ordered lists](#ordered)
    - [task lists / check boxes](#tasks)
* [Links](#links)
    - [URLs](#url)
    - [images](#images)
    - [Table of Contents / Anchor Tags](#anchor)
* [Block Quotes](#quotes)
* [Code Inserts](#code)
    - [Inline highlighting](#inline)
    - [Block Code](#block)
* [Tables](#tables)
* [Line Breaks](#linebreaks)
* [Using Special Characters](#escape)
* [Emojis :sparkles:](#emojis)

---
<a name="add-ons"></a>
## Sublime add-ons
* MarkdownEditor *# for easier visualization pre-publication*
* OmniMarkupPreviewer *# for previewing fully formatted document*
    - ctrl + alt + o
    - OR MarkdownBuddy

<a name="headers"></a>
## Headers <a name="headers"></a>
Use \# to indicate header level
# \# Heading1
## \#\# Sub-heading
###### \#\#\#\#\#\# level 6 subheading

<a name="emphasis"></a>
## Emphasis <a name="emphasis"></a>
*italic:* \*word\* or \_word\_ <a name="italic"></a>

**bold**: \*\*word\*\* or \_\_bold\_\_ <a name="bold"></a>

***combined:*** \*\*\*word\*\*\* or \_\_\_word\_\_\_ <a name="combo"></a>

<del>strikethrough</del>: <del\>word<del\> or ~~ word ~~ <a name="strike"></a>

<a name="lists"></a>
## Lists 
<a name="unordered"></a>
### unordered 
* unordered
* lists
    -  use \*
    - and \-

<a name="ordered"></a>
### ordered 
1. ordered
2. lists
3. use
    1. numbers
    2. but not letters
        * number of tabs determines bullet level

<a name="tasks"></a>
### task list 
- [x] - [x] this is a **complete** item
- [ ] - [ ] this is an *incomplete* item
- [ ] - [ ] list autopopulates format

<a name="links"></a>
## Links 
<a name="urls"></a>
### URLs 
example: [wikipedia](https://en.wikipedia.org/wiki/Main_Page)

`\[Alt Text](url)`

<a name="images"></a>
### Images 
All linked images must be hosted online. You can link to an image on your local machine but it will not be viewable in the published markdown on other devices. When the image is not publishable, the Alt Text input will be shown.

![cute puppy](http://www.zarias.com/wp-content/uploads/2015/12/61-cute-puppies.jpg)

`\!\[Alt Text](url)`

#### you can resize images using standard HTML

<img src="http://www.zarias.com/wp-content/uploads/2015/12/61-cute-puppies.jpg" width="200"/>

`\<img src="url" alt="Alt Text" width="200"/>`

*note: the alt="" input is optional

<a name="anchor"></a>
### Table of Contents Links 
This requires use of **anchor tags** where you want the table of contents to link to.

`\<a name="anchor_tag"></a>`

**\# This is my header!**

* *note: remove the `\` before the first `>` to activate the anchor tag*
* *personal preference: I like to put the anchor tag **above** the tagged section so that when the link jumps to that section you still see the header*

You can then link to that line from anywhere in the document using:

`\[My header\]\(\#anchor_tag\)`

<a name="quotes"></a>
## Block quotes 
the following lines will be a quote

\> it was the best of times

\> it was the worst of times

> it was the best of times
> 
> it was the worst of times

<a name="code"></a>
## Code Blocks 
#### Inline Code <a name="inline"></a>

This is your \`code\` to highlight

This is your `code` to highlight

<a name="block"></a>
#### Fenced Code Blocks 

\```javascript

function test() {

console.log("hello world");

\```

```javascript
function test() {
    console.log("hello world");
}
```

to put any text in a code box, just indent it once

    any text

<a name="tables"></a>
## Tables 
* tables use | and - to indicate field divisions

column 1 | column 2

\---------|--------- *# this line is necessary to indicate table format*

cell 1.1 | cell 2.1

cell 1.2 | cell 2.2

column 1 | column 2
---------|---------
cell 1.1 | cell 2.1
cell 1.2 | cell 2.2

<a name="linebreak"></a>
## Line breaks 
* 3 or more \* \- or \_
---

<a name="escape"></a>
## Escaping characters 
removes special syntax meaning: 
*italic* v. \*asterisks\*

`\character` can be used on the following:

\\ \` \* \# \_ \- \+ \.
\{ \} \[ \] \( \) \!

<a name="emojis"></a>
## Emjois (github) 

(just remove the spaces)

:+1: = \: \+ 1 \:
:sparkles: = \: sparkles \:
:octocat: = \: octocat \:
