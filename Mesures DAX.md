# Mesures DAX

## Table Dim_Date

```dax
-- Description de la mesure
Dim_Date =
ADDCOLUMNS(
    CALENDAR(DATE(2005,1,1), DATE(2021,12,31)),
    "Année", YEAR([Date]),
)
```

## KPIs

```dax
-- Total Production (kt)
Product_Total = SUM(product_annuel_petrole_champ[Total])
```

```dax
-- Total Importations (kt)
Import_Brut_Total = SUM(import_annuel_petrole_brute[Importations Totales])
```

```dax
-- Total Exportations (kt)
Export_Brut_Total = SUM(export_annuel_petrole_brute[Exportations Totales])
```

```dax
-- Balance Commerciale (kt)
Balance_Commerciale = [Export_Brut_Total] - [Import_Brut_Total]
```

```dax
-- Taux de Déclin Annuel
Taux_Declin_Annuel =
VAR actual_year = SELECTEDVALUE(Dim_Date[Année])
VAR actual_product = [Product_Total]
VAR previous_product =
    CALCULATE([Product_Total], Dim_Date[Année] = actual_year - 1)
RETURN
DIVIDE(actual_product - previous_product, previous_product,0)
```

```dax
-- Taux de Couverture
Taux_Couverture =
    DIVIDE([Import_Brut_Total],[Product_Total],0)
```
