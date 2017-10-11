# Markdown Tutorial
### Pagé Goddard

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
    - [Table of Contents / Anchor Tags](#contents)



### Sublime add-ons <a name="add-ons"></a>
* MarkdownEditor *# for easier visualization pre-publication*
* OmniMarkupPreviewer *# for previewing fully formatted document*
    - ctrl + alt + o
    - OR MarkdownBuddy

### Headers <a name="headers"></a>
Use \# to indicate header level
# \# Heading1
## \#\# Sub-heading
###### \#\#\#\#\#\# level 6 subheading

### Emphasis <a name="emphasis"></a>
*italic:* \*word\* or \_word\_ <a name="italic"></a>

**bold**: \*\*word\*\* or \_\_bold\_\_ <a name="bold"></a>

***combined:*** \*\*\*word\*\*\* or \_\_\_word\_\_\_ <a name="combo"></a>

<del>strikethrough</del>: <del\>word<del\> or ~~ word ~~ <a name="strike"></a>

### Lists <a name="lists"></a>
##### unordered <a name="unordered"></a>
* unordered
* lists
    -  use \*
    - and \-

##### ordered <a name="ordered"></a>
1. ordered
2. lists
3. use
    1. numbers
    2. but not letters
        * number of tabs determines bullet level

##### task list <a name="tasks"></a>
- [x] - [x] this is a **complete** item
- [ ] - [ ] this is an *incomplete* item
- [ ] - [ ] list autopopulates format

### Links <a name="links"></a>
##### URLs <a name="urls"></a>
example: [wikipedia](https://en.wikipedia.org/wiki/Main_Page)
\[Alt Text](url)

##### Images <a name="images"></a>
All linked images must be hosted online. You can link to an image on your local machine but it will not be viewable in the published markdown on other devices. When the image is not publishable, the Alt Text input will be shown.

![cute puppy](https://s7.favim.com/610/151130/cute-cuties-dog-smile-Favim.com-3673417.jpg)

\!\[Alt Text](url)

you can resize images using standard HTML

<img src="https://s7.favim.com/610/151130/cute-cuties-dog-smile-Favim.com-3673417.jpg" alt="cute puppy" style="width: 200px;"/>

<img src="url" alt="Alt Text" width=200/> 

*note: the alt="" input is optional

##### Table of Contents Links <a name="contents"></a>
This requires use of **anchor tags** where you want the table of contents to link to.

\# This is my header! `\<a name="name_me"></a>`


You can then link to that line from anywhere in the document using:

\[That one header\]\(\#name_me\)

### Block quotes <a name="quotes"></a>
the following lines will be a quote

\> it was the best of times

\> it was the worst of times
> it was the best of times
> 
> it was the worst of times

### Code Blocks <a name="code"></a>
##### Inline Code <a name="inline"></a>

This is your \`code\` to highlight

This is your `code` to highlight

##### Fenced Code Blocks <a name="block"></a>

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

### Tables <a name="tables"></a>
* tables use | and - to indicate field divisions

column 1 | column 2

\---------|--------- *# this line is necessary to indicate table format*

cell 1.1 | cell 2.1

cell 1.2 | cell 2.2

column 1 | column 2
---------|---------
cell 1.1 | cell 2.1
cell 1.2 | cell 2.2

### Line breaks <a name="linebreak"></a>
* 3 or more \* \- or \_
---


### Escaping characters <a name="escape"></a>
removes special syntax meaning: 
*italic* v. \*asterisks\*

`\character` can be used on the following:

\\ \` \* \# \_ \- \+ \.
\{ \} \[ \] \( \) \!

### Emjois (github) <a name="emojis"></a>

(just remove the spaces)

:+1: = \: \+ 1 \:
:sparkles: = \: sparkles \:
:octocat: = \: octocat \:
etc.
