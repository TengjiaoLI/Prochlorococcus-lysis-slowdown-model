# Prochlorococcus–cyanophage infection models (PSSP7 × ZT145)

Julia notebooks for mechanistic ODE modelling and random-parameter screening of *Prochlorococcus* (PSSP7)–cyanophage (ZT145) infection dynamics.

The repository contains models corresponding to different mechanisms proposed to explain the pronounced slowdown of host lysis during cyanophage infection, including adsorption inefficiency, eclipse–lysis asymmetry, host resistance evolution, and their integration into a unified three-mechanism framework.

Each notebook implements a mechanistic hypothesis (or their combination), samples parameter sets, solves the ODEs, evaluates model fits against experimental host and phage time-series data, and exports high-scoring parameter combinations.

> **Language:** Julia
> **Data:** Experimental and model-derived datasets are provided in the repository.
> **Large supplementary datasets:** S3–S6 are stored using Git LFS.

---

## Repository contents

| File                               | Mechanism / role                                    | Key extra parameter(s) | State variables     |
| ---------------------------------- | --------------------------------------------------- | ---------------------- | ------------------- |
| `pro_virus-pssp7-highMOILN.ipynb`  | Baseline / high-MOI reference model                 | —                      | Su, Ex, In, Vi      |
| `pro_virus-pssp7-adsorp.ipynb`     | Adsorption inefficiency                             | ξ (adsorption success) | Su, Ex, In, Vi      |
| `pro_virus-pssp7-eclipse.ipynb`    | Eclipse–lysis asymmetry                             | λe, λl                 | Su, Ex, In, Vi      |
| `pro_virus-pssp7-resistance.ipynb` | Resistance evolution                                | μ_re (resistance cost) | Su, Ex, In, Vi, Re  |
| `pro_virus-pssp7-combined.ipynb`   | Integrated three-mechanism framework                | ξ, λe, λl, μ_re        | Su, Ex, In, Vi, Re  |
| `PSSP7_ZT145.xlsx`                 | Experimental time-series data used by the notebooks | —                      | Host & Virus sheets |

**State variables:** Su = susceptible host, Ex = exposed host, In = infected host, Vi = free virus, Re = resistant host.

---

## Supplementary datasets

The `supplementary_datasets/` directory contains the large supplementary datasets associated with the mechanistic modelling and model calibration described in the manuscript.

| Dataset              | Description                                                                                                                                                                     |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SI Dataset S3.xlsx` | Observed host–virus time-series data used for model calibration, together with ensemble simulation trajectories for the host-resistance and adsorption-inefficiency (ξ) models. |
| `SI Dataset S4.xlsx` | Observed host–virus time-series data used for model calibration, together with ensemble simulation trajectories for the host-resistance and eclipse–lysis asymmetry models.     |
| `SI Dataset S5.xlsx` | Observed host–virus time-series data used for model calibration, together with ensemble simulation trajectories for the host-resistance and high-MOI/superinfection models.     |
| `SI Dataset S6.xlsx` | Observed host–virus time-series data used for model calibration, resistance-model ensemble simulation trajectories, and simulations from the integrated three-mechanism model.  |

### Data organization

The supplementary datasets include experimental observations and model simulation results used to evaluate the proposed mechanisms and the integrated model.

Depending on the dataset, columns may include:

* Time points
* Solution / simulation index
* Best-fit indicator
* Observed host abundance
* Observed virus abundance
* Predicted host abundance
* Predicted virus abundance
* Susceptible host abundance
* Exposed host abundance
* Infected host abundance
* Resistant host abundance
* Total host abundance

Units and column definitions should be interpreted according to the corresponding dataset and analysis described in the manuscript.

### Large-file storage

The four supplementary Excel files are stored using **Git Large File Storage (Git LFS)** because of their file sizes.

To clone and access the large datasets, Git LFS should be installed before cloning the repository.

---

## Experimental data (`PSSP7_ZT145.xlsx`)

The smaller experimental dataset used directly by the Julia notebooks is provided as:

`PSSP7_ZT145.xlsx`

| Sheet | Columns                              | Description                             |
| ----- | ------------------------------------ | --------------------------------------- |
| Host  | Viral inoculation time, Rep 1, Rep 2 | Host cell density time series           |
| Virus | Time, Rep 1, Rep 2                   | Extracellular phage density time series |

The notebooks load these sheets using:

```julia
med4 = DataFrame(XLSX.readtable("PSSP7_ZT145.xlsx", "Host"))
virus = DataFrame(XLSX.readtable("PSSP7_ZT145.xlsx", "Virus"))
```

Place `PSSP7_ZT145.xlsx` in the same directory as the notebooks before running them.

---

## Requirements

### Julia

The workflow was developed and tested using Julia 1.x.

### Required packages

Install the required packages in the Julia REPL:

```julia
using Pkg

