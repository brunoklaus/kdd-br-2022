# KDD-BR 2022
PaRaNA solution for KDD-BR 2022 (Visual odometry for autonomous UAVs in GNSS contested environments)

The steps to reproduce our solution are as follows:

1. Manually download and extract the [competition .zip from Kaggle](https://www.kaggle.com/competitions/kddbr-2022/data) into the `kddbr-2022` folder of this repository. You must also manually extract the prediction zips found in `predictions/`.

2. Generate Features using PDCNet model. These are already precomputed and found at `features/PDCNet`. If you actually want to generate them yourself from scratch, use the notebook  `0) Generate features from scratch using DenseMatching library.ipynb`. Note that this requires you to download the [DenseMatching repository](https://github.com/PruneTruong/DenseMatching), and follow its instructions to set up the correct environment before running the `.ipynb` file.

3. Create the 'flight' CSVs. These CSVs are our attempt to restore temporal information that was lost in the original input data. They are already precomputed, so you can just use the ones at found at `flight/`. Alternatively, they may be generated from scratch using notebook `1) Infer Flight order, create Flight csvs.ipynb`. The trick is to:
    * Start at some random row of `public.csv` DataFrame.
    * Calculate `new_altitude = altitude + delta`
    * Check for exact matches for the new_altitude. If 0 or more than 1 matches are found, then we filter the 100 closest altitudes and use mean squared error between images to determine the closest match.
    * Repeat process until all rows are visited.


4. Use LightGBM to map the Extracted Features to the output values (separate Boosters for North, East variables). If you want to train it from scratch, use `2) Gradient Boosting using DenseMatching Features.ipynb` . It should be enough to install the dependencies with `pip install lightgbm==3.3.2 pandas matplotlib tqdm`. Do note that, because of random parameters such as feature fraction, you might end up with a slightly different model. To take that into account, we have also provided the files for the actual Booster model used in the final prediction. Notebook `3) Verification - Gradient Boosting using DenseMatching Features-Oct23.ipynb` verifies that the predictions of this saved model on the test set does indeed match exactly the one that was sent to Kaggle.
