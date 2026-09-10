# Rob.Planning - Evolutions UX et preparation

## Objectif

Ameliorer Rob.Planning pour faciliter la preparation des semaines futures, la recherche employe, la lecture mobile et la fiabilite des compteurs.

---

## Phase 1 - Compteurs personnes actives

- [x] Creer une fonction commune `isWorkingShift(shift)`.
- [x] Exclure des compteurs les cases vides `A definir`.
- [x] Exclure des compteurs les repos explicites `Jour off`.
- [x] Exclure des compteurs les absences speciales type `VA`, `CRDT`, `MAL 1/2T`, `Vacances`, `Ferie`, `Recup`.
- [x] Compter comme actif un shift avec horaires de travail.
- [x] Corriger le compteur dans la vue `Aujourd'hui`.
- [x] Corriger le compteur par jour dans la vue mobile semaine.
- [x] Verifier que les compteurs affichent bien le nombre de personnes qui travaillent ce jour-la.

---

## Phase 2 - Recherche employe

- [ ] Ajouter un champ de recherche employe. (retire — pas utile)
- [ ] Filtrer les employes par prenom et nom pendant la saisie. (retire — pas utile)
- [x] Conserver le select employe actuel pour ne pas casser l'existant.
- [ ] Permettre l'ouverture rapide de la fiche employe depuis un resultat. (retire)
- [ ] Afficher un etat vide si aucun employe ne correspond. (retire)
- [x] Verifier que la recherche fonctionne avec tous les employes connus sur toutes les semaines.

---

## Phase 3 - Fiche employe centree sur aujourd'hui

 - [x] Dans `renderEmployee()`, identifier la ligne correspondant a aujourd'hui.
 - [x] Si aujourd'hui existe pour l'employe, scroller automatiquement dessus.
 - [x] Si aujourd'hui n'existe pas, scroller sur la premiere date future.
 - [x] Si aucune date future n'existe, rester en haut de la fiche.
 - [x] Ajouter un style visuel discret sur la ligne du jour actuel.
 - [x] Verifier que l'ouverture depuis la semaine et depuis la recherche garde ce comportement.

## Phase 3b - Ouvrir directement la vue "Aujourd'hui" au lancement

- [x] Definir `currentView = 'today'` dans le handler DOMContentLoaded.
- [x] Vérifier que la vue `Aujourd'hui` s'affiche au demarrage.

---

## Phase 4 - Mode preparation

- [x] Ajouter un onglet admin-only `Preparation`.
- [x] Scanner la semaine actuelle et les semaines futures.
- [x] Lister uniquement les cases `A definir`.
- [x] Grouper les cases par semaine puis par jour.
- [x] Afficher un compteur global du type `18 cases a definir`.
- [x] Cliquer sur une ligne doit ouvrir la modale de modification du shift.
- [x] Apres modification, rafraichir la liste de preparation.
- [x] Verifier que le mode reste cache hors admin valide.

---

## Phase 5 - Copie de semaine

- [x] Ajouter un bouton admin `Copier semaine`.
- [x] Creer une modale de copie.
- [x] Ajouter un select `Semaine source`.
- [x] Ajouter un select `Semaine destination`.
- [x] Copier les employes de la semaine source vers la destination (ajout si absent).
- [x] Adapter les dates des shifts a la semaine destination.
- [x] Remplacer tous les shifts destination selon source (sauf employés non présents dans la source : on ne les modifie pas).
- [x] Marquer la semaine destination comme previsionnelle si elle ne vient pas d'un import officiel.
- [x] Sauvegarder localement puis synchroniser Google Sheet.
- [x] Verifier qu'une copie ne modifie jamais la semaine source.
- [x] Afficher une notif de succès/erreur.

- [x] Decision finale copie : copier les absences speciales comme la source pour garder une vraie duplication de semaine.

---

## Phase 6 - Commentaires internes

- [x] Fonctionnalite retiree apres validation terrain : pas utile pour l'usage actuel.
- [x] Supprimer la modale de commentaire.
- [x] Supprimer les boutons/pastilles dans la vue semaine et mobile.
- [x] Nettoyer automatiquement les anciennes donnees de commentaires au chargement.
- [x] Conserver la base plus simple : planning, preparation, copie, employes, stats.

---

## Phase 7 - Mobile paysage

- [x] Ajouter une media query pour mobile paysage.
- [x] Utiliser une condition du type `@media (max-width: 900px) and (orientation: landscape)`.
- [x] En paysage, afficher la grille semaine complete.
- [x] En portrait, conserver les cartes par jour.
- [x] Reduire la largeur de la colonne employe en paysage.
- [x] Compacter les chips horaires en paysage.
- [x] Verifier la lisibilite sur telephone en portrait et paysage.

---

## Phase 8 - Verification technique

- [x] Verifier la syntaxe JavaScript de `index.html`.
- [x] Tester la vue `Semaine`.
- [x] Tester la vue `Aujourd'hui`.
- [x] Tester la vue `Employe`.
- [x] Tester la vue `Stats`.
- [x] Tester le mode admin valide.
- [x] Tester que les actions admin restent cachees hors admin.
- [x] Tester la sauvegarde locale.
- [x] Tester la sync Google Sheet si disponible.
- [x] Bumper le cache PWA dans `service-worker.js`.
- [x] Passer `CACHE_NAME` de `rob-planning-v13` a `rob-planning-v14`.

---

## Phase 9 - Documentation projet

- [x] Mettre a jour `todoo.md`.
- [x] Mettre a jour `lessons.md` si un bug est corrige.
- [x] Ajouter une ligne de lesson pour le bug compteur personnes actives.
- [x] Ajouter une ligne de lesson pour la vue mobile paysage si le pattern est valide.
- [x] Mettre a jour `Planning.md` avec les nouvelles fonctionnalites livrees.

---

## Decisions a valider

- [x] Decider si un libelle sans heures comme `Formation` doit compter comme personne active. Decision : non, actif = horaires valides uniquement.
- [x] Decider si la copie de semaine doit copier les absences speciales ou les remettre en `A definir`. Decision : copier tel quel pour une duplication fidele.
- [x] Decider si les commentaires internes doivent rester. Decision : retire, pas utile pour l'instant.

---

## Phase 10 - Publication des semaines futures

- [x] Ajouter un etat `published` sur les semaines.
- [x] Verrouiller les semaines futures par defaut pour l'equipe.
- [x] Permettre a l'admin de voir et modifier toutes les semaines futures.
- [x] Ajouter un bouton admin `Deverrouiller la semaine`.
- [x] Filtrer les semaines verrouillees dans les vues publiques.
- [x] Bumper le cache PWA de `rob-planning-v14` a `rob-planning-v15`.
- [x] Verifier la syntaxe JavaScript de `index.html`.

---

## Phase 11 - Dedoublonnage des semaines

- [x] Identifier une semaine par sa date de debut, independamment de son libelle.
- [x] Garder la copie la plus renseignee lorsqu'un doublon est detecte.
- [x] Appliquer la regle a l'import, au cache local et a la synchronisation Google.
- [x] Verifier la syntaxe JavaScript et mettre a jour le cache PWA.
