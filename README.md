# The Overloaded Phishing Inbox

The Overloaded Phishing Inbox is a phishing email triage application developed by Code Mavericks for Microsoft Innovate 2026.

The project is designed for situations where a user or security reviewer has to deal with a large number of emails and needs a quick way to identify messages that deserve closer attention. Instead of giving only a yes or no result, the application produces a risk score and shows the signals that contributed to the result.

## What the project does

The application accepts an email and analyzes three main areas:

- Email headers
- URLs and domains
- Email text

These results are combined into a risk score from 0 to 100.

The current classification is:

| Score | Result |
| --- | --- |
| 0 to 29 | SAFE |
| 30 to 69 | SUSPICIOUS |
| 70 to 100 | CRITICAL |

The result also includes evidence such as authentication failures, sender mismatches, suspicious URL characteristics, and language commonly associated with phishing attempts.

## Main features

### Header analysis

The header analyzer checks:

- SPF
- DKIM
- DMARC
- From and Reply-To domain mismatches
- Sender and Return-Path mismatches
- Missing or malformed sender information

When complete authentication information is available, the application also uses DNS information to provide additional context.

### URL and domain analysis

The URL analyzer checks for signals such as:

- IP addresses used instead of domains
- Unusually long URLs
- @ symbols in URL authorities
- Suspicious top-level domains
- Punycode
- Look-alike domains
- Non-HTTPS URLs
- Deep subdomain structures

### Text analysis

The text analyzer uses a TF-IDF and Logistic Regression pipeline trained on the supplied email dataset.

It also checks the email for groups of terms associated with:

- Urgency
- Credential requests
- Payment requests
- Impersonation

The trained model is stored in models/nlp_pipeline.joblib.

### Risk scoring

The base score combines the three analyzers using these weights:

    Header / Sender: 30%
    URL / Domain:    35%
    NLP / Text:      35%

Additional guardrails are used so that combinations of strong phishing indicators are not incorrectly treated as safe.

## Triage dashboard

The web application provides a simple dashboard where an analyst can enter an email for analysis and review:

- Overall risk score
- Risk classification
- Header findings
- URL findings
- Text findings
- Supporting evidence

The application is intended as a triage aid. It does not replace a full email security gateway or manual investigation.

## Project structure

    The-Overloaded-Phishing-Inbox/
    ├── app.py
    ├── train_model.py
    ├── requirements.txt
    ├── .env.example
    ├── .gitignore
    ├── README.md
    ├── data/
    │   └── CEAS_08.csv
    ├── models/
    │   └── nlp_pipeline.joblib
    ├── static/
    │   └── style.css
    └── templates/
        └── index.html

## Requirements

- Python 3.10 or newer is recommended
- pip
- A virtual environment is recommended for local development

## Installation

Clone the repository:

    git clone https://github.com/amankumar254/The-Overloaded-Phishing-Inbox.git
    cd The-Overloaded-Phishing-Inbox

Create and activate a virtual environment.

On Windows:

    python -m venv .venv
    .venv\Scripts\activate

On Linux or macOS:

    python3 -m venv .venv
    source .venv/bin/activate

Install the dependencies:

    pip install -r requirements.txt

## Configuration

Copy .env.example to .env and fill in any values required by your local setup.

Do not commit API keys, passwords, tokens, or other private configuration values.

## Running the application

Start the application with:

    python app.py

Then open the local address shown by Flask in your browser.

## Training the text model

The repository includes the training script used for the text classification pipeline.

To retrain the model:

    python train_model.py

The script loads the labelled CSV data, performs preprocessing, creates TF-IDF features using unigrams and bigrams, trains a Logistic Regression classifier, evaluates the model, and saves the trained pipeline for inference.

## Model approach

The text classifier follows this flow:

    Email subject + body
            |
            v
    Text cleaning
            |
            v
    TF-IDF features
    (unigrams + bigrams)
            |
            v
    Logistic Regression
            |
            v
    Phishing probability
            |
            v
    Text risk score

The wider application then combines the text result with the header and URL analysis to produce the final triage score.

## Data

The project uses the CEAS 2008 email dataset supplied with the project for text model training.

The dataset is included in the local project under data/CEAS_08.csv.

For a different dataset, update the training script and make sure the expected text and label fields are available.

## API

The Flask application exposes the analysis functionality through its application routes. The exact request and response structure can be checked directly in app.py.

The main analysis flow takes an email object, runs the available analyzers, and returns a combined risk result with supporting findings.

## Security notes

This project is intended for educational, research, and prototype use.

Do not use real sensitive mailboxes or confidential email content for testing without appropriate authorization.

Before deploying the application in a real environment, add authentication, access control, secure storage, logging controls, rate limiting, and production-grade secret management.

## Limitations

The quality of the final score depends on the quality of the email data and the signals available to the analyzers.

A phishing email can be designed to look normal, and a legitimate email can sometimes contain characteristics that look suspicious. The score should therefore be treated as a prioritization signal rather than a final verdict.

## Future work

Planned improvements include:

- Training and validation on a larger labelled phishing dataset
- More detailed evaluation and threshold tuning
- Integration with additional header and URL reputation services
- Better handling of spear-phishing and impersonation attacks
- Authentication and role-based access for multi-user deployments
- Production deployment and monitoring

## Team

Code Mavericks

The project was prepared for Microsoft Innovate 2026 at Bennett University.

### Team members

- Aman Kumar
- Jai Gupta
- Krish Astwal
- Arpit Tyagi

## License

Add the license that matches the intended distribution of the project before publishing the repository for wider reuse.


## Repository note

The original local project also contains two large generated/training artifacts:

- data/CEAS_08.csv
- models/nlp_pipeline.joblib

They were kept in the supplied project package but are not stored in this GitHub repository because the connected GitHub upload interface available here cannot transfer those large binary and dataset files reliably.

To reproduce the complete local project, keep those two files in the paths above. The rest of the application source and documentation is available in this repository.
