# Spécification fonctionnelle — Plateforme de location Set et Brique

> Document interne (AGON-GATE) — à usage d'Alexis et Madus pour le développement. Ne pas
> diffuser tel quel à la cliente. Basé sur le [devis](devis-set-et-brique.md), le
> [contrat](contrat-set-et-brique.md) et les échanges de cadrage successifs (voir
> [README.md](README.md) pour les points encore ouverts et les décisions reportées en V2).

## Objectif du document

Décrire, module par module, les règles métier concrètes à implémenter : statuts, données,
comportements attendus, cas limites déjà tranchés, et ce qui reste en attente. Rien dans ce
document n'est inventé — tout ce qui n'est pas explicitement confirmé ci-dessous doit être
posé en question avant développement plutôt que supposé.

## Légende

- ✅ Règle confirmée, prête à développer.
- ⏳ En attente d'une réponse de la cliente (Marion) ou d'Alexis — cf. [questions-recap-a-envoyer.docx](questions-recap-a-envoyer.docx).
- 🔜 Reporté à une V2, ne pas développer maintenant.

---

## 1. Location pure — catalogue & stock

**Fiche article (par set) — champs confirmés (document client du 11/09, exemple détaillé
"Faucon Millénium" + [catalogue complet](catalogue-sets-lego.md)) :**
- ✅ Titre.
- ✅ Description (zone de texte libre).
- ✅ Nombre de pièces total.
- ✅ Dimensions une fois construit.
- ✅ Numéro(s) du set — **peut être multiple** : un article loué regroupe parfois plusieurs
  boîtes LEGO officielles combinées (ex. NINJAGO = 2 références, Château Harry Potter = 2,
  Gare et train = 3). Le champ doit donc supporter une liste de références, pas une seule.
- ✅ Nombre de notices (total, somme des références combinées le cas échéant).
- ✅ Type de notice : papier ou numérique — **si numérique, avertissement à afficher au
  client** (nécessite un accès à internet par téléphone/PC/tablette pour suivre le montage).
- ✅ Nombre de figurines.
- ✅ Temps estimatif de montage.
- ✅ Âge conseillé.
- ✅ Marque : LEGO ou autre — confirmé nécessaire, le catalogue contient au moins une
  référence d'une autre marque ("La mine de l'ouest", marque "PANTASY").
- ✅ Montant de la caution (par set, déjà su — cf. [grille des cautions](catalogue-sets-lego.md),
  de 100 € à 650 € selon le set).
- ✅ Champ commentaire libre, **visible côté client** sur la fiche du set (vide sur tous les
  sets actuels, à remplir au cas par cas par Set et Brique).
- ✅ Poids du set : stocké en interne (utile à la vérification au retour, module 9), mais
  **jamais affiché côté client** — décision volontaire pour éviter que les locataires ne
  devinent le contenu exact des sachets pesés.
- ⏳ Nombre de photos à afficher par set : on avait proposé 3 à 4 par défaut, mais l'exemple
  fourni par la cliente pour le Faucon Millénium contient 12 photos. À reclarifier : faut-il
  toutes les afficher, ou nous laisse-t-elle choisir parmi celles fournies ?

