FIGURE CHECKLIST  -  BTP_Final_Report.tex
=========================================
Create a folder named  figures/  next to the .tex file in Overleaf and upload
these 13 files with EXACTLY these names.

ALREADY GENERATED (in this folder, ready to upload)
  fig_regime_ladder.png      Fig 9  - regime ladder + split-design inflation
  figures_regimeA.png        Fig 10 - ROC / PR / calibration, explicit-feature model
  shap_regimeA.png           Fig 11 - SHAP summary, explicit-feature model

FROM YOUR MID-SEM REPORT (reuse the same image files)
  fig_length.png             Fig 1  - sequence length distribution
  fig_hydrophobicity.png     Fig 2  - Kyte-Doolittle hydrophobicity distribution
  fig_pi_charge.png          Fig 3  - isoelectric point vs net charge per residue
  fig_corr.png               Fig 4  - Spearman correlation matrix
  fig_pca_var.png            Fig 5  - cumulative explained variance
  fig_pca_proj.png           Fig 6  - PC1-PC2 projection
  fig_pc1.png                Fig 7a - PC1 loadings
  fig_pc2.png                Fig 7b - PC2 loadings

DOWNLOADED FROM YOUR COLAB RUNS (you already have these)
  conc_response.png          Fig 12 - mean concentration response (phase 3c)
  csat_logfixed.png          Fig 13 - c_sat distribution, log grid (phase 3c)
  band_anchor_scan.png       Fig 14 - anchor weight scan (phase 3d)
  salt_reentrance_fixed.png  Fig 15 - salt response + reentrant profiles (phase 3c)
  data_support.png           Fig 16 - data support histograms (phase 3c)

Placeholder images with these names are included so the document compiles
immediately. Overwrite each one as you upload the real figure.

LENGTH ADJUSTMENT
=================
The report compiles to 48 pages. To shorten it, change these in the preamble:

  \setstretch{1.03}   -> 0.98        saves roughly 2 pages
  \setlength{\parskip}{0.3em} -> 0.2em   saves roughly 1 page
  \documentclass[11pt,...] -> [10pt,...] saves roughly 4 pages

To cut content instead, Appendix C (Reproducibility) and Appendix B.1 are the
least essential, worth about 2 pages together.
