---
title: "Writing my Resume in LaTeX"
description:
date: 2023-03-07T10:00:00-06:00
slug: "latex-resume"
image: orion-nebula-gb8faf4a6f_1920.jpg
draft: true
tags:
  - latex
  - job search
---

Writing LaTeX sucks, but the results are undeniably better than anything else. This article is a brief introduction into LaTeX, the basic prerequisites to writing documents, what the ecosystem is like, and how you can write your resume in LaTex.

## Dark Times Before LaTeX

I am very picky about resume formatting. If things aren't perfectly aligned, it bothers me on a spiritual level. Before LaTeX, my solution for writing my resume was to make a table in google docs, organize the content into columns, and set the table line width to 0 so it would be transparent. This setup worked for quite some time, but it had drawbacks.

- changing the column width for one cell had implications for other cells
- the witdth of each cell was independent from other similar cells. This led to inconsistent spacing on the headers and sections
- content would shift between when the table was set to 1 or 0 line width
- a lot of cells had to be merged together
- content had to be manually shuffled around for appropriate page balancing

![my old resume with the table cell outline set to 1pt and 0pt width](old-resume.png)

I felt really good about the results, but over time as I would make modifications the pain points started to wear on me. I spoke to a coworker about his resume, and he said he wrote his in LaTeX. I didn't know much about LaTeX; I knew it was frequently used for academic papers and was developed as an easier way to express mathematical equations.

My interest was piqued, and I decided to give it a shot. I looked on Overleaf for public LaTeX resumes, but I didn't see anything that really looked like what I wanted. I ended up writing my own from scratch!

## LaTeX Concepts

### Environments

When you declare things like font, size, color, and other text modifiers, You're applying it to an environment. Think of an environment as something like a title, paragraph, footer, or table. A subsection of things that needs to be handled in a certain way. You can create your own environments, or you can use pre-made ones included in libraries. You can use a pre-made an environment like so:

```latex
\begin{environmentName}
  I'm in the environment!
\end{environmentName}
```

Any text alterations you make inside that codeblock will only apply to that subset of text. You can declare your own custom environment like so

```latex
\newenvironment{foo}{
  % executes as the top portion of the environment
  \color{Green}
  \begin{list}{x}{\raggedright}
}{
  % executes as the bottom portion of the environment
  \end{list}
}
```

You can also have environments within environments

```latex
\begin{environmentName}
  I'm in the environment!
  \begin{foo}
    I'm in the custom environment!
  \end{foo}
\end{environmentName}
```

### Packages

TeXLive has a large number of default document classes and packages that are immediately available at your disposal, and you'll have to use a lot of them. Set a page's margins? Use `Geometry`. Merge cells in a table? Use `multirow` or `multicol`. Color something? Use `xolor`. Want to add a hyperlink? Use `hyperref`.

