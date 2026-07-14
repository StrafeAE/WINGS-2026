# WINGS-2026

A repository to store code, data, papers, etc. for our work with WINGS at SUNY Poly.

## Todo List:

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

There is an issue causing our accuracy to be artificially inflated. The problem is that we are leaking data from our training set into our validation and test sets. This is a big issue and essentially means that our ML model is "cheating" by looking at the answers, so-to-say.

The fix seems to be that instead of stratifying on a row-by-row basis, we should instead stratify across each file. This way we are actually testing if we can generalize to new predictions. This will likely bring our accuracy down, but that is okay (it's better than being dishonest).

I have also included more auxiliary features in the second iteration of the model, `material_classifier_2.ipynb`. This time, we are also using `distance_Rx`, `distance_Tx` (I thought we were using these two the whole time lol), `P_sig_dBm` (48 $\times$ 16 array). These extra auxiliary features should help to get our accuracy back up. 

Another thing I noticed is that we were normalizing over each individual multipath profile. This means that we are feeding the model a version of the data that has mean 0 and standard deviation 1 for EVERY profile. We only really retain the shape of the multipath, but not other things that might be encoded in the amplitude, like power or distance. Instead, we can normalize over the *entire* dataset, and apply that transform to all samples. This will still let us normalize the data, while keeping information about the amplitude as well.

All of these changes bring us to about $60\%$ accuracy, which is a decrease from the first model, but it's actually a better model since it should generalize to new data better.

---

For the third iteration, we decided to implement some dimensionality reduction. Since our dataset is so small, this is even more important because our current model is likely learning a lot of the noise, not the actual data we want it to learn. Luckily, we don't have to do much to implement this. We already have the peaks of the multipath stored in `peak_loc_IF` and `peak_val_IF`, so we just have to modify the model to not take in the multipath profile, and instead take in the peak information.

Handling the peak data requires some special handling since the arrays have variable length based on how many peaks we detect. In this program, we pad the arrays with `NaN` whenever there are less peaks than the maximum.

This change actually brings us back up to a little over $70$ percent! We are making great progress! Currently, I believe the thing that would bring the biggest improvements would be to fix our data. We either need to obtain more data, or to find how the `.mat` files were generated and pin down which specific multipaths are missing.

---

$\dots$

---