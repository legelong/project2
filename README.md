# PSAMA: MATLAB Code and Experimental Data

This repository provides the MATLAB implementation, benchmark instances, saved initial populations, archived experimental results, and supporting analyses for PSAMA research on time-of-use-aware distributed heterogeneous flexible job shop scheduling.

The method combines evolutionary search with terminal **CUT -> exact one-block CBJ1 -> CUT** refinement. The release contains the materials needed to inspect the reported evidence and assemble the original MATLAB project structure.

**Experimental release:** October 12, 2026.  
**Documentation:** This English README integrates the README files supplied in `代码.zip` (code) and `数据.zip` (data). It does not introduce algorithm or result changes.

## 1. Download and repository layout

The code and data are distributed separately:

| Archive | Extracted folder | Contents |
| --- | --- | --- |
| `代码.zip` | `代码/` | MATLAB projects, development-validation implementations, optional Python analysis scripts, and release-management utilities |
| `数据.zip` | `数据/` | Benchmark instances, saved initial populations, raw MAT results, protocols, CSV/Excel evidence tables, figures, and historical records |

Extract both archives into the **same parent directory**. If you cloned the repository, extract the archives before following the MATLAB commands below. Reading files directly inside a ZIP is not sufficient to run the projects.

```text
PSAMA/
├── README.md
├── 代码.zip
├── 数据.zip
├── 代码/
│   ├── README.md
│   ├── verify_release.m
│   ├── prepare_workspace.m
│   ├── 01_MATLAB_PROJECTS/
│   ├── 04_MODERN_COMPARISON/
│   ├── 05_DEVELOPMENT_VALIDATION/
│   └── 07_ANALYSIS/
└── 数据/
    ├── README.md
    ├── 01_MATLAB_PROJECTS/
    ├── 03_EVIDENCE/
    ├── 04_MODERN_COMPARISON/
    ├── 05_DEVELOPMENT_VALIDATION/
    ├── 06_DEVELOPMENT_HISTORY/
    └── 文件哈希清单.json
```

The ZIP files may remain alongside the extracted directories. Preserve the Chinese folder names: the supplied utilities expect the sibling data folder to be named `数据` by default. The manuscript is distributed separately and is not included in either archive.

## 2. Requirements

- MATLAB.
- Optimization Toolbox for the optimization components that require it.
- Parallel Computing Toolbox if running experiments in parallel.
- Python for the optional analysis scripts in `07_ANALYSIS/`; these scripts are not required for the MATLAB quick start.

The archived projects retain their own implementations and configurations. Select the intended project and run its setup function. Avoid adding every project recursively to the MATLAB path, because different frozen projects contain functions with the same names.

## 3. Quick start: assemble a working copy

Set the MATLAB current folder to the extracted `代码/` directory, then run:

```matlab
verify_release;
workspaceRoot = prepare_workspace;
```

`verify_release` checks that ten expected core code/data files exist. It is a release-layout check, not a full SHA-256 verification or an independent reproduction of the scientific results.

`prepare_workspace` copies the code and data into a new sibling directory named `PSAMA_WORKSPACE/`, restoring the relative paths expected by the original projects, including `datasets`, `initial_populations`, and `results`.

The original extracted code and data remain unchanged. The utility refuses to write into a nonempty target directory. It assembles files and does not launch an optimization experiment. Allow additional disk space for this working copy.

To choose explicit locations, call:

```matlab
workspaceRoot = prepare_workspace(dataRoot, newWorkspaceRoot);
```

Here, `dataRoot` is the extracted data directory and `newWorkspaceRoot` must be a new or empty directory.

### Main formal project

```matlab
cd(fullfile(workspaceRoot, '01_MATLAB_PROJECTS', ...
    'PSAMA_FORMAL20_CUT_CBJ1_CUT_v1_MATLAB_6workers'));
setup_project;
run_selfcheck;
summarize_formal20_cut_cbj1_cut;
```

These commands set up the project, run its self-check, and summarize the saved formal results. They do not launch the full formal experiment.

**Preserve the archived results.** Do not run `run_formal20_cut_cbj1_cut_experiment` against the frozen result directories. Any new optimization study should use a separate working copy, output location, and recorded protocol.

### Supplementary MOEA/D-ACN comparison

```matlab
cd(fullfile(workspaceRoot, '04_MODERN_COMPARISON', ...
    'PSAMA_MOEAD_ACN_LITE_v1_2026-10-12'));
setup_moead_acn;
selfcheck_moead_acn;
summarize_moead_acn_results;
postprocess_moead_acn_unified(workspaceRoot);
```

The data archive already contains the completed 20-instance x 5-run search and CUT records. Running `run_moead_acn_20x5` is not necessary to inspect or summarize them. Summarization and postprocessing can write derived tables in the assembled working copy.

## 4. Project and data contents

| Directory | Role |
| --- | --- |
| `01_MATLAB_PROJECTS/` | Four independent frozen projects: FORMAL20, CONT_FINAL_CUT, NSGA-II+CUT, and Stage-II robustness. The code and data folders mirror the same project paths. |
| `03_EVIDENCE/` (data only) | Consolidated main evidence and the separate supplementary MOEA/D-ACN comparison tables. |
| `04_MODERN_COMPARISON/` | Adapted MOEA/D-ACN implementation, experiment entry points, saved initial populations, raw search/CUT results, and summaries. |
| `05_DEVELOPMENT_VALIDATION/` | Selected mechanism studies: 16 A/B/C/D blocks, speed-time block oracles, gain bounds, safe screening, L3-within-L5 containment, CUT-CBJ1 bridging, and fixed-speed retiming/scalability checks. |
| `06_DEVELOPMENT_HISTORY/` (data only) | Selected earlier exploratory results. These are historical records rather than final-method evidence. |
| `07_ANALYSIS/` (code only) | Python scripts for recomputing the extended comparison from archived MAT files and examining reference-front sensitivity. |

