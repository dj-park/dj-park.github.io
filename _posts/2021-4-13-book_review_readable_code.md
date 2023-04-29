---
title: 'Book Review, The Art of Readable Code'
date: 2021-4-13
permalink: /posts/2021/4/review_the_art_of_readable_code/
tags:
  - programming
  - HDL
---

In short, I definitely recommend this book. Some materials, I naturally knew, but some materials I didn’t know. Some materials, it’s better to explicitly be aware of. If you recently suffered from someone’s bad code, this book will relieve your pain, telling you that it’s code writer’s responsibility to make the code readable.


## Some of the Take-aways (quotes directly from the book)
- Code should be written to minimize the time it would take for someone else to understand it.
- Variable naming rule of thumbs: Would a new teammate understand what the name means? If so, then it’s probably okay.
- Break code into “paragraphs”: it provides a visual “stepping stone”. Without it, it’s easy to lose your place on the page.
- “Crutch comments” that make up for bad code (such as a bad function name)—fix the code instead.
- Use “big picture” comments at the file/class level to explain how all the pieces fit together.
- You can also reorder the blocks of an if/else statement. Generally, try to handle the positive/easier/interesting case first. Sometimes these criteria conflict, but when they don’t, it’s a good rule of thumb to follow.
- Engineering is all about breaking down big problems into smaller ones and putting the solutions for those problems back together. Applying this principle to code makes it more robust and easier to read.
- >You do not really understand something unless you can explain it to your grandmother. - Albert Einstein


## Thought – What to Comment
Regarding comments in codes, I rank codes with different comment styles as shown below, 1. being the best, and 4. being the worst.

1. Readable code without any comment
2. Mediocre code with concise and clear comment
3. Mediocre code with detailed comment
4. Mediocre code without any comment


## Thought – Completeness vs Readability
When I create technical documents in my workplace, I fall into a dilemma whether to make it as formal like an official manual, or to make it as informal like a blog post. Looking back, I often took the former path. To be honest, this was partially because I wanted to look professional even though I am well aware of the fact that in some cases, dry explanations are more difficult to understand. I believe Wikipedia and Stack Overflow were successful because they fully take advantage of the latter (Wikipedia has become somewhat organized though..). Official manual sometimes answers ‘A-Z’ when I only need a sample code example. 

Depending on the goal of the document, the author needs to make a trade-off(obviously, achieving both is the best). If I write a public manual for an open-sourced framework, I’d better make it as “complete.” If I write internal documents for someone who’s taking over my duties, I’d better put more emphasis on whether it’s easy to understand.


## Thought – The Power of Open Source, Hardware
Open source culture is very powerful and has been a driving force for software industry to rapidly prosper in past two decades. But such culture isn’t as popular in hardware as it is in software(except RISC-V, an open sourced ISA). There was OpenCore, an open source community for hardware IPs, but didn’t end up with a huge success.

Because when software engineers contribute to open source projects, they carefully organize the code so that it can attract users. Many coding styles for readability have developed, and you must unconsciously know a number of conventions if you have experience of using someone’s code from his or her github.

Verilog, one of Hardware Description Languages(HDLs), looks similar to C, but is not a programming language and is left out from the massive open source trend. It’s true that HDLs usually have higher entrance barriers than programming languages, and the synthesized output of the HDL codes are considered as IPs you need to pay for. There are not many “free” HDL source codes on the web. Hardware hobbyists are not exposed to ready-to-use toy examples; and the field loses a giant man power. 

One excuse for hardware engineers is that few lines of comment often are not sufficient to summarize Verilog modules. They usually need a waveform that explains which signals behave how in certain timing.

Anyway, the entrance barrier has been lowered as people have more interest in hardware. People ask more questions on the forum(Stack Overflow, Xilinx QnA Forum), and people later with the same questions find the solution by simply googling the issue, enhancing the productivity. As the size of forum(the number of threads in the past) increases, the benefits increase faster than linear if not exponential. The improvement of long studied High-Level-Synthesis(HLS) technology also attracts software engineers to the world of hardware. 
