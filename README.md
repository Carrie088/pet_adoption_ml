# PetFinder.my — przewidywanie czasu adopcji

Na podstawie danych z ogłoszeń zwierząt budujemy modele, które przewidują jak szybko zwierzę zostanie adoptowane(`AdoptionSpeed`).

## Cel

Klasyfikacja wieloklasowa — 4 klasy czasu adopcji (po połączeniu oryginalnych klas 0 i 1):

| Klasa | Znaczenie |
|-------|-----------|
| 0 | adopcja w ciągu 0–7 dni |
| 1 | 8–30 dni |
| 2 | 31–90 dni |
| 3 | brak adopcji po 100+ dniach |

## Struktura projektu

```
pet_adoption_ml/
├── data/
│   ├── raw/              # surowe CSV (train, etykiety ras/kolorów/stanów)
│   └── processed/        # dane po czyszczeniu i flagach anomalii
├── models/               # zapisane modele (.pkl) i lista cech
├── notebooks/            # pipeline krok po kroku
├── requiremets.txt       # zależności (pip freeze z venv)
└── venv/                 # środowisko Python (lokalne)
```

## Notebooki (kolejność)

| Nr | Plik | Opis |
|----|------|------|
| 01 | `01_data_preparation.ipynb` | czyszczenie danych, nowe cechy, zapis `train_clean.csv` |
| 02 | `02_anomaly_detection.ipynb` | Isolation Forest, flaga `is_anomaly` |
| 03 | `03_random_forest.ipynb` | Random Forest |
| 04 | `04_svm.ipynb` | SVM |
| 05 | `05_knn.ipynb` | KNN |
| 06 | `06_neural_network.ipynb` | sieć MLP |
| 07 | `07_expert_system.ipynb` | głosowanie modeli (hard / weighted voting) |
| 08 | `08_fuzzy_logic.ipynb` | system rozmyty (scikit-fuzzy) |

Notebooki **03–08** uczą się na `data/processed/train_clean_anomaly.csv` (kolumna `is_anomaly` jest usuwana przed treningiem).

## Uruchomienie

1. Utwórz i aktywuj środowisko wirtualne:

```bash
python -m venv venv
source venv/bin/activate   # Linux / WSL
# venv\Scripts\activate    # Windows
```

2. Zainstaluj biblioteki z pliku zależności (po aktywacji `venv`):

```bash
pip install -r requirements.txt
```

3. Uruchamiaj notebooki **po kolei** od `01` do `08`. Wybierz kernel **`venv`**.

## Główne biblioteki

- **pandas**, **numpy** — dane  
- **scikit-learn** — modele, metryki, skalowanie  
- **matplotlib**, **seaborn** — wykresy  
- **joblib** — zapis i wczytanie modeli  
- **scikit-fuzzy** — logika rozmyta (notebook 08)

## Wyniki (zbiór testowy, orientacyjnie)

| Metoda | Accuracy |
|--------|----------|
| Random Forest | ~0,42 |
| MLP | ~0,40 |
| SVM | ~0,39 |
| KNN | ~0,37 |
| Głosowanie (hard / weighted) | ~0,41–0,42 |
| System rozmyty (ulepszona wersja w `08_fuzzy_logic copy.ipynb`) | ~0,42 |

## Uwagi

- Z danych usunięto m.in. identyfikatory `PetID` i `RescuerID`, żeby modele nie „zapamiętywały” konkretnych ogłoszeń.
- SVM, KNN i MLP wymagają **skalowania** cech (`StandardScaler`); Random Forest — nie.

## Źródło danych

[Kaggle — PetFinder.my Adoption Prediction](https://www.kaggle.com/c/petfinder-adoption-prediction)
