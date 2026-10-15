# BIFMMessagePassingRules

[![CI](https://github.com/ReactiveBayes/BIFMMessagePassingRules.jl/actions/workflows/CI.yml/badge.svg?branch=main)](https://github.com/ReactiveBayes/BIFMMessagePassingRules.jl/actions/workflows/CI.yml)
[![Docs: stable](https://img.shields.io/badge/docs-stable-blue.svg)](https://reactivebayes.github.io/BIFMMessagePassingRules.jl/stable/)
[![Docs: dev](https://img.shields.io/badge/docs-dev-blue.svg)](https://reactivebayes.github.io/BIFMMessagePassingRules.jl/dev/)
[![Coverage](https://codecov.io/gh/ReactiveBayes/BIFMMessagePassingRules.jl/graph/badge.svg)](https://codecov.io/gh/ReactiveBayes/BIFMMessagePassingRules.jl)
[![Code style: Runic](https://img.shields.io/badge/code_style-%E1%9A%B1%E1%9A%A2%E1%9A%BE%E1%9B%81%E1%9A%B2-black)](https://github.com/fredrikekre/Runic.jl)

Backward-information-filter forward-marginal (BIFM) smoothing of a linear state-space model with
known matrices:

- `BIFM`, a whole time slice, `znext = A zprev + B in` and `out = C znext`, as one deterministic
  node, under `BIFMSmoother(A, B, C)`, which a model must give each BIFM node;
- `BIFMHelper`, at the start of the chain, which turns the backward pass into the forward one.

The rules take multivariate normal messages only. **The free energy of a model with BIFM is not
supported**: asking for it throws a `BIFMFreeEnergyError`.

```julia
using BIFMMessagePassingRules, MessagePassingRulesBase, ExponentialFamily

result = @call_message_update_rule(
    node = BIFM, target = :zprev, algorithm = BIFMSmoother([1.0;;], [1.0;;], [1.0;;]),
    m = (
        out = MvNormalMeanPrecision([1.0], [1.0;;]),
        in = MvNormalMeanPrecision([0.0], [1.0;;]),
        znext = MvNormalWeightedMeanPrecision([0.0], [0.0;;]),
    ),
)
getresult(result)   # the backward message on zprev, N(1, 2) in information form
```

```julia
import Pkg; Pkg.add("BIFMMessagePassingRules")
```

- Documentation: <https://reactivebayes.github.io/BIFMMessagePassingRules.jl/stable/>.
  `make docs` builds it locally, into `docs/build`.
- Tests: `make test` runs the suite as CI does, ending with the rule-coverage gate: every rule
  must be selected by some test. `make test test_args="name:rules"` selects items by tag, by
  name or by file; `make help` lists the other targets.
- Julia 1.10 or later. MIT licence.