The four formal project names are:

```text
PSAMA_FORMAL20_CUT_CBJ1_CUT_v1_MATLAB_6workers
PSAMA_CONT_FINAL_CUT_followup_v1_MATLAB_6workers
PSAMA_NSGA2_CUT_comparison_v1_MATLAB_6workers
PSAMA_STAGE2_robustness_v1_MATLAB_6workers
```

Their separation is intentional. Do not merge shared function names across projects or substitute configurations between them.

## 5. Experimental setting and scope of exactness

The release describes the following main settings:

| Item | Setting |
| --- | --- |
| Benchmark family | 20 Li DHFJSP instances |
| Speed levels | 1-5 |
| Processing duration | `max(1, floor(baseTime / v))` |
| Processing power | `4 * v^2` |
| Time-of-use tariff levels | Off-peak 0.156; normal 0.520; peak 0.884 |
| Internal machine idle power | 1 |
| Main population size | 100 |
| Main search budget | 10,000 function evaluations (NFE) |
| Main repetitions | 20 runs per instance |
| Main seeds | 20262201-20262220 |
| Terminal refinement | CUT -> exact one-block CBJ1 -> CUT |

CUT is exact for its stated retiming subproblem with the structure and speeds fixed. CBJ1 is exact within the specified block neighborhood. These local guarantees do not make the complete evolutionary scheduling method globally exact.

The supplementary MOEA/D-ACN implementation is adapted to the common encoding and TOU/CUT evaluation framework. It should be identified as an adapted implementation rather than the original authors' unmodified algorithm.

## 6. Where to find the evidence

### Frozen main evidence: 20 runs per instance

```text
数据/03_EVIDENCE/PSAMA_Paper_Evidence_Master_2026-10-11/
```

This directory contains the frozen, eight-method evidence based on unified empirical reference fronts, including:

- `PSAMA_Paper_Evidence_Master_2026-10-11.xlsx`: consolidated workbook.
- `data/master_run_metrics_unified.csv`: run-level unified metrics.
- `data/master_empirical_reference_fronts.csv`: empirical reference fronts.
- `data/master_paired_statistics_unified.csv`: paired statistical results.
- `data/master_summary_by_instance_method_unified.csv`: instance/method summaries.
- `data/Table1_Unified_Performance.csv` through `data/Table4_Robustness.csv`: manuscript-oriented tables.
- `data/claim_map.csv` and `data/development_evidence_register.csv`: evidence mappings.
- `figures/` and `source_tables/`: figures and supporting tables.

### Supplementary modern comparison: five paired runs

```text
数据/03_EVIDENCE/Modern_MOEAD_ACN_v5/
```

This directory contains the extended ten-method comparison on five paired runs, recomputed using a new reference front, together with reference-front sensitivity tables and processing records. Files include `TABLE5_per_instance_5run_newref.csv`, `expanded_unified_5runs_all_methods.csv`, and `sensitivity_all_methods_paired5_reference.csv`.

The corresponding saved MOEA/D-ACN search/CUT MAT files are under:

```text
数据/04_MODERN_COMPARISON/PSAMA_MOEAD_ACN_LITE_v1_2026-10-12/
```

## 7. Interpretation and reproducibility rules

1. **Keep reference-front definitions separate.** HV and IGD from the frozen eight-method, 20-run evidence and the later ten-method, five-run evidence must not be mixed as if they used one common evaluation basis.
2. **Keep repetition counts explicit.** The supplementary 20-instance x 5-run comparison does not support the same statistical claims as the main 20-instance x 20-run study.
3. **Distinguish NFE from elapsed time.** Common search budgets do not imply equal CPU time or equal numbers of terminal CUT calls. Consult the runtime and CUT-trace records when comparing computational effort.
4. **Keep development and formal samples distinct.** The 16 development blocks and the 2,312 formally selected schedules are different sample sets and must not be pooled into a single statistical conclusion.
5. **Describe robustness at the correct level.** The robustness study reoptimizes Stage II under scenarios while holding the Stage-I schedules fixed. It does not rerun the entire Stage-I search independently for every scenario.
6. **Separate historical exploration from the final method.** Earlier learning-related results do not imply that the final algorithm uses LinUCB, Q-learning, or another learning controller. Compatibility functions retained inside historical projects do not by themselves establish final-method use.
7. **Preserve frozen records.** Existing formal results need not be rerun to inspect the released evidence. Record new experiments separately rather than overwriting the archived runs.

## 8. Integrity, provenance, and release boundaries

`数据/文件哈希清单.json` contains the recorded SHA-256 values and sizes for the released code/data contents. The source documentation states that README files themselves are excluded from that index. The lightweight `verify_release` utility does not recompute these hashes.

According to the supplied release documentation, the frozen MATLAB source, datasets, and formal MOEA/D-ACN records were copied byte for byte from the source materials; `prepare_workspace.m` and `verify_release.m` were added as release-management utilities. This combined README was prepared by reading the two supplied README files and their referenced layout utilities. No MATLAB experiment was executed for this documentation update.

The curated release excludes superseded learning experiments, replaced bridge versions, abandoned research routes, unrelated SSTCE/PDA source, redundant result archives, and manuscript DOCX/PDF files. The selected historical data remain available only for provenance and development context.

The manuscript is maintained separately. Use the evidence family, reference-front definition, and repetition count corresponding to each reported result when preparing tables or describing the experiments.
