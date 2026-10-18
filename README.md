# Keystroke Authenticator

A Flask web application that authenticates users using their typing rhythm. Instead of relying only on a password, the application compares keystroke timing patterns—such as key-pair latency and key hold duration—against a user-specific machine-learning profile.

## Features

- Collects typing timing data in the browser.
- Builds a feature vector from key-pair timing and key dwell-time measurements.
- Uses a PyTorch neural network to score whether a typing sample matches a user profile.
- Provides a simple Flask web interface for login, typing verification, and results.

## Project Structure

```text
.
├── app.py                    # Flask application and HTTP routes
├── requirements.txt          # Python dependencies
├── processing/
│   ├── keystroke.py          # Optional desktop keystroke data collector
│   ├── model.py              # PyTorch user-profile model
│   ├── train.py              # Model-training entry point
│   ├── utils.py              # Feature extraction, datasets, and training helpers
│   └── data/                 # Training data and saved user model files
├── templates/
│   ├── index.html            # Login and typing page
│   ├── profile.html          # Profile page
│   └── results.html          # Authentication result page
├── static/
│   ├── js/index.js           # Browser keystroke collection
│   └── styles.css             # Application styles
├── *.csv                     # Example keystroke and key-pair data
└── test.pt                   # Saved PyTorch model artifact
```

## Requirements

- Python 3.10 or newer is recommended.
- A Python environment capable of installing the packages in `requirements.txt`.

## Installation

```bash
git clone https://github.com/Gabriel-Xu/keystroke-authenticator.git
cd keystroke-authenticator

python -m venv .venv
source .venv/bin/activate       # Windows: .venv\\Scripts\\activate
pip install -r requirements.txt
```

## Running the Application

Start the Flask server with:

```bash
python app.py
```

Then open `http://localhost:1432` in a browser.

Enter a username with a corresponding saved model in `processing/data/<username>.pt`. After login, type the displayed phrase and press **Enter** to submit the sample. The results page reports whether the sample passed the model's confidence threshold.

## How It Works

1. The browser records valid lowercase letters and spaces while the user types.
2. For each sample, the client measures:
   - **Key-pair elapsed time:** the interval between consecutive key presses.
   - **Dwell time:** how long each key is held.
3. `processing.utils.createFeatureVec` aggregates these measurements into a 756-value feature vector: 729 possible letter/space pairs plus 27 per-key dwell-time features.
4. A user-specific PyTorch feed-forward network produces a probability score.
5. The application accepts the sample when the score is greater than `0.5`.

## Training a User Model

Training data is expected in `processing/data/main.csv`. To train the example user profiles configured in `processing/train.py`, run:

```bash
cd processing
python train.py
```

The resulting model files are written to `processing/data/<username>.pt` and can then be loaded by the Flask application.

## Data and Privacy

Keystroke timing data is biometric-like behavioral information. Use this project only with informed user consent, protect collected CSV and model files, and avoid treating the current implementation as production-grade authentication. The application currently stores session state in process memory and is intended for local or development use.

## License

No license has been specified for this repository.
