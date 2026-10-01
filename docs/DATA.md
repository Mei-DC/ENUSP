# Data and split

ENUSP reads the prepared DRAI recordings from the public 77 GHz AWR1843 gesture
dataset used in the paper. The source dataset is described by Li et al.,
*Towards Domain-Independent and Real-Time Gesture Recognition Using mmWave
Signal*, IEEE Transactions on Mobile Computing 22(12), 7355–7369 (2023),
[doi:10.1109/TMC.2022.3207570](https://doi.org/10.1109/TMC.2022.3207570).
Obtain the data separately from its authors and follow their distribution terms.
Radar recordings are not included in this repository.

## Input files

Place the original `.npy` files together in one data directory. Each file must
contain a finite real-valued array shaped `(T, 32, 32)`, where the axes are time,
range and angle. Names carry the gesture and one-based domain identifiers:

```text
y_Push_e3_u6_p2_s1.npy
n_sit_e3_u6_p2_s1.npy
```

The loader preserves the stored amplitudes. It center-crops sequences longer
than 32 frames and appends zero frames to shorter sequences, then converts to
float32. A loaded sample has shape `(32, 1, 32, 32)`; a batch has shape
`(B, 32, 1, 32, 32)`. Invalid shapes, nonfinite values and out-of-range domain
IDs produce an error with the file name.

Raw ADC processing, CFAR and ROI extraction are upstream of this package. The
available historical material does not identify all original CFAR/ROI settings
or a complete numerical DRAI normalization specification. This release therefore
starts at the supplied DRAI arrays and adds no inferred normalization or detector
settings. Use DRAI files prepared consistently with the intended experiment.

## Seven classes

The output order is fixed and follows the original trainer's alphabetical
LabelEncoder order:

| Index | Class |
| --- | --- |
| 0 | Clockwise |
| 1 | Counterclockwise |
| 2 | Pull |
| 3 | Push |
| 4 | SlideLeft |
| 5 | SlideRight |
| 6 | Negative (`n`) |

Negative groups LiftRight, LiftLeft, Sit Down, Stand Up, Waving, Turn Around and
Walk at the task-definition level. Files beginning with `y_liftright`,
`y_liftleft` or `y_waving` also map to Negative. The `n_` file names use the
tokens `liftright`, `liftleft`, `sit`, `stand`, `waving`, `turn` and `walking`.
Every selected recording must still have explicit environment, user and
position labels. Original walking recordings without a position token are
excluded from this strict protocol; a position is never guessed.

## Fixed environment–user–position split

The following IDs use the original one-based dataset notation:

| Split | Environments | Users | Positions | Samples |
| --- | --- | --- | --- | ---: |
| Train | e3–e5 | u6–u18 | p2–p4 | 2,880 |
| Validation | e6 | u19–u25 | p5 | 540 |
| Test | e1–e2 | u1–u5 | p1 | 600 |

These are inclusion ranges. Some combinations were not recorded, so the
observed user list may be smaller than the complete range. Files outside these
three sets are excluded. No random split or Negative-class downsampling is used.

| Class | Train | Validation | Test |
| --- | ---: | ---: | ---: |
| Clockwise | 240 | 55 | 50 |
| Counterclockwise | 240 | 55 | 50 |
| Pull | 240 | 55 | 50 |
| Push | 240 | 55 | 50 |
| SlideLeft | 240 | 55 | 50 |
| SlideRight | 240 | 55 | 50 |
| Negative | 1,440 | 210 | 300 |
| Total | 2,880 | 540 | 600 |

The expected counts come from the paper's fixed split and the recovered
file-level manifest. Check the saved split summary against this table before
comparing an experiment with the paper.

## Manifests

Training saves exact file membership and ordering in a CSV manifest. Its columns
are:

```text
split_name,file_name,gesture_name,gesture_label,env_label,user_label,pos_label
```

`split_name` is `train`, `val` or `test`. Numeric gesture and domain labels are
**zero-based**; filenames retain their original one-based identifiers. The
loader checks each label against the filename and fixed split, checks that files
exist, and rejects repeated records across splits. The default construction
sorts filenames for a stable order. Preserve the generated manifest with each
reported run.

Some historical analysis manifests used another gesture-index order. Their
numeric labels cannot be substituted directly for this package's labels. Use
the generated manifest, or supply a file containing only `split_name` and
`file_name` so that labels are derived from names.

## Training augmentation

For each command-gesture sample, the original sequence is reversed along time
with probability 0.5, before temporal crop or padding. Its label is swapped
between Push/Pull, SlideLeft/SlideRight or Clockwise/Counterclockwise. Negative
samples are not reversed. Range and angle axes are unchanged. Validation and
test samples receive no random augmentation.

This is the sequence-reversal augmentation described in the paper. Generic
noise, crop, rotation, brightness/contrast and erasing transforms from the old
experiment code are omitted. In particular, no image-style clipping to `[0,1]`
is applied to the stored radar amplitudes.
