---
title: "Masters Thesis"
collection: thesis
permalink: /thesis/masters
---

My Master’s thesis focused on developing tool support for detecting and managing software code clones.

### Thesis: CSeR - A Code Editor For Tracking & Visualizing Detailed Clone Differences

We developed an approach for clone management that tracks Copy and Paste (CnP) operations to identify and manage code clones effectively. This method provides detailed insights into differences between clones directly within the editor, with changes visualized in distinct colors for easy interpretation. The comparison is context-sensitive and based on Abstract Syntax Tree (AST) analysis, enabling a deeper and more accurate understanding of code variations.

![Change Visualization Description](/files/ms.png)

In the figure on the right, the change marked “1” is categorized as “Other” under the update change type. Changes “2” and “3” represent the deletion of three fields from the class. The deleted fields can be viewed by hovering the mouse over the corresponding marker, as illustrated for change “2.” Similarly, change “7” indicates the deletion of two statements, while change “8” corresponds to a move operation.

Changes “4,” “5,” and “6” relate to parameter modifications:

“5” denotes the deletion of two parameters, and

“4” and “6” are highlighted in green, indicating newly inserted parameters.


[Thesis](/files/ms_thesis.pdf)
[Thesis TEX](/files/ms_thesis_tex.zip)
[Slides](/files/ms_thesis_slides.pdf)
[Slides TEX](/files/ms_thesis_slides_tex.zip)