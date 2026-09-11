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
- ✅ Un client ne peut réserver que l'une des 4 durées fixes du tarif (4j / 7j / 15j /
  1 mois) — pas de durée libre dans le tunnel. **Pas de durée maximale de location** côté
  entreprise (confirmé par la cliente), mais le tunnel ne propose toujours que les 4 durées :
  au-delà, cf. règle de prolongation ci-dessous.
- ✅ **Le tunnel réserve une date, pas un créneau horaire précis.** L'heure exacte de remise
  est négociée directement avec le client via chat/téléphone/email après la réservation — pas
  de sélecteur d'horaire dans le tunnel en V1. (Confirmé par la cliente ; elle envisage
  d'ajouter une tranche horaire plus tard, cf. section V2.)
- ✅ **Durée comptée en jours calendaires, pas en blocs de 24h.** Une location démarrant un
  mardi (quelle que soit l'heure de remise ce jour-là) et prévue pour 4 jours se termine le
  vendredi — le client doit rendre le set ce jour-là, quelle que soit l'heure. Cette règle
  détermine la date de retour attendue pour tous les calculs (rappel J-48h, déclenchement de
  la relance de retard, etc.).
- ✅ **Prolongation d'une location en cours** : possible, mais **gérée manuellement, pas par
  un bouton dédié dans l'espace client**. Le client contacte Set et Brique (téléphone, chat
  ou email) pour vérifier la disponibilité, puis effectue lui-même une **nouvelle réservation
  via le tunnel** pour les jours supplémentaires — comme une location normale qui suit
  immédiatement la première. Aucun développement spécifique nécessaire pour la prolongation
  en elle-même.
- ⏳ Cette nouvelle réservation "à la suite" doit-elle sauter le délai de battement (puisque
  le set n'est jamais physiquement rendu entre les deux réservations), ou le système la
  traite-t-il comme une location normale avec battement standard, obligeant Marion à
  l'ajuster manuellement au cas par cas ?
- ⏳ "Chat" mentionné comme canal de contact (ici et pour la prise de créneau horaire) — est-ce
  un outil existant côté cliente (WhatsApp, Messenger...) à référencer simplement sur le site,
  ou une fonctionnalité de chat en direct à développer sur la plateforme ? Pas dans le
  périmètre actuel du devis si c'est la 2ᵉ option — à clarifier avant de l'assumer.
- ⏳ Marion doit-elle pouvoir bloquer des dates à l'avance sur le planning (vacances,
  indisponibilité générale), indépendamment du statut de chaque set ? Non tranché — remonté
  par Madus en lien avec le délai minimum de réservation.

**Reporté en V2 :**
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

---

## 6. Bons cadeaux

Module ajouté en cours de cadrage (absent du devis initial), à la demande de la cliente.
Listé "Inclus" dans le devis — pas de surcoût, présenté comme rendu possible grâce à la
réutilisation des modules Paiement Stripe (4) et Tunnel de réservation (5).

**Confirmé :**
- ✅ Bons vendus aux montants de la grille tarifaire uniquement (10 € / 15 € / 25 € / 45 €) —
  pas de montant libre.
- ✅ Génération automatique d'un code unique par bon acheté, envoyé par email à l'acheteur.
- ✅ Utilisable comme moyen de paiement dans le tunnel de réservation, au même titre que
  Stripe ou le TPE.
- ✅ Suivi des bons émis (valide / utilisé / expiré) depuis le back-office.

**Encore à trancher :**
- ⏳ Un bon doit-il correspondre exactement à une des 4 durées (ex. bon de 15 € utilisable
  uniquement pour une location de 7 jours), ou est-ce un crédit utilisable pour compléter un
  paiement plus important avec un autre moyen de paiement pour la différence ?
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

**Retard de retour — trigger désormais connu (document client du 11/09) :**
- ✅ La date de retour attendue se calcule en jours calendaires (cf. module 2) : le client
  doit rendre le set **le jour J de la fin de location, à n'importe quelle heure**.
