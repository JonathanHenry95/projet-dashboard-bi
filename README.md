# 📊 Dashboard BI — Superstore Sales 2014–2017

**Source :** [Kaggle — Sample Superstore Sales Dataset](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final)  
**Dataset :** 9 994 commandes · 21 colonnes · 4 ans (2014–2017) · USA

---

## Ce que ce projet démontre

- Pipeline ETL complet sur Power Query : chargement, nettoyage, feature engineering (colonnes calculées, types, dates)
- Modélisation des données : table Calendar, relations, table de mesures `_Mesures`
- Dashboard interactif Power BI — 5 pages · navigation · slicers · mise en forme conditionnelle
- Mesures DAX avancées : YoY, YTD, SAMEPERIODLASTYEAR, DIVIDE, FILTER, CALCULATE

---

## Structure

```
projet-dashboard-bi/
├── Sample - Superstore.csv
├── dashboard_screenshot_overview.png
├── dashboard_screenshot_produits.png
├── dashboard_screenshot_segments.png
├── dashboard_screenshot_geographie.png
├── dashboard_screenshot_remises.png
└── README.md
```

---

## Résultats clés

| KPI | Valeur | Insight |
|-----|--------|---------|
| CA Total | $2.30M | Croissance constante +51% sur 4 ans |
| Profit Total | $286k | Marge globale de 12.5% |
| Nb commandes | 5 009 | Panier moyen de $458 par commande |
| Taux commandes à perte | **19%** | 1 commande sur 5 génère une perte |
| Sous-catégorie à perte | **Tables** | -$17.7k de profit malgré $207k de CA |
| Effet remise | **Critique > 20%** | Marge passe de +29.5% (0%) à -119% (>50%) |
| Région la + rentable | **West** | ~15% de marge vs ~8% pour Central |
| Croissance 2014→2017 | **+51%** | Accélération nette en 2016 (+29.5% YoY) |

---

## Pages du Dashboard

| Page | Contenu |
|------|---------|
| 1 — Performance Overview | KPI Cards · CA mensuel + MA3 · Croissance YoY · CA par catégorie |
| 2 — Analyse Produits | Profit par sous-catégorie · Marge par catégorie · Tableau synthèse |
| 3 — Analyse Segments | Répartition CA · Marge par segment · CA par client · Top clients |
| 4 — Géographie | Carte à bulles · CA & Marge par région · Top 10 états |
| 5 — Analyse Remises | Impact remises sur marge · Taux commandes à perte · Évolution marge |

---

## Transformations Power Query (M)

```m
// Parsing des dates (format américain MM/DD/YYYY)
= Table.TransformColumnTypes(Source,{{"Order Date", type date}}, "en-US")

// Colonnes calculées ajoutées
Year          = Date.Year([Order Date])
Month Num     = Date.Month([Order Date])
Month Name    = Date.ToText([Order Date], "MMM", "en-US")
Quarter       = "Q" & Text.From(Date.QuarterOfYear([Order Date]))
Ship Days     = Duration.Days([Ship Date] - [Order Date])
Profit Margin = if [Sales] = 0 then 0 else [Profit] / [Sales]
Discount Band = tranches 0% / 1-10% / 11-20% / 21-30% / 31-50% / >50%
```

---

## Mesures DAX principales

```dax
CA Total = SUM(Orders[Sales])

Profit Total = SUM(Orders[Profit])

Marge % = DIVIDE([Profit Total], [CA Total], 0)

Nb Commandes = DISTINCTCOUNT(Orders[Order ID])

Nb Clients = DISTINCTCOUNT(Orders[Customer ID])

CA Année Précédente =
CALCULATE([CA Total], SAMEPERIODLASTYEAR(Calendar[Date]))

Croissance YoY % =
DIVIDE(
    [CA Total] - [CA Année Précédente],
    [CA Année Précédente],
    BLANK()
)

MA3 =
AVERAGEX(
    DATESINPERIOD(Calendar[Date], LASTDATE(Calendar[Date]), -3, MONTH),
    [CA Total]
)

Taux Commandes Perte % =
DIVIDE(
    COUNTROWS(FILTER(Orders, Orders[Profit] < 0)),
    COUNTROWS(Orders),
    0
)

Croissance YoY Annuelle =
VAR annee = SELECTEDVALUE(Calendar[Year])
VAR ca_courant = CALCULATE([CA Total], Calendar[Year] = annee)
VAR ca_precedent = CALCULATE([CA Total], Calendar[Year] = annee - 1)
RETURN DIVIDE(ca_courant - ca_precedent, ca_precedent, BLANK())
```

---

## Stack

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Mesures%20avanc%C3%A9es-F2C811?logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/Power%20Query-ETL-01B8AA?logo=powerbi&logoColor=white)
