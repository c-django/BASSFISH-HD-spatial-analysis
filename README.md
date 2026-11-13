# BASSFISH-HD

Analysis and plotting notebooks for *Early Huntington's disease disrupts the positional transcriptional code in medium spiny neurons*.

## Contents

Notebooks are organized by manuscript figure or panel. The repository includes both figure-generation notebooks and selected upstream analysis workflows required to reproduce the corresponding panels. Supplementary-only analyses are outside this package.

| Notebook | Contents |
| --- | --- |
| [Fig1_BASSFISH_workflow_and_detection.ipynb](Fig1_BASSFISH_workflow_and_detection.ipynb) | Decoded transcript maps, density maps, mHTT spot detection, and segmentation overlays |
| [Fig2D_cross_platform_integration_and_annotation.ipynb](Fig2D_cross_platform_integration_and_annotation.ipynb) | Integration of scRNA-seq, lower-plex BASSFISH, and higher-plex BASSFISH; Harmony clustering; UMAP; marker dot plot; and technology-specific versus integrated annotation agreement |
| [Fig2E_CAST_spatial_domain_identification.ipynb](Fig2E_CAST_spatial_domain_identification.ipynb) | CAST graph embedding, 20 major spatial domains, region-specific K-means refinement, and per-slice spatial-domain maps |
| [Fig3B_F_MSN_subclustering_and_SM_diffusion_axis.ipynb](Fig3B_F_MSN_subclustering_and_SM_diffusion_axis.ipynb) | Hierarchical striatal MSN refinement, final MSN-subtype UMAP, D1 S/M program scoring, diffusion-map DC1, and DC1–S/M score correlations |
| [Fig3H_manual_striatal_subregions_and_anatomical_axes.ipynb](Fig3H_manual_striatal_subregions_and_anatomical_axes.ipynb) | Manual dorsal striatum/NAcc/OT annotations, interactive boundary drawing, and normalized anterior–posterior and dorsal–ventral anatomical coordinates |
| [Fig3_Normal_striatal_MSN_spatial_gradients.ipynb](Fig3_Normal_striatal_MSN_spatial_gradients.ipynb) | Cell-type UMAPs, spatial maps, S/M scores, DC1 correlations, and WT anatomical-gradient plotting |
| [Fig4_Striosome_matrix_changes_in_HD.ipynb](Fig4_Striosome_matrix_changes_in_HD.ipynb) | Tshz1/Id4 maps, S-minus-M maps, DC1 profiles, gene heatmaps, and spatial CV |
| [Fig5Q_GO_spatial_pattern_terms_and_scores.ipynb](Fig5Q_GO_spatial_pattern_terms_and_scores.ipynb) | D1/D2 GO Biological Process libraries, GO scoring across ten MSN anatomical axes, WT spatial-pattern filtering, redundancy reduction, and representative GO heatmaps |
| [Fig5_Anatomical_gene_expression_gradients_in_HD.ipynb](Fig5_Anatomical_gene_expression_gradients_in_HD.ipynb) | Anatomical gene heatmaps, category profiles, GO-score heatmaps, and spatial-map examples |
| [Fig6_MSN_spatial_homogenization_and_disorganization.ipynb](Fig6_MSN_spatial_homogenization_and_disorganization.ipynb) | D1/D2 heatmaps and profiles, CV distributions, and five-axis CV radar plots |

Input data, large intermediate h5ad files, MATLAB helper functions, manually drawn boundary JSON files, and final assembled figure PDFs are not included unless stated otherwise.

## Software

Core analysis software versions used in the original workflows:

| Software | Version |
| --- | --- |
| Python | 3.9 |
| Scanpy | 1.10.3 |
| NumPy | 1.22.3 |
| pandas | 1.5.3 |
| SciPy | 1.8.1 |
| Matplotlib | 3.7.1 |
| MATLAB | R2020a |

Additional dependencies used by individual notebooks include AnnData, seaborn, scikit-learn, Harmony integration through `scanpy.external`, CAST, PyTorch, Palantir, IPython/Jupyter, `tqdm`, `tifffile`, and a pandas-compatible Parquet engine such as `pyarrow`. Exact versions for these additional packages are not specified in the source notebooks.

