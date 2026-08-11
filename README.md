# Fashion Retail Product Placement Optimization

Fashion-store product-to-fixture assignment pipeline: predicts item
saleability, scores fixture locations, optimizes item→lot assignment with
CP-SAT, and visualizes the resulting planogram.

## Pipeline

```
1. Preprocessing + LR                2. Location effectiveness scoring
   notebooks/01_preprocessing_lr/       + 3. Lot generation & merge
   in:  data/01_raw/*.xlsx              notebooks/02_location_effectiveness/
   out: data/02_interim/                (one notebook — see note below)
        saleability_scores_full_M9      in:  (self-contained: hardcoded
        .xlsx                                store grid + fixture layout)
                                         out: data/02_interim/A_scores.xlsx
                                              data/02_interim/fixture_grid.json
                                              data/03_processed/lots_with_Aij.xlsx*
              \                                    /
               \                                  /
              4. CP-SAT assignment
                 notebooks/04_cp_sat_assignment/
                 in:  data/03_processed/lots_with_Aij.xlsx
                      data/02_interim/saleability_scores_full_M9.xlsx
                 out: data/03_processed/kontrol.xlsx
                      data/03_processed/assignment.json
                      viz/store-planogram/public/data/assignment.json
                          |
                          v
              4b. Store layout visualization
                  viz/store-planogram/  (React + Vite)
                  reads assignment.json at runtime — rerun step 4 to update
```

## Running it

### Notebooks (steps 1, 2, 4)

```bash
pip install -r requirements.txt
jupyter notebook notebooks/
```

Run in order: `01_preprocessing_lr` → `02_location_effectiveness` (builds and
scores `lots_df` internally, no separate Step 3) → `04_cp_sat_assignment`.
Each notebook's export cells write straight into `data/`, and
`04_cp_sat_assignment`'s final cell writes `assignment.json` directly into
`viz/store-planogram/public/data/`, so the next step's input is ready as
soon as the previous one finishes.

### Visualization

```bash
cd viz/store-planogram
npm install
npm run dev
```

AŞKOLAR
Opens the interactive planogram at `http://localhost:5173`, reading
`public/data/assignment.json`. To refresh the visualization after a new
solve, just rerun the last cell of `04_cp_sat_assignment` and reload the page
— no rebuild needed in dev mode (`npm run build` regenerates the static
`dist/` bundle for deployment).

## Repo layout

```
data/
  01_raw/          # source inputs (tomodel.xlsx, product_arrival.xlsx)
  02_interim/       # step 1 & 2 outputs
  03_processed/     # step 2 (lot export) & 4 outputs (final assignment)
notebooks/
  01_preprocessing_lr/
  02_location_effectiveness/  # also builds/scores lots_df (no separate step 3)
  03_lot_merge/     # see README inside — historical note, not a real step
  04_cp_sat_assignment/
viz/
  store-planogram/  # React + Vite app, reads assignment.json at runtime
```

## Data note

`data/*.xlsx` and `viz/store-planogram/public/data/assignment.json` have product/pricing data that built all coefs etc.

Aşkolar step 1's inputs and output are now fully included: `tomodel.xlsx`,
`product_arrival.xlsx`, and `saleability_scores_full_M9.xlsx` are all in
place, so `01_preprocessing_lr` runs end-to-end as-is. Step 2 is
self-contained (no external inputs) but its outputs (`A_scores.xlsx`,
`fixture_grid.json`) aren't included yani run the notebook to generate
them. See `data/README.md` for column details on each file.