Pkg.add([
    "DataFrames",
    "CSV",
    "XLSX",
    "Statistics",
    "DifferentialEquations",
    "StatsPlots",
    "LinearAlgebra",
    "Random",
    "Dates",
    "NaNMath",
    "Turing"
])
```

The notebooks can be opened using Jupyter, VS Code, or other Julia environments with compatible notebook support.

For Jupyter-based workflows, `IJulia` may also be installed:

```julia
using Pkg
Pkg.add("IJulia")
```

---

## How to run

1. Clone this repository and enter the repository directory.
2. Ensure that `PSSP7_ZT145.xlsx` is located in the same directory as the notebooks.
3. Open the desired Julia notebook.
4. Run the notebook cells from top to bottom.
5. The typical workflow is:

   * Load packages and experimental data
   * Define the ODE model
   * Specify parameter ranges
   * Sample random parameter sets
   * Solve the ODE system
   * Evaluate model performance against experimental host and virus trajectories
   * Retain solutions that pass predefined filters
   * Identify high-scoring or best-fitting parameter combinations
   * Plot model trajectories against experimental observations
   * Export accepted or best-fitting parameter sets

### Note on `vpro_parameters.csv`

`pro_virus-pssp7-eclipse.ipynb` may attempt to read `vpro_parameters.csv` for host-growth priors. If this file is unavailable, the notebook can fall back to alternative paths or default values depending on the code version. Adjust the corresponding path or parameter cell if necessary.

---

## Model mechanisms

| Notebook     | Biological mechanism                                                                                       |
| ------------ | ---------------------------------------------------------------------------------------------------------- |
| `highMOILN`  | Baseline infection model under high MOI, including MOI-linked infection/latency dynamics.                  |
| `adsorp`     | Adsorption inefficiency (ξ): only a fraction of adsorption events result in productive infection.          |
| `eclipse`    | Eclipse–lysis asymmetry: separate eclipse (λe) and lysis (λl) timescales after infection.                  |
| `resistance` | Evolution of phage resistance, including a fitness cost associated with resistant hosts (μ_re).            |
| `combined`   | Integrated model incorporating adsorption inefficiency, eclipse–lysis asymmetry, and resistance evolution. |

The integrated model is used to evaluate how multiple mechanisms operating on different timescales collectively contribute to host persistence and the observed slowdown in host lysis.

Shared host–virus traits typically include:

`μmax, Lopt, α, KL, ω, K, ϕ, β, δ`

with additional mechanism-specific parameters described below.

---

## Parameter vectors

The approximate order of parameters in `p` is:

| Notebook     | Parameter order                                   |
| ------------ | ------------------------------------------------- |
| `highMOILN`  | μmax, Lopt, α, KL, ω, K, ϕ, β, λ, δ               |
| `adsorp`     | μmax, Lopt, α, KL, ω, K, ϕ, β, λ, δ, ξ            |
| `eclipse`    | μmax, Lopt, α, KL, ω, K, ϕ, β, λe, λl, δ          |
| `resistance` | μmax, Lopt, α, KL, ω, K, ϕ, β, λ, μ_re, δ         |
| `combined`   | μmax, Lopt, α, KL, ω, K, ϕ, β, λe, λl, δ, ξ, μ_re |

> **Note:** Parameter ordering may change if the notebooks are modified. Always check the parameter-definition cell in the corresponding notebook before reproducing an analysis.

---

## Typical outputs

Running the notebooks may generate files whose names vary according to the script and analysis date, including:

* `*_passed_params_*.csv` — parameter sets that pass ODE-solve and model-fit filters
* `*_best_params_*.csv` — best-scoring parameter vector(s)
* Model trajectories comparing simulated host and phage dynamics with experimental observations
* Diagnostic plots and parameter-screening results

These outputs support downstream analyses of model performance, parameter relationships, and the relative contributions of the proposed mechanisms.

---

## Reproducibility

The repository is intended to provide the modelling code and associated datasets needed to reproduce the computational analyses described in the manuscript.

For the large supplementary datasets, Git LFS is required to retrieve the complete Excel files.

After cloning the repository, verify that the large files have been downloaded correctly using:

```bash
git lfs ls-files
```

---

## Contact / issues

For questions about model structure, parameter screening, data interpretation, or reproducibility, please open a GitHub Issue or contact the repository maintainers.

---

## 中文简介

本仓库提供 *Prochlorococcus*（PSSP7）与蓝藻病毒（ZT145）感染动力学的 Julia ODE 模型及参数筛选代码。

模型分别用于研究以下机制：

* `highMOILN`：基准高 MOI 感染模型
* `adsorp`：噬菌体吸附效率降低（ξ）
* `eclipse`：Eclipse phase 与裂解时间尺度不对称（λe, λl）
* `resistance`：宿主噬菌体抗性演化及其适合度代价（μ_re）
* `combined`：三种机制的整合模型

其中，`combined` 模型将**吸附效率降低、Eclipse–lysis 时序不对称和宿主抗性演化**整合到统一的动态模型中，用于解释感染后期宿主裂解速率下降以及宿主–噬菌体持续共存的动态过程。

### 数据

实验时间序列数据见：

`PSSP7_ZT145.xlsx`

大型补充数据集见：

`supplementary_datasets/`

包括：

* `SI Dataset S3.xlsx`：抗性模型与吸附效率模型相关的实验数据及模型集成模拟结果
* `SI Dataset S4.xlsx`：抗性模型与 Eclipse–lysis asymmetry 模型相关的实验数据及模型集成模拟结果
* `SI Dataset S5.xlsx`：抗性模型与高 MOI / superinfection 模型相关的实验数据及模型集成模拟结果
* `SI Dataset S6.xlsx`：抗性模型及三机制整合模型的实验数据和模拟结果

大型数据文件通过 Git LFS 管理。

---

## Citation

If you use the code or datasets in this repository, please cite the associated manuscript and the corresponding repository release when applicable.
