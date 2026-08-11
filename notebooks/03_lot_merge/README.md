# Step 3: Lot generation & merge — this is NOT a separate notebook

**Correction from an earlier version of this README:** this used to say the
lot-generation/merge step was a missing notebook. That was wrong — it isn't
missing. `notebooks/02_location_effectiveness` already builds `lots_df` (one
row per lot slot, exploded from the `fixtures` dict) and scores it with
`L_score()`, all in the same notebook as the area effectiveness scoring.
Steps 2 and 3 are one notebook, not two.

What *was* a real bug (now fixed): the cell that merges area effectiveness
scores into `lots_df` used a 3-row hardcoded placeholder (`A_i` in `[2,4,6]`
only) instead of the real 53-row `A_scores.xlsx` data, which would have left
`A_score` as `NaN` for nearly every lot. Fixed to merge against `df_effect`
directly, with a hard check that raises if any lot ends up unmatched.
Re-executed the fix standalone (skipping the plotting cells, which have an
unrelated pre-existing cell-ordering issue) — confirms: 0 missing A_scores
across all 563 lots, 405 clothing + 158 accessory, matching the existing
`lots_with_Aij.xlsx` in `data/03_processed/` exactly.

**What's still missing:** the notebook builds `lots_df` and scores it, but
doesn't yet *export* it to `data/03_processed/lots_with_Aij.xlsx` in the
two-sheet format (`lots_with_Aij` + `L_types_reference`, with `lot_ID` and
`L_type_ID` assigned) that Step 4 reads. That's a small addition, not a
missing notebook — happy to add that export cell whenever you want it; just
didn't want to assign new `lot_ID`s that might not match the `lot_ID`s
already baked into the current `kontrol.xlsx`/`assignment.json` without
confirming that's okay first.