Plotting uses Arial where available. No environment lockfile is included.

## Running the notebooks

1. Place the required input data at accessible paths and open the selected notebook.
2. Check the input/output path variables at the beginning of each workflow. The newly added analysis notebooks use project-relative `data/` and `outputs/` paths; some older plotting notebooks retain local Windows/Linux path examples that must be edited before use.
3. Start with a fresh Python kernel and run each workflow from its imports and configuration cells through its output cells in order.
4. In Figure 1, run the MATLAB examples displayed in Markdown separately in MATLAB. Spot detection requires the external `bpass` and `pkfnd` functions, which are not included.
5. Figure 2D reproduces the recorded cross-platform integration and hierarchical refinement steps. The final manuscript UMAP and marker dot plot use a manually cleaned integrated h5ad because part of the final annotation cleanup was not fully encoded in the original clustering notebook.
6. Figure 2E runs CAST jointly across BASSFISH sections. `CAST_MARK` is the computationally intensive step. Cell ordering must remain identical between the sample-wise CAST embeddings and the concatenated AnnData object when domain labels are mapped back to cells.
7. Figure 3B/F prefixes cell IDs with batch before refinement. Confirm that the supplied input IDs have not already been prefixed. The supplied diffusion-map source code contains the complete D1 implementation; a separate D2 diffusion-map execution block was not present in the source notebook.
8. Figure 3H requires manual reference contours for each applicable sample and region. Dorsal striatum and NAcc use both x and y axes; OT uses x only. Existing legacy `VM_curve_points` / `DL_curve_points` JSON files can be read by the notebook, but their anatomical polarity should be checked before downstream analysis.
9. Figure 5Q requires a ten-row `GO_axis_matrix_manifest.csv` linking each D1/D2 × anatomical-axis combination to its upstream gene-level spatial matrix. Run the GO workflow in order: term-library construction → term scoring → WT spatial-pattern identification → redundancy reduction → heatmap export.
10. In Figure 6, run the CV-distribution workflow before the radar workflow. Preferred animal-summary CSVs, if present at the configured paths, take precedence over summaries rebuilt from the CV/RDR CSVs.

Some workflows export additional conditions, cell types, axes, intermediate h5ad files, or diagnostic plots. Select the outputs corresponding to the intended manuscript panel. Schematics, panel lettering, tissue outlines, Allen reference images, and final multi-panel figure assembly are not generated automatically by these notebooks.

## Required input data

All large inputs must be provided separately.

| Figure / panel | Required inputs |
| --- | --- |
| 1 | `rois.csv`; decoded `*.final.parquet` transcript tables with `global_x` and `global_y`; nuclear and expanded-label TIFFs; an mHTT image; and a spot CSV with `x`, `y`, and `brightness` headers. The ROI MATLAB example requires `hd240625_s94_c1_decode.h5`, `hd240625_s94_c2_decode.h5`, and `hd240625_s94_c3_decode.h5`. |
| 2D | Higher-plex BASSFISH annotated h5ad (`1000genes/integrated_major_clustered_refined_domain_temp.h5ad`); lower-plex BASSFISH annotated and raw-count h5ad files; scRNA-seq annotated and raw-count h5ad files; and the final cleaned integrated annotation h5ad used for manuscript plotting. |
| 2E | `integrated_refined_clustered_250519.h5ad` containing a `.raw` matrix and `obs['sample']`, `obs['x']`, and `obs['y']`. The notebook generates `norm_1e4`, CAST embeddings, major domains, and refined domains. |
| 3B/F | `str_neuron_refined_clustered.h5ad` containing `refined_cluster`, `batch`, `total_counts`, and `.raw`; plus `integrated_refined_cluster_refined_domain_20250605.h5ad` containing `sample`, `condition`, and `refined_domain_3`. |
| 3H | `hd_cut_cortex/adata_with_region_str_manual.h5ad` containing `sample`, `region_str_manual`, and `obsm['position']` or `obs['x']`/`obs['y']`; manually drawn boundary JSON files are required to calculate final 0–100 coordinates. |
| 3 plotting | `integrated_str_final_with_D1_D2_DC.h5ad`; `integrated_5_str_with_sample_level_per_cell_layers.h5ad`; `integrated_5_str_with_axis_xy.h5ad` |
| 4 | `d1_sm_data_final.h5ad`; `d2_sm_data_final.h5ad`; `integrated_5_str_with_axis_xy.h5ad` |
| 5Q | `MSN_D1_expressed_genes.txt`; `MSN_D2_expressed_genes.txt`; `go-basic.obo`; `MOUSE-mod.gaf.gz`; `GO_axis_matrix_manifest.csv`; and ten upstream gene-level spatial matrices for D1/D2 across dorsal-striatal x/y, NAcc x/y, and OT x axes. |
| 5 plotting | `1228genes_x_y_axis.h5ad`; GO-score or representative-term outputs required by the selected plotting workflow; exact single-gene spatial-map inputs depend on the requested panel. |
| 6 | `1228genes_x_y_axis.h5ad`; CV/RDR CSVs generated by the CV-distribution workflow, or compatible animal-summary CSVs |

