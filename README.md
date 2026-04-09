# 📊 Dashboard BI — Suivi KPIs Marketing & Performance

## Contexte

Reproduction d'un cas terrain inspiré de mon alternance chez Valeo : construction d'un pipeline de données automatisé et d'un dashboard interactif permettant le suivi de KPIs marketing et opérationnels à destination de la direction.

---

## Ce que ce projet démontre

- Automatisation d'un pipeline de données (collecte → nettoyage → agrégation → visualisation)
- Conception de KPIs pertinents et lisibles pour des non-data
- Maîtrise de Plotly Dash pour la restitution interactive

---

## Structure

```
projet-dashboard-bi/
├── data/
│   ├── raw/             # Données brutes simulées
│   └── processed/       # Données nettoyées et agrégées
├── src/
│   ├── pipeline.py      # Extraction, nettoyage, transformation
│   ├── kpis.py          # Calcul des indicateurs
│   └── app.py           # Dashboard Plotly Dash
├── outputs/
│   └── dashboard_screenshot.png
├── requirements.txt
└── README.md
```

---

## KPIs suivis

- **Taux d'engagement** (hebdomadaire / mensuel)
- **Évolution du CA** par période et par segment
- **Taux d'anomalie** dans les flux de données
- **Délai moyen de reporting** avant/après automatisation

---

## Extrait — Pipeline de nettoyage

```python
import pandas as pd

def clean_data(filepath: str) -> pd.DataFrame:
    df = pd.read_csv(filepath, encoding='utf-8')
    
    # Suppression doublons et valeurs manquantes critiques
    df = df.drop_duplicates()
    df = df.dropna(subset=['date', 'segment', 'montant'])
    
    # Normalisation des formats
    df['date'] = pd.to_datetime(df['date'], dayfirst=True)
    df['montant'] = df['montant'].astype(float)
    df['segment'] = df['segment'].str.strip().str.upper()
    
    return df

def compute_kpis(df: pd.DataFrame) -> dict:
    return {
        'ca_total': df['montant'].sum(),
        'ca_moyen': df['montant'].mean(),
        'nb_transactions': len(df),
        'taux_anomalie': df['flag_anomalie'].mean() * 100
    }
```

---

## Stack

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-Dash-3F4F75?logo=plotly)
![Pandas](https://img.shields.io/badge/Pandas-150458?logo=pandas)
![PowerBI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black)
