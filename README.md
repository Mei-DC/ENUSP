# ENUSP

**Explicit Decoupling of Environment, User, and Position for Generalizable Gesture Recognition**

Dachuan Mei, Yongtao Ma, Bobo Wang, Chenglong Tian, and Lele Yin

[中文说明](README.zh-CN.md) · [Request the code](CODE_ACCESS.md) · [Handwritten undertaking](RESEARCH_UNDERTAKING.md)

ENUSP studies mmWave gesture recognition across unseen environments, users, and
positions. Its Purify-then-Align strategy combines a local–global spatiotemporal
encoder, independent feature gates, soft subspace orthogonalization, and three
gradient-reversal branches with adaptive linear balancing.

This repository is the public project and code-access page. The implementation
is distributed by email for research use. It covers the seven-class ENUSP method
and the strict environment–user–position split.

## Obtain the implementation

1. Read the [access conditions](CODE_ACCESS.md).
2. Copy the [research-use undertaking](RESEARCH_UNDERTAKING.md) **by hand**, complete
   the applicant information, and add your handwritten signature and date.
3. Email a legible scan or photograph to **[meidachuan@tju.edu.cn](mailto:meidachuan@tju.edu.cn)**.
   Use the subject `ENUSP Code Request — Name — Institution` and briefly describe
   your research purpose.
4. The ENUSP code package will be sent to the requesting email address **within
   15 calendar days of receipt of a complete handwritten undertaking**.

Send applications by email rather than posting signed documents in GitHub issues.
The package contains the model, data loader, training and evaluation entry points,
configuration, and usage instructions. Obtain the underlying radar dataset
separately from its original provider.

## Method and usage

- [Model and training details](docs/METHOD.md)
- [Dataset layout and fixed split](docs/DATA.md)
- [Running the code after receiving the package](docs/RUNNING.md)

The source package is a compact implementation of the documented method. Its
implementation notes record the configuration and differences from historical
experiment scripts. The cleaned package has not been retrained to establish the
accuracy reported in the manuscript.

## Citation and contact

Please cite the ENUSP manuscript when using this work. Author and title metadata
are provided in [CITATION.cff](CITATION.cff); publication metadata will be added
when confirmed.

For code requests and technical correspondence: **Dachuan Mei**,
**meidachuan@tju.edu.cn**.

Access is subject to the [research-use conditions](CODE_ACCESS.md). A public
project page does not grant unrestricted use or redistribution of the code.