- ✅ Tolérance d'environ **30 minutes après la fermeture/l'horaire habituel du jour J** avant
  qu'un retard soit considéré comme tel. Au-delà (ou passé le jour J), les pénalités de
  retard s'appliquent.
- ⏳ Montant/barème exact de la pénalité de retard : toujours non tranché — seul le
  déclencheur (quand ça compte comme un retard) est désormais connu.

**Barème de pénalité (pièces manquantes/cassées) :**
- ⏳ Non tranché (pièce manquante/cassée) — **ne pas développer les prélèvements
  automatiques sur caution avant réponse de Marion**.

---

## 10. Notifications automatisées

- ✅ Canal : email uniquement en V1 (pas de SMS).
- ✅ Rappel avant retour : envoyé **48h avant** la date de retour prévue.
- ✅ Relance en cas de retard : démarre **le lendemain** de la date de retour dépassée (vers
  10h), puis **se répète chaque jour** tant que le set n'est pas rendu.
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

Cf. [README.md](README.md) pour le détail — ne pas démarrer les modules 6 et 7 tant que les
barèmes/règles ne sont pas connus. **Le sujet caution/durée de préautorisation (Swikly,
Stripe étendu, PayPal) est résolu**, ne figure plus dans cette liste (cf. module 4).

1. Barème de pénalité en cas de pièce manquante ou cassée (modules 7, 9).
2. Politique d'annulation (module 7).
3. Montant/barème de la pénalité de retard de retour — le déclencheur (jour calendaire +
   tolérance ~30 min) est désormais connu, seul le montant reste ouvert (modules 7, 9).
4. Délai minimum entre réservation et retrait (modules 2, 5).
5. Gestion du client suivant en cas de non-retour dans les temps (modules 1, 2).
6. Locations simultanées par client — oui/non (module 3).
7. Pré-autorisation de la caution en cas de paiement TPE sur place (module 4).
8. Nombre de photos par set à afficher — la proposition initiale (3-4) est contredite par
   l'exemple fourni (12 photos pour un set) ; à réclarifier (module 1).
9. Blocage de dates à l'avance sur le planning par Marion, vacances/indisponibilité
   (module 2).
10. Battement à appliquer (ou non) lors d'une prolongation immédiate du même client, sans
    retour physique du set entre les deux réservations (module 2).
11. Nature du "chat" mentionné par la cliente comme canal de contact : outil existant à
    référencer, ou fonctionnalité à développer (hors devis actuel si c'est le cas) ? (module 2).
12. Bon cadeau = valeur exacte d'une durée, ou crédit complétable par un autre moyen de
    paiement (module 6).
13. Solde restant d'un bon cadeau partiellement utilisé : conservé ou perdu (module 6).
14. Bon cadeau nominatif ou utilisable par toute personne détenant le code (module 6).
15. Durée de validité légale du bon cadeau — minimum 1 an, valeur exacte à définir (module 6).
16. Remboursement/annulation d'un bon cadeau non utilisé (module 6).
17. Réutilisation du système de bons cadeaux pour émettre des avoirs gratuits en cas de
    litige (lien avec le point 5) (module 6).

## Récapitulatif des points reportés en V2 (🔜)

1. Tranche horaire précise sélectionnable dans le tunnel de réservation (module 2) — la
   cliente envisage de l'ajouter plus tard, "pas dans l'immédiat". En V1, l'heure se négocie
   hors tunnel (chat/téléphone/email).
2. Export comptable complet (modules 8, 11) — l'export simple des ventes par année civile,
   lui, est inclus en V1 (module 11).

**Résolu, ne figure plus en V2 :** la prolongation d'une location en cours ne nécessite
finalement aucun développement dédié — elle se fait via une nouvelle réservation standard
dans le tunnel après accord manuel avec la cliente (cf. module 2).
