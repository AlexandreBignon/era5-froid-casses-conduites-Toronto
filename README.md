# Le froid augmente-t-il les casses de canalisations d'eau potable ? ERA5 et casses réelles de Toronto

Notebook Python qui relie la température minimale journalière (réanalyse **ERA5**, Copernicus) aux **casses réelles** de conduites d'eau de la Ville de Toronto (1990-2016, environ 35 000 casses), avec deux méthodes : la méthode par classes de température que j'ai utilisée pendant un stage, puis une régression de Poisson.

## Ce que fait le notebook

1. Télécharge la température à 2 m ERA5 (valeurs horaires 1990-2016) pour le point de Toronto, en une seule requête.
2. Obtient la température minimale de chaque jour et la relie au nombre de casses du jour.
3. **Méthode 1** (inspirée de Bruaset et Sægrov, 2018) : taux de casse par classe de 1 °C de température minimale, puis régression linéaire (OLS).
4. **Méthode 2** : régression de Poisson jour par jour, avec l'année comme variable de contrôle.
5. Teste un décalage de 0 à 7 jours entre le froid et la casse, sépare l'effet du froid de celui de la saison, et vérifie si les jours sont indépendants.

## Principaux résultats

* Méthode 1 : taux de casse = 4,76 − 0,217 × température minimale (R² = 0,84, p ≈ 10⁻¹⁶).
* Méthode 2 : **+5,8 % de casses par °C de moins** (environ +76 % pour 10 °C de moins).
* À mois égal, l'effet tombe à +3,0 % par °C de moins : environ la moitié du lien vient de la saison, le froid garde un effet propre.
* Le Poisson donne le meilleur ajustement avec la température d'il y a 3 jours.
* Les jours ne sont pas indépendants : les estimations restent valables, mais leurs intervalles de confiance sont corrigés (méthode HAC).

## Données

* **Température** : ERA5 (Copernicus Climate Change Service), jeu *ERA5 hourly time-series data on single levels*. *Generated using or contains modified Copernicus Climate Change Service information. Neither the European Commission nor ECMWF is responsible for any use that may be made of the Copernicus information or data it contains.*
* **Casses** : Ville de Toronto, portail de données ouvertes (open.toronto.ca), jeu « watermain breaks 1990 to 2016 ». Licence : à vérifier sur la page du jeu de données.

Le fichier de casses n'est pas inclus dans ce dépôt : téléchargez-le sur le portail (version Excel) et placez-le dans `data/watermain-breaks-1990-to-2016-excel.xlsx`.

## Installation

```bash
pip install -r requirements.txt
```

## Accès aux données ERA5

1. Créer un compte gratuit sur [https://cds.climate.copernicus.eu](https://cds.climate.copernicus.eu) et accepter les conditions d'utilisation si elles sont demandées.
2. Copier votre jeton API depuis votre page de profil.
3. Créer le fichier `~/.cdsapirc` (Windows : `C:\\Users\\<nom>\\.cdsapirc`) :

```
   url: https://cds.climate.copernicus.eu/api
   key: <VOTRE-JETON>
   ```

   Ne mettez jamais ce jeton dans le notebook ni sur GitHub.

## Lancer

```bash
python -m jupyterlab
```

Ouvrir le notebook et exécuter les cellules dans l'ordre. Le téléchargement ERA5 est une requête unique (de quelques minutes à quelques dizaines de minutes selon la file d'attente) ; il est ignoré si le fichier est déjà dans `data/`.

## Limites

* La date des casses est, selon la documentation du jeu de données, la date à laquelle la casse a été **signalée** : elle peut suivre la casse réelle de quelques jours.
* Corrélations, pas causes : le fichier de Toronto ne contient ni matériau ni diamètre, et l'âge des conduites, le sol et la pression ne sont pas pris en compte.
* ERA5 est une réanalyse lissée (maille d'environ 30 km), et son « jour » est un jour en heure UTC.
* Résultats propres à Toronto : non transposables tels quels à un autre réseau.

## Références

* Bruaset, S., \& Sægrov, S. (2018). An analysis of the potential impact of climate change on the structural reliability of drinking water pipes in cold climate regions. *Water*, 10(4), 411. https://doi.org/10.3390/w10040411
* Copernicus Climate Change Service (2025) : *ERA5 hourly time-series data on single levels from 1940 to present*. Copernicus Climate Change Service (C3S) Climate Data Store (CDS). DOI : [10.24381/1cf1ad76](https://doi.org/10.24381/1cf1ad76) (consulté le 21-11-2026).
* Ville de Toronto, portail de données ouvertes, *Watermain Breaks* (consulté le 21-11-2026).

## Auteur

Alexandre Bignon, ingénieur IMT Atlantique.

