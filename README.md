# Reproducing "Data formats in analytical DBMSs": Parquet vs ORC on TPC-DS

AI508 Big Data Technologies and Applications – Assignment 2 (Reproduction and Validation of a Big Data Research Study)

This repository contains the code, setup instructions and results for our partial reproduction of:

> C. Liu, A. Pavlenko, M. Interlandi and B. Haynes, "Data formats in analytical DBMSs: performance trade-offs and future directions," *The VLDB Journal*, vol. 34, art. no. 30, 2025. https://doi.org/10.1007/s00778-025-00911-1

## What we reproduced

We compared the Apache **Parquet** and Apache **ORC** file formats on TPC-DS data with **Snappy** compression, using Apache Spark. There were two experiments:

1. **File size:** how much disk space five TPC-DS tables take in each format, measured as the compression ratio (saved size ÷ original size). This corresponds to Sect. 5.1.2 and Fig. 10 of the paper.
2. **Write and read time:** how long each format takes to write each table and to read it back with `count()`. This corresponds to Sects. 5.2.1–5.2.2 of the paper.

Both experiments were run at three data sizes (TPC-DS scale factors 1, 2 and 5) on five tables: `customer`, `item`, `store`, `date_dim` and `store_sales`.

## Dataset

We used pre-generated TPC-DS data from the public repository **[alamb/tpcds-data](https://github.com/alamb/tpcds-data)**, which has one branch per scale factor (`sf1`, `sf2`, `sf5`). The data is **not** stored in this repository because it is several GB. The notebook downloads it automatically.

## Environment

| Item | Details |
|---|---|
| Platform | Google Colab (standard hosted runtime) |
| CPU | Intel Xeon @ 2.20 GHz, 2 logical CPUs |
| Memory | 12 GB RAM |
| Disk | 108 GB |
| Python | 3.13.18 |
| PySpark | 4.0.5 (Spark local mode) |
| pandas | 2.2.3 |
| PyArrow | 23.0.1 |

The hardware details come from the notebook's last cell (`lscpu`, `nproc`, `free -h`, `df -h`), which was run after the experiments. Colab can assign a different machine to each session.

## How to run

1. Open `notebooks/BD_Assignment2_v8.ipynb` in Google Colab (**File → Open notebook → GitHub**, then paste this repository's URL).
2. Optional: install the exact versions we used by adding a first cell with `!pip install -q -r requirements.txt`, or `!pip install -q pyspark==4.0.5 pyarrow==23.0.1 pandas==2.2.3`.
3. Choose **Runtime → Run all**.
   - The first cell lists the branches available in the TPC-DS data repository.
   - The next three cells download SF1, SF2 and SF5 and run the file-size experiment.
   - The next three cells run the write/read timing experiment for SF1, SF2 and SF5.
   - The last cell prints the machine's CPU, memory and disk details.
4. Results are printed as tables below each cell.

Notes:
- The SF5 download and conversion take the longest to run.
- Timing results will vary between runs and machines. File sizes should match exactly with the same software versions.

## Repository structure

```
notebooks/
  BD_Assignment2_v8.ipynb             # all experiment code, saved with outputs
results/
  file_size_results.csv               # Experiment 1: sizes and compression ratios
  write_read_time_results.csv         # Experiment 2: write and read times (seconds)
  figures/
    fig1_total_size.png               # total size of the five tables per format
    fig2_store_sales_write_time.png   # store_sales write time per format
requirements.txt                      # Python package versions used
```

## Main results

| | SF1 | SF2 | SF5 |
|---|---|---|---|
| Total size, Parquet (MB) | 149.68 | 297.97 | 737.75 |
| Total size, ORC (MB) | 171.71 | 340.09 | 860.76 |
| `store_sales` write, Parquet (s) | 27.68 | 53.33 | 133.53 |
| `store_sales` write, ORC (s) | 23.62 | 44.87 | 112.25 |

- **File size:** Parquet produces smaller files overall, which agrees with the paper. ORC is smaller for four of the five tables, but Parquet wins on the largest table (`store_sales`).
- **Write time:** ORC was about 15% faster to write `store_sales`, which is the opposite of the paper's Arrow-based result.
- **Read time:** all reads took under 1 second. `count()` reads the row counts from file metadata rather than the data itself, so this test is not directly comparable with the paper.

Full results and discussion are in our report.

## Acknowledgements

- Original study: Liu et al. (2025), *The VLDB Journal*.
- TPC-DS benchmark: Transaction Processing Performance Council (TPC).
- Data: [alamb/tpcds-data](https://github.com/alamb/tpcds-data).
- Software: Apache Spark / PySpark, Apache Parquet, Apache ORC, pandas, PyArrow, Google Colab.

## Team

| Member | Responsibility |
|---|---|
| Wardha | Reproduction methodology, dataset preparation, experimental setup, code implementation and experiment execution |
| *(add team members)* | |