## Analysis-specific data conventions

### Cross-platform integration

Figure 2D uses three technology labels:

- `scrna`: scRNA-seq
- `h_bass`: lower-plex BASSFISH
- `t_bass`: higher-plex BASSFISH

Only genes shared by all three datasets are retained before concatenation. Harmony integration uses technology and technology-specific batch covariates. The final manuscript plots use `refined_combined_cluster`.

### CAST spatial domains

Figure 2E first identifies 20 major CAST domains and then refines selected domains in CAST embedding space. The first-level final annotation is stored in `refined_domain`. The exploratory second-level `refined_domain_2` workflow from the source development notebook is not part of the main Figure 2E reproduction notebook.

### MSN refinement and S–M diffusion axis

Figure 3B/F generates the final striatal MSN subtype annotation in `refined_cluster_4`. The recorded Figure 3F D1 workflow uses the second Palantir diffusion eigenvector, stored as `dc_component1`, as the S–M diffusion coordinate. The release notebook orients DC1 so that the S program increases toward the striosome-rich end and rescales it to 0–100.

### Anatomical axes

Anatomical analyses use:

- `d`: dorsal striatum
- `n`: nucleus accumbens (NAcc)
- `o`: olfactory tubercle (OT)

The five manuscript anatomical axes are:

- `d-x`: anterior → posterior
- `d-y`: dorsal → ventral
- `n-x`: anterior → posterior
- `n-y`: dorsal → ventral
- `o-x`: anterior → posterior

For each sample and region, manually drawn reference contours define the 0 and 100 ends of the corresponding axis. The normalized coordinate is calculated from the relative shortest distances to those two contours. Final columns are:

- `region_manual_axis_x_0_100`
- `region_manual_axis_y_0_100`

For compatibility with older downstream notebooks, `region_manual_axis_0_100` is retained as an alias of the x-axis coordinate.

### GO spatial programs

Figure 5Q performs the GO analysis explicitly across ten combinations: D1 and D2 for each of the five manuscript anatomical axes.

The GO references used by the workflow are:

- `go-basic.obo`: format version 1.2; data release 2026-07-26
- `MOUSE-mod.gaf.gz`: GAF version 2.2; generated by UniProt on 2026-08-04

The primary GO Biological Process libraries retain terms containing 5–50 measured genes. A term must contain at least five genes available in an axis-specific matrix to receive a score.

GO scores are the unweighted mean of the upstream gene-level relative log-ratio spatial profiles. Raw unsmoothed scores are used to classify WT spatial patterns. A centered 3-bin moving average is used only for visualization.

A WT term is classified as a robust spatial pattern when all four criteria are satisfied:

- sample direction consistency ≥ 0.75
- absolute correlation of the WT mean profile with spatial position ≥ 0.50
- member-gene direction consistency ≥ 0.60
- gene leave-one-out direction stability ≥ 0.80