Package discovery largely entails scrawling through StackOverflow or Overleaf for threads where people talk about how to do things. Overall, the ecosystem is well documented with most LaTeX libraries choosing to document their programs in PDFs on CTAN. You can find an example of the Geometry package [here](https://ctan.org).

You can import a package like so

```latex
\usepackage{fontawesome}
```

### Text Sizing

Because of how LaTeX handles text, you are limited to a small range of predetermined sizes that should meet most people's needs. If you want to go larger or smaller, you'll need to find a package with different supported sizes. Overleaf has some excellent docuemntation on font sizing you can read [here](https://www.overleaf.com/learn/latex/Font_sizes%2C_families%2C_and_styles)

### Fonts

Using a specific font isn't as easy as you might think it would be. To use a font, you first have to identify what the font's name as it's defined in LaTeX. Once you have that name, you can apply it to an environment like so

```latex
\begin{foo}
  \fontfamily{<familyname>}\selectfont
\end{foo}
```

Packages like fontenc make using fonts much easier. To look at fonts online, I'd recommend reading over the tug.org font catalogue https://tug.org/FontCatalogue

### Tolerance

Whenever a line of text is justified, LaTeX must make a determination on how much whitespace spread between words can be tolerated without looking weird. It also decides just how much you can go past a margin before it puts the next word on a newline. Tolerance is an integer set between 0 and 10000. It's easiest to understand in a picture

![An image showcasing tolerance in LaTex](tolerance.png)

### Document Classes

There are many different types of documents, and all those documents have things in common regardless of who wrote the document. Classes are a collection of common parameters and characteristics in a document that should apply to all documents of that type. LaTeX ships with a few default document classes that you can use like report, book, article, or letter. Article is a good class to use if you want a blank template to work with.

### Functions and Variables

Functions

You declare a variable the same way you declare a function; Just create a function that returns the value you want assigned to the variable. You can then use that function wherever you want to

### Drawing Things

There are a million ways to do what you probably want, and 50% of those ways will probably involve using TikZ

TikZ allows you to draw pictures in LaTeX. The way you draw pictures might be appealing to mathemeticians, but it's not a very enjoyable experience otherwise. There are a lot of packages that utilize TikZ to draw things for you in a more easy to write format.

I use a package called `mdframed` that uses TikZ to outline text boxes around words and color them.

## Writing the Resume

It can be very helpful to observe a project from its creation to completion, so I've added an example branch with a series of tagged commits you can inspect to compare. A picture of the PDF output is included in the `README.md` of the repository so this blogpost doesn't get too long.

### File Setup

I start by defining a custom document class named `resume.cls` and a `resume.tex`. I put shared functions and formatting related things in `resume.cls`. The content of the resume itself goes in `resume.tex`. I add some content to `resume.cls` to declare its parent document class, and link `resume.tex` to that class. I also add a special environment named document to `resume.tex` and put my name in it.

![](01_file-setup.png)

### Content

When working with a document that requires a lot of formatting, it can be helpful to have all your content already on the page so you can observe the text will behave as you make changes. For this example, there's a mix of real text and fake text. I'll use the lipsum package and add it to `resume.cls`. After it's imported, I can use the `\lipsum` directive to get some example content.

Some of the content needs to be handled differently from the others though. I'll declare a list environment for the qualifications and skills, then add some placeholder items to it.

![](02_content.png)

### Heading

The first formatting thing I want to tackle is the page margins. I like my resume to have short vertical margins and wider horizontal margins. I can use the geometry package to setup page margins like so

```latex
\usepackage{geometry}
\geometry{
    a4paper,
    left=20mm,
    right=20mm,
    top=8mm,
    bottom=8mm,
}
```

The second formatting thing I want to tackle is the heading. Before I can do that, I need to add some variables so that I can use their content in `resume.cls`. After adding them with `\newcommand{\functionName}{string}` I can then call `\functionName` to access their output.

My heading isn't something that's really going to change, so the strategy I use for my heading is a function that takes no parameters that pulls from the variables we just declared. I define a heading command and add the content we have in our header like so

```latex
\newcommand{\heading}{
  \name
  \github
  \website
  \email
  \phone
  \address
}
```

I want things roughly aligned with where they need to go, and I can do that with an environment called a tabular. Table declarations in LaTeX are very finnicky. They look best as code when their content is kept simple and straightforward. We pass a few parameters to indicate how many cells we want, how wide those cells should be, and how they should align. We'll also want to use the package `tabularx` since it fixes some issues that native LaTeX tabulars have. Here's an example

```latex
\begin{tabular}{p{200pt} p{120pt} p{120pt}}
  % define the table content and separate cells with &
  % newlines in tables are declared with \\ at the end
  \name & \github & \website \\
        & \email & \phone
\end{tabular}
```

The document is already looking much better! The next thing I want to do is increase the size of my name and capitalize it as well as the sections. For that, we can use `\MakeUppercase{}` and `\huge`. I also want the things on the right side of the heading to align to the right side of the page; We can do that by applying the `>{\raggedright\arraybackslash}` directive to the columns in question.

```latex
\newcommand{\heading}{
  \begin{tabular}{p{200pt} p{120pt} >{\RaggedLeft}p{120pt}}
    \MakeUppercase{\huge\name} & \github & \website \\
                               & \email  & \phone
  \end{tabular}
}
```

![the heading without any cells merged](03_weird-spacing.png)

My name looks bigger, but now the table's content is weirdly aligned. To fix this, I merge the empty cell below my name with the `multirow` package

```latex
\newcommand{\heading}{
  \begin{tabular}{p{200pt} p{120pt} >{\RaggedLeft}p{120pt}}
    \multirow{2}{200pt}{\MakeUppercase{\huge\name}} & \github & \website \\
    & \email & \phone
  \end{tabular}
}
```

![the heading with the two cells merge](03_better-spacing.png)

Okay my name is aligned now, but that line to print my name is now absurdly long and makes the table illegible from a code standpoint. I'm going to obfuscate how my name's casted with another command
