# Sex differences in the human kidney: donor-level re-analysis

Code for the manuscript "Reproducible sex differences in adult human kidney gene expression are concentrated in the proximal tubule, are small, and are largely not shared with the mouse" (Li, Ji, Zhao, Bao, Wang and Yuan). All input data are public; no controlled-access data are used.

## Layout

Unpack this archive into an empty project folder. Scripts find the project folder automatically (the parent of `processing/` and `analysis/`); set the environment variable `TOPIC4_ROOT` to use a different location.

```
<project>/
  processing/   downloads, pseudobulk tables, DESeq2 fits (01-13)
  analysis/     statistics, supplementary tables, figures (A01-A18)
  data/         downloaded .h5ad files (created by processing/02)
  results/      intermediate tables (created by the processing scripts and A06/A08)
  literature/   Source Data of Chen et al. 2025 (added by hand, see below)
  work/         analysis outputs (created by the analysis scripts)
      fig/            figure data (.pkl)
      figures/        Figures 1-5, Supplementary Figure 1 (.pdf, .png)
      supplementary/  Supplementary Data and Source Data
```

## Software

Python 3.10 or 3.11 with the packages in `requirements.txt`. DESeq2 fits used PyDESeq2 0.5.2 (numpy 2.2.6, pandas 2.3.3, scipy 1.15.2, Python 3.10). The processing scripts install PyDESeq2 with pip if it is missing.

## Inputs not downloaded by the scripts

`literature/Source Data Fig. 3.xlsx`: the Source Data file for Fig. 3 of Chen et al., Nature Genetics 2025, downloaded from the article page. It contains the published mouse proximal tubule sex-biased gene table.

## Run order

Each script is run from any directory with `python <script>`. Processing scripts can be stopped and restarted; finished parts are skipped.

| Step | Script | Main outputs |
|---|---|---|
| 1 | processing/01_kidney_donor_tables.py | results/donors_*.csv |
| 2 | processing/02_download_and_pseudobulk.py | data/*.h5ad (about 23 GB), results/pseudobulk/ |
| 3 | processing/03_gene_annotation.py | results/gene_annotation.csv |
| 4 | processing/04_mouse_atlas.py | results/pseudobulk/hkav2_mouse__*, results/orthologs_human_mouse.csv |
| 5 | processing/05_cell_level_vs_donor_level.py | results/cell_level/ |
| 6 | processing/06_published_study_materials.py | results/cell_level/lake2023_sc_v1__all_genes.csv |
| 7 | processing/08_per_donor_pt_tables.py | results/per_donor_pt/ |
| 8 | processing/12_fetch_annotations.py | results/annotations/ (GO, Reactome) |
| 9 | analysis/A01_sexcheck.py | work/sexcheck_*.csv (XIST and Y-linked genes) |
| 10 | analysis/A02_donors.py | work/donors_sn.pkl (donor groups, exclusions) |
| 11 | analysis/A03_linear_discovery.py | linear log-CPM discovery models (initial analysis) |
| 12 | analysis/A04_external_cohorts.py | work/ext.pkl (six external cohorts) |
| 13 | analysis/A05_mouse.py | work/mouse.pkl |
| 14 | analysis/A06_deseq_inputs.py | results/deseq2_inputs/ |
| 15 | processing/10_deseq2_recheck.py | results/deseq2/ (28 human analyses, mouse, permutations) |
| 16 | analysis/A07_discovery_deseq.py | work/n1_stats.pkl, n1_summary.csv |
| 17 | analysis/A08_replication_inputs.py | results/deseq2_rep_inputs/ |
| 18 | processing/11_deseq2_replication.py | results/deseq2_rep/ (replication fits, 100 permutations each) |
| 19 | analysis/A09_replication.py | replication, consensus set, replicated core, leave-one-cohort-out; Supplementary Data 1-2 |
| 20 | analysis/A10_interaction.py | sex x CKD interaction; Supplementary Data 4 |
| 21 | analysis/A11_gene_sets.py | gene-set analysis; Supplementary Data 3 |
| 22 | analysis/A12_pseudocount_diagnostics.py | library size by sex, pseudocount strata |
| 23 | analysis/A13_chen_reanalysis.py | re-analysis of Chen et al.; Supplementary Data 5; work/fig/data5*.pkl |
| 24 | processing/13_human_downsample.py | results/deseq2_downsample/ (9 F + 9 M, 50 draws) |
| 25 | analysis/A14_figure_data.py | work/fig/data1.pkl, data_v2.pkl |
| 26 | analysis/A15_figures.py | work/figures/ |
| 27 | analysis/A16_source_data.py | work/supplementary/Source_Data.xlsx |
| 28 | analysis/A17_downsampling.py | work/supplementary/human_downsampling.csv |
| 29 | analysis/A18_supplementary_data.py | work/supplementary/Supplementary_Data_1-6.xlsx |

Shared code: `analysis/config.py` (paths), `analysis/lib.py` (loading and statistics helpers), `analysis/chen_common.py` (published mouse table and per-donor tables for step 23), `analysis/pubstyle.py` (figure style).

## Random seeds

All random draws use fixed seeds written in the scripts (permutations, down-sampling, cell subsampling), so outputs are reproducible with the package versions above.

## Data sources

Human Kidney Atlas v2 (Lake et al. 2025; KPMP and HuBMAP) and the other human and mouse data sets are downloaded from CELLxGENE Discover by `processing/02` and `processing/04`; Lake et al. 2023 single-cell and single-nucleus data by `processing/05` and `processing/08`; gene annotation, GO and Reactome by `processing/03` and `processing/12`. Exact dataset identifiers are in the scripts.

## License

MIT (see LICENSE).
