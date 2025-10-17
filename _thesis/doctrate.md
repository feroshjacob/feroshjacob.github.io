---
title: " Ph.D. Dissertation"
collection: thesis
permalink: /thesis/doctrate
---

 My Ph.D. dissertation explored the application of software modeling techniques to computation-intensive problems, enabling efficient heterogeneous computing and enhanced source code maintenance.  

I was supported by an Air Force/AFRL STTR grant, which I helped Dr. Jeff Gray prepare and secure. Much of the work from this grant became the foundation of my doctoral research, and based on our success in Phase I, we were invited to submit a Phase II proposal. 

### Dissertation: Modeling of High Performance Programs to Support Heterogeneous Computing
![Phd overview](/files/phd.png)
My dissertation explored how *Model-Driven Engineering (MDE)* and *Domain-Specific Languages (DSLs)* can support the evolution of parallel programs used by both high-performance computing (HPC) programmers and end-users. The research demonstrated that effective tool support for HPC programs can be achieved at multiple levels of abstraction, each tailored to a specific set of users. These abstraction levels include:

1. Code-level
2. Algorithm-level
3. Program-level
4. Sub-domain-level

We designed, implemented, and evaluated DSLs at each abstraction level to facilitate heterogeneous computing. The code-level abstraction is broadly applicable to any C/C++ program, while the algorithm-level abstraction targets programs implementing MapReduce algorithms. In contrast, program-level and sub-domain-level abstractions are domain-specific and were applied to specialized areas such as the Signature Discovery Initiative (SDI) and N-body simulations.

Our findings showed that the more specific the domain, the less input is required from the user, as the DSLs are inherently domain-aware. Conversely, more general abstractions (such as code-level and algorithm-level) offer broader applicability but demand greater input and expertise from end-users for effective adoption.

[Dissertation](/files/phd_dissertation.pdf) [TEX files](/files/phd_dissertation_tex.zip) [PPTX](/files/phd_dissertation_slides.pptx)