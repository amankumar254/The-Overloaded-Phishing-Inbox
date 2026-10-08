# Data

The repository includes the CEAS 2008 email dataset used for training the text classification model.

The dataset is stored as a compressed archive:

    data/CEAS_08.zip

Extract the archive in this directory to obtain:

    data/CEAS_08.csv

The CSV file is required when running train_model.py to retrain the NLP model. The application itself does not need the CSV when using the supplied trained model in models/nlp_pipeline.joblib.
