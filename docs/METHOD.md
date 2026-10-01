# ENUSP implementation

ENUSP learns gesture features from DRAI sequences with a local–global encoder,
independent stream gates, four factorized feature vectors, and three auxiliary
adversarial branches. This package contains the seven-class method for the
strict environment–user–position split.

## Architecture

| Paper component | Implementation |
| --- | --- |
| Input `X` | 32 frames × 32 range bins × 32 angle bins; one input channel |
| Local sequence `F` | Three 3D convolutions, channels 1→16→32→64, kernel 3 and padding 1; GroupNorm, GELU and Dropout3D; spatial 2×2 pooling after the first two layers; spatial mean and a 64→128 projection |
| Global sequence `H` | The CNN sequence plus learned positional embeddings, followed by LayerNorm and two Transformer encoder layers; eight attention heads, width 128, feedforward width 256 |
| Independent gates | Separate 1×1 convolutions and sigmoid activations for the CNN and Transformer streams; both produce a 128-dimensional gate at every time step |
| Fused sequence | Concatenate the two gated streams; Linear(256,128), LayerNorm, GELU and dropout |
| Factorization `Phi` | Temporal mean; Linear(128,512), LayerNorm, GELU, dropout, Linear(512,256), LayerNorm and GELU |
| `z_g, z_e, z_u, z_p` | Four consecutive 64-dimensional blocks of the factorized vector |
| Gesture classifier `C` | `z_g` → Linear(64,64), LayerNorm, GELU, dropout, Linear(64,32), LayerNorm, GELU, dropout, Linear(32,7) |
| Discriminators `D_e, D_u, D_p` | A GRL followed by the same 64→64→32 head structure, with output sizes 6, 25 and 5 respectively |

The Transformer consumes the CNN feature sequence. The local and global streams
are fused after this encoding step. The gates are independent sigmoid gates;
they are not normalized against each other or across the batch. Domain losses
reach the encoder and shared factorization through `z_e`, `z_u` and `z_p`.
The gesture classifier uses only `z_g` at inference.

The retained architecture has **639,755 parameters**, including the three
training-time discriminators. The unused 128→64 gesture projection in the legacy
constructor has been removed. The manuscript's reported 0.79 M parameter count
does not describe this four-by-64 implementation and should be reconciled with
the authors' original experiment configuration before publication.

## Training objective

For a batch of `B` samples, each factorized vector is L2-normalized along its
feature dimension with epsilon `1e-12`. The implemented penalty is:

```text
L_ortho = mean over samples and d in {e,u,p} of |normalize(z_g) · normalize(z_d)|
L_total = L_cls + lambda_adv(e) * sum_d(w_d * L_domain_d) + 0.05 * L_ortho
```

`L_cls` is cross-entropy with label smoothing 0.1 and class weights computed only
from the training split: `a_k = N_train / (7 * n_k)`. Each domain loss is ordinary
cross-entropy, averaged over the batch. The full dataset vocabularies of 6
environments, 25 users and 5 positions are retained; absent source identities do
not occur in the training labels.

The legacy implementation averages the three cosine terms. The supplied
manuscript equation sums them before the batch average. Consequently, the code
coefficient 0.05 corresponds to **0.05/3** when using the manuscript's summed
definition. The code preserves the implemented scaling. The training class
weights are also made explicit here because the supplied manuscript describes
label smoothing without specifying these weights.

Adaptive linear balancing (ALB) uses the detached, batch-mean domain losses:

```text
m_d = 0.9 * m_d + 0.1 * detach(L_domain_d)
w_d = 3 * max(m_d, 1e-8) / sum_j(max(m_j, 1e-8))
```

Each EMA starts at 1 and is updated only after the adversarial warm-up. Weights
sum to three. This is raw-loss ALB: the losses are not divided by class counts
or source-label entropy. The weights have no gradient. Raw cross-entropy has an
axis-dependent scale, so ALB can settle to nearly constant unequal weights;
the implementation does not assume that larger losses isolate domain-shift
difficulty.

GRL is the identity in the forward pass and multiplies the feature gradient by
`-alpha`. The total objective uses a positive domain-loss coefficient. A single
backward pass updates the encoder, classifier and discriminators; no second
sign reversal or alternating discriminator update is required.

## Default recipe

Defaults follow the primary 77 GHz implementation table and schedules in the
third-review revision materials, together with the retained training helpers.

| Setting | Value |
| --- | --- |
| Optimizer | AdamW; weight decay `1e-4` |
| Initial learning rates | Shared modules and gesture classifier `1e-3`; discriminators `5e-5` |
| Scheduler | CosineAnnealingWarmRestarts; `T_0=20`, `T_mult=2`, `eta_min=1e-6`; one step per epoch |
| Batch size | 32 |
| Dropout | 0.2 |
| Maximum epochs / early-stopping patience | 200 / 50 |
| Gradient clipping | Global norm 1.0 |
| Checkpoint selection | Highest validation gesture accuracy; keep the earliest tie |
| Default seed | 2024; the manuscript seed set is 2024–2028 |

For one-based epoch `e` and the default 200-epoch budget:

```text
alpha(e) = 2 / (1 + exp(-10 * e / 200)) - 1
lambda_adv(e) = 0                       for epochs 1–10
              = 0.1 * (e - 10) / 190    for epochs 11–200
```

Classification and cosine regularization remain active during warm-up.
Validation selects the gesture checkpoint; test data is evaluated only after
selection. The same deterministic input preparation is used for validation and
test. See [DATA.md](DATA.md) for the split and training augmentation.

## Scope of this release

The code is a cleaned implementation of the documented ENUSP method. It removes
unused branches, historical experiment runners, and augmentations absent from
the paper's method description. It has not been retrained to establish the
manuscript's reported accuracy. No historical checkpoint or experiment result
is represented as an output of this package. In particular, preserving stored
DRAI amplitudes and removing the legacy generic image augmentations makes this
a documented method implementation, not a claim of bit-for-bit historical
training reproduction.
