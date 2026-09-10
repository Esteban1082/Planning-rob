# Rob.Planning - Debrief projet

Ce document sert de passation pour continuer le projet Rob.Planning avec une autre IA.

## Resume

Rob.Planning est une application HTML standalone pour consulter, preparer et gerer les plannings salaries de la patisserie.

Objectif principal :
- toute l'equipe peut consulter son horaire depuis une page web ;
- Esteban peut administrer le planning avec un token admin ;
- les donnees sont centralisees dans Google Sheet via Google Apps Script ;
- l'app reste simple, portable, sans framework ni build.

## Stack

- HTML unique avec CSS + JS inline.
- Vanilla JS uniquement.
- PWA avec `manifest.webmanifest` + `service-worker.js`.
- Compatible GitHub Pages.
- Donnees via Google Apps Script + Google Sheet.
- Cache local via `localStorage`.

## Dossier actuel du projet

Tous les fichiers importants ont ete regroupes ici :

```text
F:\Projet\Planning
```

## Fichiers importants

```text
F:\Projet\Planning\Planning.md
F:\Projet\Planning\index.html
F:\Projet\Planning\service-worker.js
F:\Projet\Planning\manifest.webmanifest
F:\Projet\Planning\icon.svg
F:\Projet\Planning\brand-spec.md
F:\Projet\Planning\planning_api.gs
F:\Projet\Planning\todoo.md
F:\Projet\Planning\lessons.md
```

Roles des fichiers :

- `index.html` : application principale Rob.Planning.
- `service-worker.js` : cache PWA, derniere version connue `rob-planning-v14`.
- `manifest.webmanifest` : configuration PWA.
- `icon.svg` : icone PWA.
- `brand-spec.md` : direction esthetique v2.
- `planning_api.gs` : API Google Apps Script.
- `todoo.md` : historique des taches realisees.
- `lessons.md` : erreurs, corrections et conventions apprises.

## Google Apps Script

L'app utilise cette URL Apps Script integree directement dans `index.html` :

```text
https://script.google.com/macros/s/AKfycbxkRwejUPDsjpUG6z7oRIQlGxj3lmJbYMgefCrNB8HCzWWI8Q5zHxPYL8tKn_hvg_ar/exec
```

Fonctionnement :
- lecture publique pour l'equipe ;
- ecriture reservee au mode admin ;
- token admin valide via Apps Script ;
- sauvegarde complete du planning dans Google Sheet ;
- cache local en secours.

## Mode admin

Le bouton `Admin` permet de saisir le token.

Important :
- le bouton `Importer` reste cache tant que le token admin n'a pas ete valide pendant la session ;
- un token stocke en local ne suffit pas a afficher les actions admin ;
- les actions admin sont : importer, modifier les cases, ajouter/modifier/supprimer un employe, preparation et copie de semaine.

## Vues de l'application

### Semaine

Vue principale du planning.

Fonctions :
- navigation semaine precedente / suivante ;
- affichage numero de semaine + mois ;
- distinction semaine passee / actuelle / future ;
- semaines previsionnelles marquees `A preparer` ;
- clic sur une case en admin pour modifier le shift ;
- clic sur un nom pour ouvrir la fiche employe.

### Aujourd'hui

Vue du planning du jour.

Fonctions :
- date du jour bien visible ;
- liste des salaries du jour ;
- clic admin pour modifier un shift ;

### Employe

Vue detaillee par employe.

Fonctions :
- selection d'un employe ;
- affichage de tous ses jours, regroupes par semaine pour rester lisible ;
- bouton `Modifier` en admin ;
- bouton `Supprimer` en admin.

Modification employe :
- change le prenom/nom sur toutes les semaines ou cette personne existe ;
- sauvegarde ensuite localement et dans Google Sheet.

Suppression employe :
- retire l'employe de toutes les semaines ou il existe ;
- retire aussi ses horaires ;
- demande confirmation avant suppression.

### Stats

Vue statistiques de la semaine.

Regles :
- total heures travaillees ;
- nombre d'ouvertures ;
- nombre de fermetures ;
- si amplitude >= 9h, retirer 1h de pause repas non payee.

Exemple :

```text
10:15 - 19:15 = amplitude 9h = 8h travaillees
```

## Design

La direction visuelle suit :

```text
F:\Projet\Planning\brand-spec.md
```

Principes :
- theme clair professionnel ;
- couleurs OKLch ;
- typographies Inter + DM Mono ;
- cartes blanches ;
- bordures fines ;
- ombres discretes ;
- interface lisible sur mobile.

Couleurs metier :
- vert = ouverture ;
- bleu = fermeture ;
- violet = special ;
- gris = repos ou case vide.

## Mobile

Sur mobile, la vue semaine ne reste pas en tableau classique.

Elle passe en cartes par jour :
- une section par jour ;
- liste des employes dans chaque jour ;
- contour/couleur selon jour passe, actuel ou futur ;
- meilleure lisibilite que les colonnes desktop.

En mobile paysage :
- retour a la grille semaine complete ;
- colonne employe reduite ;
- chips horaires compactes ;
- cartes par jour conservees uniquement en portrait.

