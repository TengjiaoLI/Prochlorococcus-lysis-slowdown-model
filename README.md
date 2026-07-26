# Prochlorococcus-lysis-slowdown-model
# *Prochlorococcus*–cyanophage infection models (PSSP7 × ZT145)

Julia notebooks for ODE modelling and random-parameter screening of *Prochlorococcus* (PSSP7)–cyanophage (ZT145) infection dynamics. Each notebook implements one mechanistic hypothesis (or their combination), samples parameter sets, solves the ODEs, scores fits against experimental host and phage time series, and exports high-scoring parameter combinations.

> **Language:** notebooks are written in **Julia**.  
> Place `PSSP7_ZT145.xlsx` in the **same directory** as the notebooks before running.

---

## Repository contents

| File | Mechanism / role | Key extra parameter(s) | State variables |
|------|------------------|------------------------|-----------------|
| `pro_virus-pssp7-highMOILN.ipynb` | Baseline / high-MOI reference model | — | Su, Ex, In, Vi |
| `pro_virus-pssp7-adsorp.ipynb` | Adsorption inefficiency | ξ (adsorption success) | Su, Ex, In, Vi |
| `pro_virus-pssp7-eclipse.ipynb` | Eclipse–lysis asymmetry | λe, λl | Su, Ex, In, Vi |
| `pro_virus-pssp7-resistance.ipynb` | Resistance evolution | μ_re (resistance cost) | Su, Ex, In, Vi, Re |
| `pro_virus-pssp7-combined.ipynb` | Integrated tripartite framework | ξ, λe, λl, μ_re | Su, Ex, In, Vi, Re |
| `PSSP7_ZT145.xlsx` | Experimental time series | — | Host & Virus sheets |

**Compartments:** Su = susceptible, Ex = exposed, In = infected, Vi = free virus, Re = resistant (when used).

---

## Experimental data (`PSSP7_ZT145.xlsx`)

| Sheet | Columns | Description |
|-------|---------|-------------|
| **Host** | Viral inoculation time, Rep 1, Rep 2 | Host cell density time series (replicates) |
| **Virus** | Time, Rep 1, Rep 2 | Extracellular phage density time series (replicates) |

The notebooks load these sheets with:

```julia
med4  = DataFrame(XLSX.readtable("PSSP7_ZT145.xlsx", "Host"))
virus = DataFrame(XLSX.readtable("PSSP7_ZT145.xlsx", "Virus"))
```

---

## Requirements

- **Julia** (tested workflow with Julia 1.x)
- Packages (install once in the Julia REPL):

```julia
using Pkg
Pkg.add([
    "DataFrames", "CSV", "XLSX", "Statistics",
    "DifferentialEquations", "StatsPlots", "LinearAlgebra",
    "Random", "Dates", "NaNMath", "Turing"
])
```

Optional: open notebooks in **Jupyter** / **VS Code** / **Pluto**-compatible Julia kernels (`IJulia`).

---

## How to run

1. Clone this repository and `cd` into the folder that contains the notebooks.
2. Put `PSSP7_ZT145.xlsx` next to the notebooks (same working directory).
3. Open one notebook and run cells top to bottom.
4. Typical workflow inside each notebook:
   - Load packages and experimental data  
   - Define the ODE model  
   - Sample random parameter sets within prescribed ranges  
   - Solve ODEs and score fits (host + virus)  
   - Keep solutions that pass filters / top scoring fraction  
   - Plot fits vs data and export CSV of accepted / best parameters  

**Note:** `pro_virus-pssp7-eclipse.ipynb` may also try to read `vpro_parameters.csv` for host-growth priors; if that file is absent, the notebook falls back to other paths / defaults—adjust the path cell if needed.

---

## Model mechanisms (brief)

| Notebook | Biological idea |
|----------|-----------------|
| **highMOILN** | Baseline infection model under high MOI (exposed → infected with MOI-linked latency). |
| **adsorp** | Incomplete / unsuccessful adsorption (ξ): only a fraction of adsorption events lead to productive infection. |
| **eclipse** | Separate eclipse (λe) and lysis (λl) timescales after infection. |
| **resistance** | Evolution / emergence of resistant hosts (Re) with a growth cost (μ_re). |
| **combined** | All three mechanisms in one framework (used for unified simulation in the manuscript). |

Shared host–virus traits typically include: μmax, Lopt, α, KL, ω, K, ϕ, β, δ (plus mechanism-specific terms above).

---

## Typical outputs

Running a notebook may generate (names vary by script / date stamp):

- `*_passed_params_*.csv` — parameter sets that pass ODE solve + fit filters  
- `*_best_params_*.csv` — best-scoring parameter vector(s)  
- On-notebook plots of host and phage trajectories vs experimental replicates  

These CSVs support downstream analyses (posterior correlation heatmaps, RDA across mechanisms, etc.) described in the manuscript.

---

## Parameter vectors (order used in `p`)

Approximate orders as coded in the notebooks (check the notebook cell if you modify models):

| Notebook | `p` order |
|----------|-----------|
| highMOILN | μmax, Lopt, α, KL, ω, K, ϕ, β, λ, δ |
| adsorp | μmax, Lopt, α, KL, ω, K, ϕ, β, λ, δ, ξ |
| eclipse | μmax, Lopt, α, KL, ω, K, ϕ, β, λe, λl, δ |
| resistance | μmax, Lopt, α, KL, ω, K, ϕ, β, λ, μ_re, δ |
| combined | μmax, Lopt, α, KL, ω, K, ϕ, β, λe, λl, δ, ξ, μ_re |

---


## Contact / issues

For questions about model structure or parameter screening, open a GitHub Issue or contact the repository maintainers.

---

## 中文简介

本仓库提供 *Prochlorococcus*（PSSP7）与蓝藻病毒（ZT145）感染动力学的 **Julia** 笔记本，分别对应：

- **highMOILN**：基准高 MOI 模型  
- **adsorp**：吸附不完全（ξ）  
- **eclipse**：Eclipse / 裂解时序分离（λe, λl）  
- **resistance**：抗性演化（μ_re）  
- **combined**：三机制整合模型  

实验时间序列见 `PSSP7_ZT145.xlsx`（Host / Virus 两个工作表）。运行前请将 Excel 与 notebook 放在同一目录，在 Julia 中安装 `DifferentialEquations`、`XLSX`、`DataFrames` 等依赖后按单元格顺序执行。
