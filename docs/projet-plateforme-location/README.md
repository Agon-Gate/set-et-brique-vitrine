# Projet plateforme de location — Set et Brique

Contexte : Set et Brique (Marion Lange-Berteaux, EI) loue des sets LEGO à des particuliers,
en remise en main propre uniquement. La gestion des locations se fait aujourd'hui via
**Poppins**, un outil tiers mal adapté à la location longue durée. Ce projet vise à
reprendre la main sur **set-et-brique.com** en intégrant directement réservation, paiement
et gestion des locations, pour progressivement se passer de Poppins.

## Documents de référence

- [devis-set-et-brique.docx](devis-set-et-brique.docx) / [.md](devis-set-et-brique.md) —
  devis détaillé, 12 modules, 5 520 € au total, 2 options de règlement. Ordre de
  développement revu (voir ci-dessous) : module "Espace client" d'origine fusionné dans le
  module 3 "Comptes clients" ; module 6 "Bons cadeaux" ajouté en cours de route (inclus,
  sans surcoût).
- [contrat-set-et-brique.docx](contrat-set-et-brique.docx) / [.md](contrat-set-et-brique.md) —
  contrat de prestation AGON-GATE ↔ Set et Brique (projet de contrat, à faire relire par un
  juriste avant signature).
- [questions-rdv-vendredi.docx](questions-rdv-vendredi.docx) / [.md](questions-rdv-vendredi.md) —
  notes brutes du cadrage : questions posées + réponses de la cliente lors du RDV.
- [questions-recap-a-envoyer.docx](questions-recap-a-envoyer.docx) — version nettoyée à
  envoyer par mail à la cliente, avec les points encore ouverts mis en évidence.
- [specification-fonctionnelle.md](specification-fonctionnelle.md) / [.docx](specification-fonctionnelle.docx) —
  document technique métier pour Alexis & Madus : règles métier, statuts et cas limites par
  module, à jour des échanges de cadrage. Référence principale pour développer.
- [catalogue-sets-lego.md](catalogue-sets-lego.md) / [.xlsx](catalogue-sets-lego.xlsx) —
  catalogue complet des 28 sets actuellement loués (titre, référence(s), caution, etc.),
  fourni par la cliente.
- [precisions-client-caution-catalogue.pdf](precisions-client-caution-catalogue.pdf) —
  réponses de la cliente reçues le 11/09 : caution/assurance au-delà de 7 jours, remise en
  main propre, comptage des jours de location, champs produit détaillés.

## Grille tarifaire (confirmée)

Montants fixes, identiques pour tous les sets (pas de variation selon la valeur/rareté du set) :

| Durée | Prix |
|---|---|
| 4 jours | 10 € |
| 7 jours | 15 € |
| 15 jours | 25 € |
| 1 mois | 45 € |

> Info transmise par la cliente après le RDV de cadrage (hors notes brutes du RDV).

## Ordre de développement des modules (revu)

Tarification devenue fixe (voir grille ci-dessus) → la "tarification dégressive" a été
retirée du module 1. Le module "Espace client en ligne" (ex-module 11) a été fusionné dans
le module 3 "Comptes clients" : le client suit désormais l'avancement de ses locations en
cours (prochaine date de retrait, set concerné, statut récupéré / à rendre, date de retour,
retard éventuel). Total inchangé à 5 520 € (le prix de l'ancien module 11 a été absorbé dans
le module 3, qui passe de 540 € à 660 €).

1. Location pure — catalogue & stock (900 €)
2. Planning des locations (660 €)
3. Comptes clients & espace de suivi (660 €)
4. Paiement Stripe (540 €)
5. Tunnel de réservation client (480 €)
6. Bons cadeaux (Inclus — voir section dédiée ci-dessous)
7. Contrat de location / conditions générales (300 €)
8. Facturation (420 €)
9. État des lieux & gestion des dommages (450 €)
10. Notifications automatisées (300 €)
11. Back-office & reporting (450 €)
12. Intégration, tests, déploiement, formation (360 €)

## Périmètre V1 (confirmé)

