# Transplantation rénale et diabète en France : analyse des dépenses (open data SNDS)

Comment ont évolué les dépenses liées à la transplantation rénale, et la prévalence du diabète chez les patients transplantés, entre 2015 et 2023 ?

Projet réalisé dans le cadre de l'UE Approche transdisciplinaire, Master 1 Intelligence en données de santé, Université de Strasbourg.

## Contexte

L'insuffisance rénale chronique terminale est le stade final de la maladie rénale. Reconnue en ALD 19, elle est prise en charge à 100 % par l'Assurance Maladie, et la transplantation en constitue le traitement de référence. Le diabète y est une comorbidité fréquente, qui abîme les vaisseaux du greffon. Dans un contexte de hausse continue des dépenses de santé, le suivi de ce poste est un enjeu de pilotage.

## Données

Deux exports publics de Data Ameli, dérivés du SNDS et agrégés au niveau national.

| Fichier | Contenu |
|---|---|
| `Data/depenses.csv` | dépenses remboursées et effectifs par pathologie et par année |
| `Data/comorbidites.csv` | prévalence des comorbidités par pathologie et par année |

## Traitement des données

L'essentiel du travail se situe avant l'analyse.

**Lecture de fichiers mal formés.** Les exports sont séparés par points-virgules, comportent des lignes de longueur irrégulière et un encodage dégradé. La lecture se fait donc sans inférence de schéma, en tolérant les lignes irrégulières et en réparant l'encodage, puis le typage est appliqué explicitement : année en entier, montants et proportions en flottant, en tolérant les valeurs non convertibles plutôt qu'en interrompant la lecture.

**Filtrage dans la hiérarchie pathologique.** La nomenclature du SNDS est arborescente. L'extraction du périmètre utile suppose de descendre trois niveaux, de l'insuffisance rénale chronique terminale vers la transplantation rénale, puis de ne retenir que les lignes de dépenses, et côté comorbidités, le seul libellé du diabète.

**Agrégation et jointure.** Sommation des montants par année, conversion en millions d'euros, puis jointure des deux sources sur l'année pour constituer un tableau de synthèse portant effectifs, dépenses et prévalence.

**Traitement de la rupture de 2020.** L'année 2020 est une anomalie conjoncturelle et non structurelle. Elle a été exclue de l'ajustement linéaire afin de ne pas biaiser l'estimation de la tendance, mais conservée dans les figures, car elle fait partie du récit.

## Résultats

Le nombre de transplantés n'a progressé que de **3,1 %** sur la période, alors que les dépenses totales ont augmenté de **15,5 %**. La hausse n'est donc pas portée par le volume de patients, qui reste stable entre 3 180 et 3 570 par an hors période COVID, mais par le **coût par patient, en hausse de 12,1 %**.

La prévalence du diabète progresse de **15,0 %**, à un rythme comparable à celui des dépenses. Les deux évolutions sont parallèles, mais les données ne permettent pas d'établir de lien de causalité.

L'année **2020** constitue une rupture isolée : les dépenses tombent à 163,6 M€ et les effectifs à 2 510 patients, soit une baisse de près d'un tiers. La pandémie a entraîné la suspension ou le report de nombreuses greffes programmées, par réduction des dons d'organes et saturation des services de réanimation.

## Limites

- Données administratives : aucune information clinique détaillée, ni type de diabète, ni HbA1c, ni stade de néphropathie, ni survie du greffon.
- Aucune causalité possible : le travail observe des associations et des tendances temporelles, non des relations de cause à effet.
- Le montant remboursé n'est pas le coût réel total, le reste à charge et la part complémentaire en étant exclus.
- Les données sont agrégées au niveau national, ce qui interdit toute analyse en sous-groupe par âge, sexe, ancienneté de la greffe ou type de diabète.

## Structure

```
Data/         exports SNDS bruts
notebook/     Groupe1_exec.ipynb, traitement et analyse
output/       figures generees
```

## Mise en route

```bash
uv sync
jupyter lab notebook/Groupe1_exec.ipynb
```

Polars pour le traitement, Matplotlib pour les figures.