## Etats des cases

Ne pas confondre case vierge et repos.

Etats :

```text
A definir  = case vide, pas encore remplie
Jour off   = repos choisi explicitement
Ouverture  = shift ouverture, vert
Fermeture  = shift fermeture, bleu
Special    = CRDT, VA, Vacances, Ferie, Recup, MAL 1/2T, etc.
```

Les semaines previsionnelles doivent demarrer avec des cases `A definir`, pas avec des repos.

## Semaines futures

L'app permet de consulter et preparer les semaines qui ne sont pas encore importees officiellement.

Comportement :
- l'app cree une avance initiale de semaines futures ;
- il n'y a plus de limite fixe bloquante ;
- quand on clique `Suiv.` sur la derniere semaine connue, l'app cree automatiquement la semaine suivante ;
- les semaines creees sont previsionnelles ;
- elles copient la liste des employes connus ;
- leurs cases sont vierges (`A definir`) ;
- on peut deja poser des vacances, VA, CRDT, etc.

But :
- preparer les absences avant l'import JSON officiel.

## Import JSON

Format attendu :

```json
{
  "weeks": [
    {
      "label": "Sem 19 - 04 au 09 mai 2026",
      "days": [
        "2026-05-04",
        "2026-05-05",
        "2026-05-06",
        "2026-05-07",
        "2026-05-08",
        "2026-05-09"
      ],
      "employees": [
        {
          "firstName": "Esteban",
          "lastName": "RODRIGUEZ VIEJO",
          "shifts": [
            {
              "date": "2026-05-04",
              "type": "repos"
            },
            {
              "date": "2026-05-05",
              "start": "07:00",
              "end": "13:00",
              "type": "Ouverture"
            }
          ]
        }
      ]
    }
  ]
}
```

Lors d'un import :
- les semaines deja existantes peuvent etre remplacees ;
- l'app compare les changements ;
- l'import sauvegarde localement puis tente la sync Google Sheet.

## Regles de lecture des feuilles planning

Regle importante :
- les infos horaires d'une personne commencent au cadre jaune de sa ligne ;
- elles continuent jusqu'au prochain cadre jaune/personne ;
- ne pas rattacher un libelle comme `Atelier` a la mauvaise ligne.

Ordre habituel des employes :

```text
Cordelia GIBSON
Amelie RENAUD
Esteban RODRIGUEZ VIEJO
Christel Barroso
Nathalie STEYER
Rosi DE MUNCK
Khalil CHBOUKI
Sandrine Doget
```

## Regles metier salaries

### Nathalie

`CRDT`, vacances et recuperations peuvent etre sans heures.

Exemples :

```json
{"date":"2026-05-07","type":"CRDT"}
{"date":"2026-05-09","type":"Vacances ANCEM"}
```

### Sandrine

Cas particulier :
- retour de maladie longue duree ;
- souvent `MAL 1/2T` ;
- travaille souvent mardi, mercredi, vendredi, samedi ;
- horaires habituels autour de `08:00-12:00`, mais verifier sur la feuille.

### Ouverture / Fermeture

Si le type n'est pas explicite mais qu'une heure existe :
- debut avant midi = ouverture ;
- debut apres midi = fermeture.

## Raccourcis edition

Dans la modale de modification de shift :
- Ouverture ;
- Fermeture ;
- Repos ;
- Autre ;
- raccourcis speciaux : `CRDT`, `VA`, `VAE`, `Recup JF`, `Ferie`, `MAL 1/2T`.

On peut :
- mettre un libelle libre ;
- mettre ou non des heures ;
- cocher pour ne pas afficher l'horaire.

## Dernieres corrections importantes

- Bouton `Importer` cache hors admin valide.
- Validation admin stricte via Apps Script.
- Titre semaine avec numero + mois.
- Design repris depuis `brand-spec.md`.
- Vue mobile par jour.
- Suppression employe.
- Modification infos employe.
- Navigation semaines sans limite.
- Semaines futures previsionnelles.
- `Jour off` pour repos explicite.
- `A definir` pour case vide.
- Copie de semaine admin avec mapping par index de jour.
- Dedoublonnage des semaines par date de debut : la copie la plus renseignee est conservee.
- Vue employe regroupee par semaine.
- Mobile paysage en grille complete compacte.
- Cache PWA `rob-planning-v16`.

## Points a surveiller

- Apres mise en ligne, faire un refresh complet a cause du service worker.
- Si l'app semble ancienne, verifier `service-worker.js` et son `CACHE_NAME`.
- Verifier que Google Sheet ne grossit pas trop si beaucoup de semaines futures sont modifiees.
- Verifier qu'un import JSON officiel remplace correctement une semaine previsionnelle (dedoublonnage automatique en place).
- Eventuellement ajouter plus tard :
  - masquer les semaines previsionnelles totalement vides ;
  - export PDF propre ;
  - historique des modifications ;
  - recherche employe ;
  - verrouillage admin plus robuste cote Apps Script.

## Verification technique actuelle

Derniere verification effectuee :

```text
HTML JS syntax OK
CACHE_NAME rob-planning-v16
```
