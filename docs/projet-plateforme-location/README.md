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
- [precisions-client-bareme-annulation-lieux.pdf](precisions-client-bareme-annulation-lieux.pdf) —
  réponses de la cliente reçues le 11/09 (2ᵉ document) : barème de pénalité détaillé,
  séquence de relance en cas de retard, politique d'annulation, lieux de retrait réels,
  validation manuelle des demandes de réservation.

## ⚠️ Grille tarifaire — en cours de changement, ne pas développer dessus pour l'instant

**La cliente a annoncé changer son mode de prix : 2 €/jour, pour tous les sets, sans
distinction.** Ça remplace potentiellement la grille à 4 paliers fixes ci-dessous — mais les
détails ne sont pas encore clairs (est-ce que les 4 durées fixes du tunnel restent, avec un
prix recalculé en 2 €×jours par palier ? Ou la durée devient libre, en nombre de jours ?).
Alexis doit encore y réfléchir avant qu'on pose la question précise à Marion. **Ne pas
modifier le devis, les bons cadeaux (module 6) ou le tunnel (module 5) tant que ce point
n'est pas éclairci** — cf. [points encore ouverts](#points-encore-ouverts-bloquants-pour-certains-modules).

Ancienne grille (montants fixes, identiques pour tous les sets), pour référence tant que le
nouveau modèle n'est pas confirmé :

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
  l'heure de remise se négocie ensuite directement avec la cliente (téléphone/email en V1 ;
  un outil de chat est souhaité par la cliente mais explicitement pas développé pour
  l'instant, éventuellement en V2).
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

## Barème, annulation, retard, lieux — résolu (2ᵉ document du 11/09)

**Barème de pénalité (pièces manquantes/cassées, module 9) — résolu :**

| % de pertes | Retenue sur caution |
|---|---|
| 0 à 5 % | Aucune |
| 5 à 10 % | 10 € |
| 10 à 15 % | 30 € |
| 15 à 20 % | 50 € |
| > 20 % | Prix du set neuf (référence **Bricklink**) |
| Figurine manquante | Prix (référence Bricklink) |
| Notice manquante | Prix (référence Bricklink) |
| Set non restitué | Prix du set (référence Bricklink) |

- **Forfait démontage** : 20 € si le client rapporte le set assemblé/"en vrac" au lieu de le
  redémonter dans les sachets d'origine.
- ⏳ Reste à préciser : comment le "% de pertes" est-il calculé concrètement — à partir de
  l'écart de poids constaté (module 1/9), ou d'un comptage précis des pièces manquantes ?

**Séquence de retard de retour (modules 9, 10) — résolu, remplace l'ancienne règle "rappel
48h avant + relance quotidienne" :**
- Email automatique à **J-1**, **J** (jour de fin de location), **J+1** et **J+2**.
- **J+1** : forfait de retard de **30 €**.
- **J+2** : prélèvement de la **caution entière**.
- Marion veut pouvoir **stopper manuellement cette procédure** depuis le back-office si elle
  parvient à joindre le client et obtient une explication — à prévoir comme action
  disponible sur chaque réservation en retard.

