# Final Project: Data-Driven Airport Staffing Optimization: Using CBP Hourly Arrival Records and Shift-Specific Service Rates

This project uses a mixed integer linear program to assign immigration counters during the Christmas peak season, based on hourly passenger arrivals and historic counter usage from `dataset_hourly.csv`. The core model and outputs live in `final_project.ipynb`, solved with Gurobi to produce shift-level staffing decisions and heatmaps.

## Dataset
- Path: `dataset_hourly.csv`
- Processed data: [Google Drive](https://drive.google.com/file/d/1amCHvZrjBibJNKprCrKVqfM9HW6w-_3V/view?usp=sharing), or github shown as `dataset_hourly.csv`.
- Raw data: [Google Drive](https://docs.google.com/spreadsheets/d/15VRgBf5MWYKi_qHQQV2U2F-irzUBWZ8g/edit?usp=sharing&ouid=107932714686873281112&rtpof=true&sd=true), or also in [CBP Official Website](https://awt.cbp.gov/).
- Columns:
  - `week_index`: Week number (1–5).
  - `day_index`: Day of week (1–7).
  - `Shift`: Shift label; values are `00-08`, `08-16`, and `16-00`.
  - `Hour`: Hour of day (0–23).
  - `Avg_PassengersHour_3yr`: Three-year average passenger volume for the hour.
  - `Avg_BoothsHour_3yr`: Three-year average number of active counters for the hour.

## Model highlights (`final_project.ipynb`)
- **Demand and service rate**: Build hourly demand `A` for each (week, day, shift, hour) and estimate per-counter service rate `mu` from `Avg_BoothsHour_3yr`.
- **Decision variables**:
  - `x`: Regular counters; `o`: Overtime counters; `u`: Served passengers; `b`: Remaining passengers at shift end.
- **Cost and objective**: Minimize total cost combining regular/overtime wages and backlog penalty. Solver settings allow 1% MIP gap and a 10-minute time limit.
- **Outputs**:
  - Console printout of staffing and backlog by hour.
  - Shift-level aggregation plotted as heatmaps and saved to `plots_shift_level/`.

## Environment
- Python 3.10+
- Data analysis and visualization: `pandas`, `numpy`, `matplotlib`, `seaborn`
- Optimization: `gurobipy` (requires local Gurobi installation and valid license)

Example installation:
```bash
pip install pandas numpy matplotlib seaborn gurobipy
```

## How to run
1. Ensure Gurobi is installed and licensed (e.g., via `grbgetkey`).
2. Install the Python dependencies above.
3. From the project root, start Jupyter:
   ```bash
   jupyter notebook
   ```
4. Open `final_project.ipynb` and run all cells in order.
5. Check the console for staffing results; heatmaps are saved to `plots_shift_level/`.

## Common adjustments
- **Budget**: Adjust parameters budget such as `Budget_w`, `c_r`, `c_o` to other values.
- **Weight**: Adjust `alpha` to reflect budget or backlog preferences.
- **Service capacity**: If new service-rate data is available, update new dataframe to modify `Avg_PassengersHour_3yr`, `Avg_BoothsHour_3yr` or the value of `mu`.
- **Shift structure**: To change shift boundaries, edit `shift_map` and ensure the `Shift` column in dataframe match the `shift_map`.