Positive mean slope is classified as `low_to_high`; negative mean slope as `high_to_low`.

Redundancy reduction is applied after spatial-pattern classification. Highly overlapping terms are grouped using gene-set similarity, and one representative term is selected per redundancy cluster for compact visualization.

## Panel coverage and configuration notes

These notebooks reproduce the recorded analysis and plotting workflows, but some manuscript panels also contain schematics, image exports, manual curation, or final assembly steps that are not encoded as executable Python.

| Panel | Coverage or configuration to check |
| --- | --- |
| 1A | The scatter colors transcript coordinates by factorized gene identity. The manuscript legend describes total signals per cell; that coloring routine is not included. `rois.csv` must be provided or generated with the embedded MATLAB example. |
| 1B-D; 1H raw fluorescence | Separate image-export scripts are not included. The panel H segmentation/spot overlay is included. |
| 1E | Scripts for the distance histogram, GMM filtering example, and retained/discarded Drd1 spatial plots are not included. |
| 1F | The whole-brain gene list includes `Ccdc153` in addition to the genes listed in the manuscript legend. |
| 1H overlay | MATLAB writes a headerless three-column spot CSV. Add the `x`, `y`, and `brightness` headers required by the Python overlay input. External `bpass` and `pkfnd` functions are not included. |
| 2D | The recorded integration/subclustering workflow is included, but the final manuscript UMAP and marker dot plot load a manually cleaned integrated h5ad because some final annotation cleanup was not fully encoded in the original source notebook. The release notebook also distinguishes the original row-normalized annotation contingency table from true cell-set Jaccard similarity. |
| 2E | The main workflow ends at the first-level `refined_domain` annotation. Exploratory genotype-comparison code and the second-level `refined_domain_2` development workflow are not required for the panel and are not included. |
| 3B | The hierarchical MSN refinement and explicit cluster-to-subtype mappings are included. |
| 3F | The supplied source notebook contains the complete D1 S/M diffusion-map implementation. A separate D2 execution block was not present in the supplied source code. |
| 3H | Manual Dorsal/NAcc/OT annotation is an upstream input. The notebook reproduces the interactive reference-boundary drawing and 0–100 axis calculation. Boundary placement remains manual and sample-specific. |
| 3I | A spatial-map example is included in the original Figure 3 plotting notebook; exact final gene/region calls are not all encoded there. |
| 4B | Expression is read from `norm_1e4`; the manuscript legend should be checked if it refers to raw counts. |
| 4C | The original plotting notebook defaults to raw S-minus-M scores and should be checked against the final manuscript color limits. |
| 4E | Expression falls back to `.X` when `norm_1e4` is absent. Panel 4F has an explicit linear-expression requirement. |
| 5B/H/N | The original Figure 5 plotting notebook includes a 191-gene spatial-map example; exact higher-plex single-gene calls depend on the input file and panel-specific settings. |
| 5C/E/I/K/O; 6A | The original plotting workflow supports both relative-mean and row-z-score heatmaps. Confirm the mode used in the final manuscript panel. |
| 5Q | GO gene-set construction, term scoring, WT spatial-pattern identification, redundancy reduction, and representative-term heatmaps are now included in `Fig5Q_GO_spatial_pattern_terms_and_scores.ipynb`. The older Figure 5 plotting notebook may retain different heatmap display defaults; use the configuration matching the final manuscript figure. |
| 6D | The original batch also contains an OT DV (`o-y`) diagnostic outside the five manuscript axes. Its plotted values may be clipped for visualization; exported statistics are unaffected. |
| 6E | The radar workflow may retain an older output filename from figure development. Preferred summary tables at configured paths are loaded before summaries rebuilt from CV/RDR CSVs. |

Figure 5 spatial-map genes in the original plotting workflow include Coch/Galntl6/Lamp5/Oprm1 (dorsal striatum), Galntl6/Erbb4/Htr4/Tshz2 (NAcc), and Cnr1/Fign/Sema5b (OT). Panel-specific input files, expression layers, annotation columns, and coordinate transformations should be checked before reproducing the final single-gene panels.