**Restitution de la caution (module 4/9) — précision :**
- Automatique 48h après réception du set, **avec un plafond de 7 jours maximum** dans tous
  les cas (ne dépend donc pas uniquement de la rapidité de Marion à faire l'état des lieux).

**Politique d'annulation (module 7) — résolu :**
- Annulation par le client : gratuite si > 7 jours avant le retrait ; 50 % du prix de la
  location retenu entre 7 jours et 48h ; 100 % retenu à moins de 48h.
- Annulation à l'initiative de Set et Brique : proposition d'un changement de date
  (gratuit), ou avoir sur un autre set disponible, ou remboursement — au choix.

**Lieux de retrait (module 1/2) — liste réelle confirmée :**
- Parking de l'aire de covoiturage de Lanester (à côté du McDo)
- Parking de covoiturage de Guidel
- Parking de covoiturage de Kerizan (Brec'h)
- Parking Intermarché Drive de Monistrol (Lorient)
- Parking de covoiturage de Plouay

**Validation des réservations (module 5) — point majeur à confirmer avec Alexis avant
d'inscrire cette règle dans la spécification :**
- Marion indique : *"pas de délai [minimum de réservation], vu que nous avons la main sur la
  demande de location, nous pouvons oui ou non accepter et proposer d'autres dates."* Cela
  suggère que chaque demande de réservation doit être **validée manuellement par elle**
  (accepter / refuser / proposer une autre date) plutôt que confirmée automatiquement et
  instantanément par le tunnel, contrairement à ce qui était prévu jusqu'ici (module 5 :
  "réservation confirmée dès que le paiement est effectué"). ⏳ À confirmer avec Alexis :
  cela implique-t-il vraiment une validation manuelle systématique ? Si oui, le paiement
  est-il pris avant ou après cette validation, et que se passe-t-il en cas de refus d'une
  réservation déjà payée ?

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
- Bons vendus aux montants de la grille tarifaire, pas de montant libre — ⚠️ montants exacts
  (actuellement 10 € / 15 € / 25 € / 45 €) à revoir selon le nouveau modèle tarifaire en
  cours de clarification (cf. points encore ouverts, item 1).
- Génération automatique d'un code unique par bon, envoyé par email à l'achat.
- Utilisable comme moyen de paiement dans le tunnel de réservation, au même titre que
  Stripe/TPE.
- Suivi des bons (valide / utilisé / expiré) depuis le back-office.

**Encore à trancher** (cf. section "Points encore ouverts" ci-dessous, items 10 à 15).

## Points encore ouverts (bloquants pour certains modules)

Ces points ne sont pas tranchés côté cliente — cf. section "Points à trancher" du récap
envoyé. Ne pas démarrer le développement des modules concernés tant qu'ils ne sont pas
clarifiés :

1. **⚠️ Nouveau modèle tarifaire (2 €/jour, tous les sets)** — Alexis doit d'abord clarifier
   en interne comment ça s'articule avec les 4 durées fixes actuelles avant qu'on formule la
   question précise à poser à Marion. Bloque : la grille tarifaire, les montants des bons
   cadeaux (module 6), potentiellement le fonctionnement du tunnel (module 5) si la durée
   devient libre plutôt que fixée à 4 paliers.
2. **Validation manuelle des réservations** : Marion valide-t-elle vraiment chaque demande
   une par une (accepter/refuser/proposer une autre date) ? Si oui, le paiement est-il pris
   avant ou après cette validation ? — à trancher avec Alexis avant de demander à Marion.
3. Comment le "% de pertes" du barème de pénalité est-il calculé (écart de poids constaté,
   ou comptage précis des pièces manquantes) ? — c'est Set et Brique qui détermine cette
   méthode, à leur demander explicitement.
4. Gestion d'un chevauchement de réservation quand un client ne rend pas le set à temps
   (avoir, bon cadeau, remboursement pour le client suivant lésé).
5. Un client peut-il avoir plusieurs locations en cours en même temps (plusieurs sets
   réservés simultanément), ou une seule location active à la fois par client ?
6. Si un client paie sur place par TPE, faut-il quand même enregistrer sa carte en amont
   pour la pré-autorisation de la caution, ou la caution est-elle prise directement sur
   place via le TPE ?
7. Combien de photos par set afficher sur la fiche produit ? On proposait 3 à 4 par défaut,
   mais l'exemple fourni (Faucon Millénium) contient 12 photos — faut-il toutes les afficher,
   ou nous laisse-t-elle choisir ?
8. Marion doit-elle pouvoir bloquer des dates à l'avance sur le planning (vacances,
   indisponibilité générale), indépendamment du statut de chaque set ? (Ceci dit, si chaque
   réservation est validée manuellement — point 2 — cette question devient peut-être sans
   objet : elle peut simplement refuser une demande sur ses dates d'indisponibilité.)
9. **Battement lors d'une prolongation immédiate — exemple concret pour clarifier la
   question** : un client loue le Faucon Millénium du 1er au 8, puis décide de le garder plus
   longtemps et réserve lui-même, via le tunnel, une nouvelle location du 9 au 15 *pour ce
   même set qu'il a déjà en main* (il ne le rend jamais entre les deux réservations). Le
   délai de battement standard (4 jours par défaut, pour que Marion nettoie/vérifie le set
   entre deux locataires) n'a ici aucune raison d'exister puisque rien n'est physiquement
   rendu. Question : le système doit-il quand même imposer ce délai entre les deux
   réservations du même client (ce qui bloquerait cette prolongation en pratique), ou faut-il
   le sauter spécifiquement dans ce cas ?
10. Un bon cadeau doit-il correspondre exactement à une des 4 durées (ex. bon de 15 €
    utilisable uniquement pour une location de 7 jours), ou est-ce un crédit utilisable pour
    compléter un paiement plus important avec un autre moyen de paiement pour la différence ?
    **À reconsidérer une fois le nouveau modèle tarifaire (point 1) clarifié** — si la durée
    devient libre en jours, la logique "bon = une des 4 durées" ne tient plus telle quelle.
11. Si le montant du bon dépasse le prix de la location choisie, le solde restant est-il
    conservé pour une prochaine location, ou perdu ?
12. Un bon cadeau est-il nominatif (lié à un compte client), ou utilisable par toute personne
    détenant le code (cas classique du cadeau) ?
13. Durée de validité d'un bon cadeau — la loi française impose une durée minimale d'1 an
    pour les bons d'achat non alimentaires (loi Chatel). Quelle durée exacte Marion
    souhaite-t-elle (1 an, 2 ans, illimité) ?
14. Un bon cadeau acheté et non utilisé peut-il être remboursé/annulé par le client ?
15. Le même système de bons peut-il aussi servir à émettre des avoirs gratuits (offerts par
    Marion, sans achat) pour le point 4 ci-dessus (client lésé en cas de non-retour) ? — la
    réponse est probablement oui vu la politique d'annulation ci-dessus ("avoir sur un autre
    set"), mais à confirmer explicitement.

**Résolus par le 2ᵉ document du 11/09** (retirés de cette liste) : barème de pénalité
pièce manquante/cassée, montant de la pénalité de retard, politique d'annulation, lieux de
retrait, délai minimum entre réservation et retrait (remplacé par le point 2 ci-dessus, plus
structurant). **Résolu (réponse d'Alexis) :** nature du "chat" — souhaité par la cliente mais
pas développé pour l'instant, cf. section V2 ci-dessous.

## Reporté à une V2 (hors périmètre actuel)

- **Outil de chat** : la cliente souhaite un chat pour être contactée (prise d'horaire,
  demandes diverses), mais **ne sera pas développé en V1**, éventuellement en V2 (confirmé
  par Alexis). En V1, contact par téléphone/email uniquement.
- **Tranche horaire précise dans le tunnel de réservation** : la cliente envisage elle-même
  de l'ajouter plus tard ("pas dans l'immédiat"). En V1, le tunnel ne réserve qu'une date ;
  l'horaire de remise se négocie hors outil (téléphone/email).
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
