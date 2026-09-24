# Data Engineer Internship — Week 1, Day 1

## Tasks Completed

- Set up the project structure (`venv`, `data/` folder, notebook, `day1.log`)
- Configured Python `logging` to track daily work in `day1.log`
- Read tabular data into pandas from `data/Test.csv`
- Learned the difference between qualitative (nominal, ordinal) and quantitative (discrete, continuous) data attributes
- Cleaned messy categorical columns (`City`, `Product`, `Gender`) — inconsistent casing and stray whitespace (e.g. `"Ktm"` vs `"Kathmandu"`, `"phone"` vs `"Phone "`)
- Built 1D, 2D, and multi-dimensional visualizations with `matplotlib` and `seaborn`
- Explored the difference between structured data (this CSV data) and unstructured/semi-structured data

## How to Run

1. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install pandas matplotlib seaborn
   ```
3. Open `data_visualization_task.ipynb` in VS Code.
4. Select the `venv` interpreter as the notebook kernel.
5. Run the cells in order:
   - Logging setup
   - Load data (`data/Test.csv`)
   - Clean categorical columns
   - Visualization cells (1D → 2D → multi-dimensional)
6. Check `day1.log` for a timestamped record of the work done.

## What I Learned

Learned the difference between qualitative and quantitative data, and how each splits into subtypes (nominal/ordinal, discrete/continuous). Understood how dimensionality (1D, 2D, multi-dimensional) determines which chart type fits the data best. Saw firsthand why raw data needs cleaning before it can be visualized meaningfully. Practiced reading CSV data with pandas and using Python's `logging` module to track daily progress instead of manual print statements.

## Challenges

Real data was inconsistent — extra whitespace, mixed capitalization, and city aliases like `"Ktm"` — which had to be cleaned before any chart made sense. Missing packages (`matplotlib`, `seaborn`) weren't installed by default and needed to be added to the venv before visualization would run.
