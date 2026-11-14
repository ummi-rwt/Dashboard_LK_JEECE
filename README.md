# Tableau de bord LinkedIn mensuel – JEECE

Tableau de bord Power BI qui suit chaque mois la performance de la page LinkedIn de JEECE (audience, visibilité, engagement). Cette première version couvre **octobre 2026**.

> **Auteur :** Umang RAWAT, Responsable Marketing chez JEECE
> **Outil :** Power BI Desktop (Power Query, DAX)
> **Statut :** version 1, octobre 2026. Septembre et la comparaison mois par mois sont à venir.

![Aperçu du dashboard](screen_dc_oct.png)

---

## 1. Contexte et objectif

En tant que responsable marketing chez JEECE, je publie régulièrement sur la page LinkedIn de l'entreprise. LinkedIn affiche ses statistiques écran par écran, mais ne permet pas de les suivre dans le temps sur une seule page.

L'objectif de ce projet est de répondre rapidement à trois questions, mois après mois :

- **Audience :** combien d'abonnés avons-nous, et combien en gagnons-nous ?
- **Visibilité :** combien de fois nos contenus sont-ils vus, et cliqués ?
- **Contenu :** quel post a le mieux fonctionné, et pourquoi ?

## 2. Résultats d'octobre 2026

| Indicateur | Valeur |
|---|---|
| Impressions | 9 629 |
| Clics | 714 |
| Taux de clics (clics ÷ impressions) | 7,42 % |
| Abonnés au total (au 31/10/2026) | 2 373 |
| Abonnés gagnés (net) sur le mois | 69 |
| Commentaires | 19 |

**Ce que montrent les chiffres**

- Les impressions quotidiennes restent basses la plupart du mois, avec un **pic marqué en fin de mois, autour du 29 octobre** (environ 3 000 impressions sur la journée).
- Ce pic correspond à la publication annonçant les membres du COS de JEECE pour le mandat 2026-2027, qui est aussi le **post le plus performant du mois : 5 090 impressions et 83 réactions**.
- Les « abonnés gagnés (net) » sont la somme des gains moins les pertes de chaque jour : le mois compte des jours négatifs.

## 3. Contenu du dashboard

- **Segment « Mois »** pour choisir la période affichée.
- **Cartes d'audience :** abonnés au total, abonnés gagnés (net), commentaires.
- **Cartes de visibilité :** impressions, clics, taux de clics.
- **Courbe** des impressions par jour.
- **Tableau du post le plus performant** du mois (titre, réactions, impressions).

## 4. Données

Les données viennent des **exports de LinkedIn Page Analytics** (fichiers Excel, période du 1er au 31 octobre 2026) :

| Fichier / feuille | Contenu | Utilisation |
|---|---|---|
| Feuille `Indicateurs` | Une ligne par jour : impressions, clics, commentaires, taux d'interactions (organique, sponsorisé, total) | Cartes de visibilité et courbe |
| Feuille `Tous les posts` | Une ligne par publication : titre, date, réactions, impressions, clics | Post le plus performant |
| Export des abonnés | Une ligne par jour : abonnés gagnés (organiques, sponsorisés, invités automatiquement) et total du jour | Abonnés gagnés (net) |
| Table saisie à la main (`AbonnesTotal`) | Nombre total d'abonnés relevé sur LinkedIn à la fin du mois (2 373 au 31/10/2026) | Carte « Abonnés total » |

Le nombre total d'abonnés ne figure pas dans l'export quotidien : il est donc relevé à la main chaque mois.

## 5. Méthode

### Préparation des données (Power Query)

1. Suppression de la ligne d'explication que LinkedIn place en haut de chaque feuille, puis promotion de la ligne suivante en en-têtes.
2. Conversion des types avec les **paramètres régionaux « Anglais (États-Unis) »** : l'export utilise le format de date mois/jour/année et le point comme séparateur décimal. Avec une locale française, les taux étaient lus comme des nombres énormes et certaines dates étaient inversées.
3. Renommage des colonnes et sélection des colonnes utiles.

### Modèle de données

- Une **table Calendrier** créée en DAX (`CALENDAR` entre la première et la dernière date des données), avec une colonne `Mois`.
- Des **relations « plusieurs à un »** entre chaque table de données et le Calendrier, filtre dans un seul sens. Le segment « Mois » filtre ainsi tous les visuels en même temps.

### Mesures DAX

```dax
Impressions = SUM(Indicateurs[Impressions (total)])
Clics = SUM(Indicateurs[Clics (total)])
Taux de clics = DIVIDE([Clics], [Impressions])
Commentaires = SUM(Indicateurs[Commentaires (total)])
Abonnés gagnés (net) = SUM(Abonnes[Total d’abonnés])
Abonnes total = LASTNONBLANKVALUE(AbonnesTotal[Date], SUM(AbonnesTotal[Total]))
```

Le taux de clics est recalculé à partir des totaux (`DIVIDE`) et non obtenu en faisant la moyenne des taux quotidiens, afin qu'un jour à faible trafic ne pèse pas autant qu'un jour à fort trafic. `LASTNONBLANKVALUE` retient le dernier total d'abonnés connu du mois choisi, sans additionner deux mois.

### Vérifications

- Total des impressions d'octobre : **9 629**, identique à l'écran de statistiques de LinkedIn.
- Table de contrôle : 31 jours, du 01/10 au 31/10.
- Carte « Abonnés total » : 2 373, identique à la page LinkedIn.

## 6. Limites et points d'attention

- **Un seul mois pour l'instant :** pas encore de comparaison avec le mois précédent.
- **Deux façons de compter les impressions.** La courbe additionne les impressions de tous les contenus jour par jour (période du 1er au 31 octobre). Les impressions d'un post sont cumulées depuis sa publication jusqu'à la date de l'export, donc elles peuvent inclure des jours de novembre. Ces deux chiffres ne sont pas directement comparables.
- **Abonnés au total saisi à la main** chaque mois, car absent de l'export.
- **Export manuel :** les données sont à réexporter depuis LinkedIn à chaque mise à jour. Le format des exports peut changer.
- **Publication en ligne non réalisée :** le compte Power BI Service n'est pas disponible avec mon accès. Le dashboard est présenté en captures et en PDF.

## 7. Prochaines étapes

- [ ] Ajouter **septembre 2026** et construire **une page par mois**, avec un menu de navigation.
- [ ] Afficher la **variation par rapport au mois précédent** (impressions, clics, abonnés).
- [ ] Intégrer les **visiteurs de la page** (total mensuel, sans additionner les visiteurs uniques jour par jour).
- [ ] Étendre le tableau de bord à d'autres sources : Google Search Console, Google Analytics 4 et emailing de prospection.

## 8. Contenu du dépôt

```
├── README.md
├── images/
│   └── dashboard_octobre.png
├── dashboard_octobre.pdf
└── dashboard_linkedin_jeece.pbix   (à publier seulement avec l'accord de JEECE)
```

## 9. Confidentialité

Les données sont celles de la page LinkedIn de JEECE. Le fichier `.pbix` contient une copie des données importées : il n'est publié qu'avec l'accord de JEECE, ou remplacé par des données fictives ou arrondies.