- Remise en main propre uniquement, pas de livraison (extension livraison possible plus tard).
- Plusieurs lieux de retrait dès la V1.
- Compte client obligatoire (pas de mode invité).
- Notifications par email uniquement (pas de SMS).
- Pas de TVA (micro-entreprise, franchise en base).
- Caution = prix neuf du set (100 € à 650 € selon le set, cf. catalogue), en pré-autorisation
  standard Stripe (7 jours), libérée automatiquement 48h après confirmation du bon retour.
- Pas d'export catalogue/historique depuis Poppins possible → ressaisie manuelle.
- **28 références au catalogue** (mise à jour du chiffre "~25" estimé au RDV — cf.
  [catalogue-sets-lego.md](catalogue-sets-lego.md)).
- Le tunnel de réservation ne fait réserver qu'une **date**, pas un créneau horaire précis —
  l'heure de remise se négocie ensuite directement avec la cliente (téléphone/chat/email).
- La durée de location se compte en **jours calendaires** (une location de 4 jours démarrée
  un mardi se termine le vendredi, quelle que soit l'heure de remise/retour ce jour-là), avec
  une tolérance d'environ 30 min le jour du retour avant que ça compte comme un retard.
- Pas de durée maximale de location : au-delà des 4 durées du tunnel, la cliente gère les
  prolongations manuellement (contact, puis nouvelle réservation standard pour les jours
  supplémentaires) — aucun développement dédié nécessaire pour ce cas.

## Caution au-delà de 7 jours — sujet résolu

Un hold bancaire (pré-autorisation) ne peut pas dépasser 7 jours de façon fiable — ce qui
posait problème pour les locations de 15 jours ou 1 mois (cf. l'exploration Swikly / Stripe
autorisation étendue / PayPal menée précédemment). **La cliente a tranché elle-même** :
Stripe reste en pré-autorisation standard (7 jours), et **elle prend une assurance
professionnelle de son côté** pour couvrir le risque au-delà. Aucune intégration
supplémentaire (Swikly, PayPal, autorisation étendue Stripe) n'est nécessaire.

## Précisions techniques (remontées de Madus, tranchées)

- **Poids du set** : non affiché côté client — information volontairement privée pour éviter
  que les locataires ne devinent le contenu exact des sachets pesés (Set et Brique sépare le
  poids des sachets de pièces de celui de la notice et des autres accessoires). Le poids de
  référence reste stocké en interne, utile pour la vérification au retour (module 8).
- **Contrat de location automatique** : confirmé, un contrat personnalisé (PDF) est généré à
  chaque réservation avec les mentions légales, le set, la durée, les dates et le montant —
  répercuté dans le devis (module 7, Contrat de location). Prix du module inchangé (300 €),
  le développement supplémentaire est absorbé dans le forfait.
- **Export des ventes par année civile** : ajouté en V1 (module 11, Back-office), même sans
  export comptable complet. Modèle de données : une **vente** est un nœud entre un **set**,
  une **durée** et un **client** — c'est cet élément qui doit être interrogeable en base pour
  produire cet export.

## Module 6 — Bons cadeaux (nouveau, ajouté en cours de cadrage)

Demande remontée par la cliente, absente du devis initial. Module dédié, ajouté sans faire
bouger le prix des autres modules ni le total (5 520 €) — listé "Inclus" dans le devis,
présenté comme rendu possible grâce à la réutilisation des modules Paiement Stripe (4) et
Tunnel de réservation (5) déjà prévus.

**Confirmé :**
- Bons vendus aux montants de la grille tarifaire (10 € / 15 € / 25 € / 45 €), pas de montant
  libre.
- Génération automatique d'un code unique par bon, envoyé par email à l'achat.
- Utilisable comme moyen de paiement dans le tunnel de réservation, au même titre que
  Stripe/TPE.
- Suivi des bons (valide / utilisé / expiré) depuis le back-office.

**Encore à trancher** (cf. section "Points encore ouverts" ci-dessous, items 12 à 17).

## Points encore ouverts (bloquants pour certains modules)

Ces points ne sont pas tranchés côté cliente — cf. section "Points à trancher" du récap
envoyé. Ne pas démarrer le développement des modules concernés (caution / planning / gestion
des dommages) tant qu'ils ne sont pas clarifiés :

1. Barème de pénalité en cas de pièce manquante ou cassée.
2. Politique d'annulation (remboursement total/partiel/aucun selon délai).
3. Montant/barème de la pénalité de retard de retour (le déclencheur est désormais connu :
   jour calendaire de fin de location + tolérance ~30 min, cf. ci-dessus — seul le montant
   reste à définir).
4. Délai minimum entre réservation et retrait.
5. Gestion d'un chevauchement de réservation quand un client ne rend pas le set à temps
   (avoir, bon cadeau, remboursement pour le client suivant lésé).
6. Un client peut-il avoir plusieurs locations en cours en même temps (plusieurs sets
   réservés simultanément), ou une seule location active à la fois par client ?
7. Si un client paie sur place par TPE, faut-il quand même enregistrer sa carte en amont
   pour la pré-autorisation de la caution, ou la caution est-elle prise directement sur
   place via le TPE ?
8. Combien de photos par set afficher sur la fiche produit ? On proposait 3 à 4 par défaut,
   mais l'exemple fourni (Faucon Millénium) contient 12 photos — faut-il toutes les afficher,
   ou nous laisse-t-elle choisir ?
9. Marion doit-elle pouvoir bloquer des dates à l'avance sur le planning (vacances,
   indisponibilité générale), indépendamment du statut de chaque set ? — remontée par Madus
   en lien avec le délai minimum de réservation (point 4).