**Stock :**
- ✅ **28 références au catalogue** (mise à jour de l'estimation "~25" du RDV de cadrage) —
  liste complète dans [catalogue-sets-lego.md](catalogue-sets-lego.md). Un seul exemplaire
  par référence aujourd'hui, mais le modèle de données doit prévoir le multi-stock (plusieurs
  exemplaires d'une même référence) pour une évolution future — ne pas coder en dur
  "quantité = 1".

**Statuts d'un set :**
- ✅ `Disponible` / `Location` / `Battement` / `Réparation` / `Retiré`.

**Délai de battement (entre deux locations) :**
- ✅ Réglable individuellement par set (valeur par défaut 4 jours), modifiable par Marion
  elle-même depuis le back-office.

**Hors périmètre :**
- Suivi des colis envoyés/reçus — retiré, sans objet en V1 (100 % remise en main propre).

---

## 2. Planning des locations

- ✅ Le planning est **propre à chaque lieu de retrait** (une seule personne fait les
  remises, donc impossible d'être sur deux lieux en même temps sur le même créneau).
- ✅ Lieux de retrait réels confirmés (5) : aire de covoiturage de Lanester (à côté du McDo),
  covoiturage de Guidel, covoiturage de Kerizan (Brec'h), Intermarché Drive de Monistrol
  (Lorient), covoiturage de Plouay.
- ⚠️ Un client ne peut réserver que l'une des 4 durées fixes du tarif (4j / 7j / 15j /
  1 mois) — pas de durée libre dans le tunnel. **Ce point est remis en question** par le
  nouveau modèle tarifaire annoncé par la cliente (2 €/jour, tous les sets — cf. README,
  section tarifaire) : si la durée devient libre en nombre de jours plutôt que fixée à 4
  paliers, cette règle et tout le tunnel de réservation doivent être repensés. **Ne pas
  développer le sélecteur de durée tant que ce point n'est pas éclairci avec Alexis.**
  Pas de durée maximale de location côté entreprise (confirmé par la cliente), quel que soit
  le modèle tarifaire retenu.
- ✅ **Le tunnel réserve une date, pas un créneau horaire précis.** L'heure exacte de remise
  est négociée directement avec le client par téléphone/email en V1 (un outil de chat est
  souhaité par la cliente mais explicitement pas développé pour l'instant — cf. section V2) —
  pas de sélecteur d'horaire dans le tunnel en V1. (Confirmé par la cliente ; elle envisage
  d'ajouter une tranche horaire plus tard, cf. section V2.)
- ✅ **Durée comptée en jours calendaires, pas en blocs de 24h.** Une location démarrant un
  mardi (quelle que soit l'heure de remise ce jour-là) et prévue pour 4 jours se termine le
  vendredi — le client doit rendre le set ce jour-là, quelle que soit l'heure. Cette règle
  détermine la date de retour attendue pour tous les calculs (rappel J-1, déclenchement de
  la relance de retard, etc.).
- ✅ **Prolongation d'une location en cours** : possible, mais **gérée manuellement, pas par
  un bouton dédié dans l'espace client**. Le client contacte Set et Brique (téléphone ou
  email — pas de chat en V1) pour vérifier la disponibilité, puis effectue lui-même une
  **nouvelle réservation via le tunnel** pour les jours supplémentaires — comme une location
  normale qui suit immédiatement la première. Aucun développement spécifique nécessaire pour
  la prolongation en elle-même.
- ⏳ **Battement lors d'une prolongation immédiate — exemple concret** : un client loue le
  Faucon Millénium du 1er au 8, puis décide de le garder plus longtemps et réserve lui-même,
  via le tunnel, une nouvelle location du 9 au 15 *pour ce même set qu'il a déjà en main* (il
  ne le rend jamais entre les deux réservations). Le délai de battement standard (4 jours par
  défaut, prévu pour que Marion nettoie/vérifie le set entre deux locataires différents) n'a
  ici aucune raison d'exister puisque rien n'est physiquement rendu. Question : le système
  doit-il quand même imposer ce délai entre les deux réservations du même client (ce qui
  bloquerait cette prolongation en pratique), ou faut-il le sauter spécifiquement dans ce cas ?
- ✅ **Outil de chat** : souhaité par la cliente comme canal de contact, mais confirmé par
  Alexis comme **non développé en V1**, éventuellement en V2.
- ⏳ Marion doit-elle pouvoir bloquer des dates à l'avance sur le planning (vacances,
  indisponibilité générale), indépendamment du statut de chaque set ? Non tranché — remonté
  par Madus en lien avec le délai minimum de réservation. Possiblement sans objet si la
  validation manuelle des réservations (module 5) est confirmée.

**Reporté en V2 :**
- 🔜 Outil de chat pour contacter la cliente — souhaité mais pas développé en V1.
- 🔜 Tranche horaire précise sélectionnable dans le tunnel (créneaux de remise) — la cliente
  confirme elle-même vouloir l'envisager plus tard, "pas dans l'immédiat".

---

## 3. Comptes clients & espace de suivi

**Inscription — champs obligatoires :**
- ✅ Nom, prénom, email, téléphone, adresse postale (informations nécessaires à la
  facturation). Rien de plus (pas de date de naissance, etc.).

**Locations simultanées :**
- ⏳ Un client peut-il avoir plusieurs locations actives en même temps, ou une seule à la
  fois ? Non tranché, question posée à Marion.

**Compte bloqué :**
- ✅ Statut "compte bloqué" à prévoir, activé/désactivé **manuellement par Marion** depuis le
  back-office (ex. set non rendu, litige en cours) — empêche toute nouvelle réservation tant
  qu'il est actif.

**Espace de suivi (fusion de l'ancien module "Espace client") :**
- ✅ Le client voit l'avancement de ses locations en cours : prochaine date de retrait, set
  concerné, statut récupéré / à rendre, date de retour prévue, retard éventuel.

---

## 4. Paiement Stripe

**Modes de paiement du loyer :**
- ✅ Deux modes possibles : paiement en ligne (Stripe, prélevé **à la réservation**), ou
  paiement sur place par **TPE** au moment de la remise en main propre.

**Caution — sujet résolu (document client du 11/09) :**
- ✅ Toujours en **pré-autorisation standard Stripe (7 jours)**, jamais débitée sauf litige.
- ✅ **Pas de solution technique de prolongation nécessaire** (ni Swikly, ni autorisation
  étendue Stripe, ni PayPal) : la cliente a tranché elle-même. Un hold bancaire au-delà de
  7 jours est techniquement impossible, elle en est consciente et **prend une assurance
  professionnelle de son côté** pour couvrir le risque sur les locations de plus de 7 jours
  (15 jours, 1 mois). Aucun développement supplémentaire à prévoir sur ce point — ce qui
  clôt toute la réflexion Swikly/PayPal/autorisation étendue menée précédemment.
- ⏳ Si le loyer est payé par TPE sur place, comment se fait la pré-autorisation de la
  caution (carte enregistrée en amont côté client tout de même) ? Question posée à Marion —
  reste ouverte indépendamment du point ci-dessus.
- ✅ Restitution de la caution : automatique 48h après réception du set, **avec un plafond de
  7 jours maximum** dans tous les cas (indépendant de la rapidité de Marion à valider l'état
  des lieux).

**Hors périmètre :**
- Pas d'option d'assurance/protection additionnelle — la caution est la seule protection.

**Cas d'échec / abandon :**
- ✅ Échec du paiement en ligne (carte refusée) → créneau bloqué **1h** le temps que le
  client trouve une solution.
- ✅ Abandon en cours de paiement (avant tentative) → créneau libéré après **15 min**.

---

## 5. Tunnel de réservation client

- ✅ Parcours : sélection du set → choix de la durée (4 options fixes) → choix de la **date**
  de retrait (pas d'heure précise, cf. module 2) → paiement (en ligne, à la remise par TPE,
  ou par bon cadeau — module 6). L'heure exacte de remise se négocie après coup, hors tunnel.
- ✅ En V1, tous les sets sont disponibles à tous les lieux de retrait (pas de restriction
  géographique par set).
- ⚠️ **Point majeur à confirmer avec Alexis avant développement** : la cliente indique
  qu'elle veut "avoir la main sur la demande de location" et pouvoir accepter/refuser/
  proposer d'autres dates — ce qui suggère une **validation manuelle de chaque réservation**
  plutôt qu'une confirmation automatique instantanée par le tunnel. Si confirmé, ça change le
  flux : la réservation devient une *demande* en attente, avec une étape d'approbation côté
  back-office avant confirmation définitive. Reste à définir : le paiement est-il pris avant
  ou après cette validation, et que se passe-t-il en cas de refus d'une réservation déjà
  payée (remboursement automatique) ? **Ne pas développer le flux de confirmation
  automatique tant que ce point n'est pas tranché avec Alexis.**

---

## 6. Bons cadeaux

Module ajouté en cours de cadrage (absent du devis initial), à la demande de la cliente.
Listé "Inclus" dans le devis — pas de surcoût, présenté comme rendu possible grâce à la
réutilisation des modules Paiement Stripe (4) et Tunnel de réservation (5).

**Confirmé :**
- ⚠️ Bons vendus aux montants de la grille tarifaire, pas de montant libre — **montants
  exacts à revoir** : la grille actuelle (10 € / 15 € / 25 € / 45 €) est remise en question
  par le nouveau modèle tarifaire annoncé par la cliente (2 €/jour, tous les sets). Ne pas
  figer les montants des bons tant que ce point n'est pas éclairci avec Alexis (cf. module 2
  et README).
- ✅ Génération automatique d'un code unique par bon acheté, envoyé par email à l'acheteur.
- ✅ Utilisable comme moyen de paiement dans le tunnel de réservation, au même titre que
  Stripe ou le TPE.
- ✅ Suivi des bons émis (valide / utilisé / expiré) depuis le back-office.

**Encore à trancher :**
- ⏳ Un bon doit-il correspondre exactement à une des 4 durées (ex. bon de 15 € utilisable
  uniquement pour une location de 7 jours), ou est-ce un crédit utilisable pour compléter un
  paiement plus important avec un autre moyen de paiement pour la différence ? **À
  reconsidérer une fois le nouveau modèle tarifaire clarifié** — si la durée devient libre en
  jours, la logique "bon = une des 4 durées" ne tient plus telle quelle.
- ⏳ Si le montant du bon dépasse le prix de la location choisie, le solde restant est-il
  conservé pour une prochaine location, ou perdu ?
- ⏳ Un bon est-il nominatif (lié à un compte client), ou utilisable par toute personne
  détenant le code (cas classique du cadeau) ?
- ⏳ Durée de validité : la loi française impose un minimum d'1 an pour les bons d'achat non
  alimentaires (loi Chatel). Quelle durée exacte Marion souhaite-t-elle (1 an, 2 ans,
  illimité) ?
- ⏳ Un bon acheté et non utilisé peut-il être remboursé/annulé par le client ?
- ⏳ Le même système peut-il aussi servir à émettre des avoirs gratuits (offerts par Marion,
  sans achat) pour compenser un client lésé par un retard de retour (cf. point ouvert du
  module 1/2 sur le chevauchement de réservation) ?

---

## 7. Contrat de location / conditions générales

- ✅ **Contrat généré automatiquement (PDF) à chaque réservation** — pas un simple texte CGL
  statique. Le PDF doit reprendre les mentions légales, le set loué, la durée, les dates de
  retrait/retour et le montant. Réutiliser le même mécanisme de génération PDF que les
  factures (module 8) est probablement pertinent.
- ✅ Acceptation par **simple case à cocher** ("J'accepte les conditions générales") avant
  validation de la commande — pas de signature électronique nominative en V1. C'est cette
  acceptation qui déclenche la génération du contrat.
- ✅ Le document physique remis dans la boîte lors du prêt (avertissement d'âge conseillé)
  est **géré indépendamment par Marion**, hors système — rien à générer côté plateforme.
- ⏳ Le contenu légal exact du contrat (barèmes de pénalités, annulation, retard) dépend des
  réponses de Marion — **le moteur de génération PDF peut être développé dès maintenant, mais
  le texte légal définitif ne pourra être finalisé qu'après ses réponses**.
- Prix du module inchangé (300 €) malgré ce développement plus conséquent que la case à
  cocher initialement prévue — décision d'Alexis d'absorber le coût dans le forfait.

**Politique d'annulation — résolu (document client du 11/09) :**
- ✅ Annulation à l'initiative du client :
  - Plus de 7 jours avant le retrait : gratuite (remboursement intégral).
  - Entre 7 jours et 48h avant : 50 % du prix de la location retenu.
  - Moins de 48h avant : 100 % retenu (pas de remboursement).
- ✅ Annulation à l'initiative de Set et Brique : trois options au choix de Marion —
  proposition d'un changement de date (gratuit), avoir sur un autre set disponible, ou
  remboursement. Ce cas confirme que le système d'avoir (module 6, bons cadeaux) doit pouvoir
  être déclenché gratuitement par Marion, pas seulement acheté par un client.

---

## 8. Facturation

- ✅ Nouvelle numérotation de factures, **repart de zéro** (pas de reprise de Poppins ou
  d'une séquence existante).
- ✅ Facture générée uniquement une fois le paiement confirmé :
  - automatiquement pour un paiement en ligne ou par bon cadeau ;
  - après que Marion ait marqué manuellement la réservation "payée" sur le site, pour un
    paiement TPE sur place.
  - **Jamais de facture avant paiement effectif.**
- 🔜 Export comptable complet (format, fréquence) : non défini, Marion n'a pas d'outil de
  comptabilité précis à ce jour — reporté en V2 (voir aussi module 11 pour l'export simple
  des ventes, inclus en V1).

---

## 9. État des lieux & gestion des dommages

**Processus de retour :**
- ✅ Le client dépose physiquement le set, sans déclaration formelle à faire (il peut
  signaler un souci à l'oral, ce n'est pas obligatoire).
- ✅ Marion réalise l'état des lieux **plus tard**, généralement le soir même — c'est elle
  seule qui valide si le set est rentré en bon état.
- ✅ Cette validation déclenche le compte à rebours des 48h avant libération automatique de
  la caution (cf. module 4 / règle déjà actée).

**Retard de retour — entièrement résolu (2ᵉ document client du 11/09), remplace l'ancienne
règle "rappel 48h avant + relance quotidienne" :**
- ✅ La date de retour attendue se calcule en jours calendaires (cf. module 2) : le client
  doit rendre le set **le jour J de la fin de location, à n'importe quelle heure**.
- ✅ Tolérance d'environ **30 minutes après la fermeture/l'horaire habituel du jour J** avant
  qu'un retard soit considéré comme tel.
- ✅ Séquence d'emails automatiques : **J-1** (veille de la fin), **J** (jour de fin), **J+1**
  (retard constaté), **J+2**.
- ✅ **J+1** : un forfait de retard de **30 €** est appliqué.
- ✅ **J+2** : la **caution entière** est prélevée.
- ✅ Marion doit pouvoir **stopper manuellement cette séquence** depuis le back-office (ex. si
  elle joint le client par téléphone et obtient une explication valable) — prévoir une action
  "suspendre/annuler la procédure de retard" sur chaque réservation concernée.

**Barème de pénalité (pièces manquantes/cassées, notices, figurines) — résolu :**

| % de pertes constaté | Retenue sur caution |
|---|---|
| 0 à 5 % | Aucune |
| 5 à 10 % | 10 € |
| 10 à 15 % | 30 € |
| 15 à 20 % | 50 € |
| Plus de 20 % | Prix du set neuf, référence **Bricklink** |
| Figurine manquante | Prix (référence Bricklink) |
| Notice manquante | Prix (référence Bricklink) |
| Set non restitué | Prix du set (référence Bricklink) |

- ✅ **Forfait démontage** : 20 € si le client rapporte le set assemblé ("en vrac") au lieu de
  le redémonter dans ses sachets d'origine.
- ⏳ Reste ouvert : comment le "% de pertes" est-il calculé en pratique — à partir de l'écart
  de poids constaté à la pesée (cf. module 1), ou d'un comptage précis des pièces manquantes
  par rapport à la notice ? C'est Set et Brique qui détermine cette méthode (Alexis doit le
  leur demander explicitement).
- Bricklink est un site de référence communautaire pour les prix des pièces/sets LEGO
  d'occasion — la consultation du prix de référence sera probablement manuelle (Marion), pas
  une intégration automatisée, sauf si une API est envisagée plus tard.

---

## 10. Notifications automatisées

- ✅ Canal : email uniquement en V1 (pas de SMS).
- ✅ Séquence de rappel/relance autour de la fin de location : **J-1, J, J+1, J+2** — voir le
  détail complet (forfaits associés à J+1/J+2, arrêt manuel possible) au module 9. Cette
  séquence remplace l'ancienne règle "rappel 48h avant + relance quotidienne indéfinie".
- ✅ Textes des emails rédigés par défaut par AGON-GATE, mais **Marion doit pouvoir les
  modifier elle-même** — prévoir un éditeur de templates dans le back-office.

---

## 11. Back-office & reporting

- ✅ Deux comptes d'accès en V1 : Alexis (admin), Marion (compte propriétaire). Pas d'autre
  utilisateur prévu pour l'instant.
- ✅ Tableau de bord : chiffre d'affaires du mois, taux d'occupation par set, sets les plus
  loués.
- ✅ Export simple des ventes par année civile, inclus en V1 (distinct de l'export comptable
  complet, qui reste en V2). Modèle de données : une **vente** est un nœud entre un **set**,
  une **durée** et un **client** — c'est cet élément qui doit être interrogeable en base pour
  produire cet export.
- 🔜 Export comptable complet (format lié à un outil de comptabilité) : reporté en V2, Marion
  n'a pas d'outil précis à ce jour.

---

## 12. Intégration, tests, déploiement, formation

- ✅ Formation destinée à Marion uniquement.
- ✅ Format : documentation écrite + sessions en présentiel chez elle (pas de visio prévue).

---

## Récapitulatif des points bloquants (⏳ en attente de Marion ou d'Alexis)

Cf. [README.md](README.md) pour le détail. **Résolus depuis le 2ᵉ document client du
11/09** (ne figurent plus ici) : barème de pénalité, montant de la pénalité de retard,
politique d'annulation, lieux de retrait. **Résolu précédemment** : sujet caution/durée de
préautorisation (Swikly, Stripe étendu, PayPal — cf. module 4). **Résolu (réponse
d'Alexis) :** nature du "chat" — souhaité mais pas développé en V1, cf. section V2.

1. **⚠️ Nouveau modèle tarifaire (2 €/jour, tous les sets)** — Alexis doit d'abord clarifier
   en interne comment ça s'articule avec les 4 durées fixes actuelles avant qu'on formule la
   question précise à poser à Marion. Bloque potentiellement : grille tarifaire, montants des
   bons cadeaux (module 6), fonctionnement du tunnel (module 5, module 2).
2. **Validation manuelle des réservations** — la cliente semble indiquer qu'elle valide
   chaque demande une par une ; à confirmer avec Alexis avant de développer le flux de
   confirmation automatique du tunnel, notamment le moment du paiement (module 5).
3. Comment le "% de pertes" du barème est-il calculé (écart de poids ou comptage précis) ?
   C'est Set et Brique qui détermine cette méthode (module 9).
4. Gestion du client suivant en cas de non-retour dans les temps (modules 1, 2).
5. Locations simultanées par client — oui/non (module 3).
6. Pré-autorisation de la caution en cas de paiement TPE sur place (module 4).
7. Nombre de photos par set à afficher — la proposition initiale (3-4) est contredite par
   l'exemple fourni (12 photos pour un set) ; à réclarifier (module 1).
8. Blocage de dates à l'avance sur le planning par Marion, vacances/indisponibilité (module 2)
   — possiblement sans objet si la validation manuelle (point 2) est confirmée.
9. **Battement lors d'une prolongation immédiate** (même client, même set, sans retour
   physique entre les deux réservations) — exemple détaillé au module 2 (module 2).
10. Bon cadeau = valeur exacte d'une durée, ou crédit complétable par un autre moyen de
    paiement — à reconsidérer une fois le point 1 clarifié (module 6).
11. Solde restant d'un bon cadeau partiellement utilisé : conservé ou perdu (module 6).
12. Bon cadeau nominatif ou utilisable par toute personne détenant le code (module 6).
13. Durée de validité légale du bon cadeau — minimum 1 an, valeur exacte à définir (module 6).
14. Remboursement/annulation d'un bon cadeau non utilisé (module 6).
15. Réutilisation du système de bons cadeaux pour émettre des avoirs gratuits en cas de
    litige (lien avec le point 4) (module 6) — probable vu la politique d'annulation
    (module 7), à confirmer explicitement.

## Récapitulatif des points reportés en V2 (🔜)

1. Outil de chat pour contacter la cliente — souhaité mais pas développé en V1 (module 2).
2. Tranche horaire précise sélectionnable dans le tunnel de réservation (module 2) — la
   cliente envisage de l'ajouter plus tard, "pas dans l'immédiat". En V1, l'heure se négocie
   hors tunnel (téléphone/email).
3. Export comptable complet (modules 8, 11) — l'export simple des ventes par année civile,
   lui, est inclus en V1 (module 11).

**Résolu, ne figure plus en V2 :** la prolongation d'une location en cours ne nécessite
finalement aucun développement dédié — elle se fait via une nouvelle réservation standard
dans le tunnel après accord manuel avec la cliente (cf. module 2).
