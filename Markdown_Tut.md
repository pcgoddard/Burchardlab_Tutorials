# Markdown Tutorial
### Pagé Goddard

## Contents
* [Sublime Add-ons](#add-ons)
* [Headers](#headers)
* [Emphasis](#emphasis)
    - [italic](#italic)
    - [bold](#bold)
    - [combined](#combo)
    - [strikethrough](#strike)
* [Lists](#lists)


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

### Images <a name="images"></a>
\!\[Alt Text](url)

### Links <a name="links"></a>
##### URLs <a name="URLs"></a>
\[Alt Text](url)

##### Images
All linked images must be hosted online. You can link to an image on your local machine but it will not be viewable in the published markdown on other devices
![cute puppy](https://s7.favim.com/610/151130/cute-cuties-dog-smile-Favim.com-3673417.jpg)
\!\[Alt Text](url)

you can resize images using HTML

##### Table of Contents Links
This requires use of **anchor tags** where you want the table of contents to link to.

`\<a name="header1"></a>`



### Block quotes
the following lines will be a quote

\> it was the best of times

\> it was the worst of times
> it was the best of times
> 
> it was the worst of times

### Inline Code

This is your \`code\` to highlight

This is your `code` to highlight

### Fenced Code Blocks

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

### Tables
* tables use | and - to indicate field divisions

column 1 | column 2

\---------|--------- *# this line is necessary to indicate table format*

cell 1.1 | cell 2.1

cell 1.2 | cell 2.2

column 1 | column 2
---------|---------
cell 1.1 | cell 2.1
cell 1.2 | cell 2.2

### Line breaks
* 3 or more \* \- or \_
---


### Escaping characters
removes special syntax meaning: 
*italic* v. \*asterisks\*

`\character` can be used on the following:

\\ \` \* \# \_ \- \+ \.
\{ \} \[ \] \( \) \!

### Emjois (github)

(just remove the spaces)

:+1: = \: \+ 1 \:

:sparkles: = \: sparkles \:

:octocat: = \: octocat \:

etc.
