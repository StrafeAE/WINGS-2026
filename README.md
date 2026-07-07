# WINGS-2026

A repository to store code, data, papers, etc. for our work with WINGS at SUNY Poly.

## Todo List:

- Create a Block Diagram explaining what our current best model is doing.
- Figure out how to fix the issue with the `.mat` files causing us to discard $\frac 13$ of the data.

## Some Ideas:

- Can we use an RNN layer for any part of this?
  - Would it even be useful? It very well might be the case that the CNN does just fine.
- Can we use a Transformer instead of a CNN?


## Progress

Currently our best model is created using `material_classifier_1.ipynb`. The model reaches $65\%$ accuracy in the test set. It consists of two main branches: the main profile branch is a CNN that handles the multipath profiles, and the auxiliary branch is simply a linear classifier that takes care of the other features. The two branches are then combined with a linear classifier to give us a final prediction.

This model only uses $\frac 23$ of the data we have available to us because the sizes of the matlab arrays don't line up with each other. For some of the files, we don't have a full $48$ rows worth of multipath data, but the script that generated the files didn't take that into account, and still generated the other arrays as if we had all the data. This means we can't reliably match a multipath profile to it's auxiliary features. Since we don't know which rows are affected, we discard the entire file.

This data issue affects the metal profiles the worst, since about half of the metal files have this issue. This is reflected in the confusion matrix where we see metal has the worst accuracy out of the 4 materials. This is not necessarily the only reason that metal does the worst, there might be some other factor that makes metal a harder material to predict.

---

$\dots$

---