10. Lors d'une prolongation immédiate (nouvelle réservation du même client à la suite de la
    première, sans retour physique du set), le délai de battement standard s'applique-t-il
    quand même, ou faut-il pouvoir le sauter dans ce cas précis ?
11. Le "chat" mentionné par la cliente comme canal de contact (prise d'horaire, demande de
    prolongation) — est-ce un outil déjà existant côté cliente à simplement référencer sur le
    site, ou une fonctionnalité de chat en direct à développer (hors devis actuel si c'est le
    cas) ?
12. Un bon cadeau doit-il correspondre exactement à une des 4 durées (ex. bon de 15 €
    utilisable uniquement pour une location de 7 jours), ou est-ce un crédit utilisable pour
    compléter un paiement plus important avec un autre moyen de paiement pour la différence ?
13. Si le montant du bon dépasse le prix de la location choisie, le solde restant est-il
    conservé pour une prochaine location, ou perdu ?
14. Un bon cadeau est-il nominatif (lié à un compte client), ou utilisable par toute personne
    détenant le code (cas classique du cadeau) ?
15. Durée de validité d'un bon cadeau — la loi française impose une durée minimale d'1 an
    pour les bons d'achat non alimentaires (loi Chatel). Quelle durée exacte Marion
    souhaite-t-elle (1 an, 2 ans, illimité) ?
16. Un bon cadeau acheté et non utilisé peut-il être remboursé/annulé par le client ?
17. Le même système de bons peut-il aussi servir à émettre des avoirs gratuits (offerts par
    Marion, sans achat) pour le point 5 ci-dessus (client lésé en cas de non-retour) ?

## Reporté à une V2 (hors périmètre actuel)

- **Tranche horaire précise dans le tunnel de réservation** : la cliente envisage elle-même
  de l'ajouter plus tard ("pas dans l'immédiat"). En V1, le tunnel ne réserve qu'une date ;
  l'horaire de remise se négocie hors outil (chat/téléphone/email).
- **Export comptable complet** (format lié à un outil de comptabilité) : non défini, Marion
  n'a pas d'outil précis à ce jour. Reste hors périmètre V1. Une version allégée est en
  revanche incluse en V1 (module 11) : export simple des ventes par année civile.

**Résolu, ne figure plus en V2 :** la prolongation d'une location en cours ne nécessite
finalement aucun développement dédié — le client contacte Set et Brique pour vérifier la
disponibilité, puis effectue lui-même une nouvelle réservation standard dans le tunnel pour
les jours supplémentaires.

## Financier / juridique — points d'attention

- Devis total : **5 520 €**. Option B (échelonnée) : 10 % à la commande (552 €) + abonnement
  mensuel 6 € → 60 €/mois, engagement 12 mois.
- Le contrat est explicitement marqué comme un **projet de contrat à faire relire par un
  juriste/comptable** avant signature (PI, responsabilité, résiliation, préavis).
- Article 9 du contrat (préavis de reconduction tacite) contient un champ `[à définir]` non
  rempli.
