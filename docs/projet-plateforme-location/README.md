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

## Grille tarifaire — nouveau modèle confirmé (2 €/jour, durée libre)

**Résolu (réponse de Marion du 13/09).** Le tarif passe à **2 €/jour, pour tous les sets,
sans distinction**, et remplace complètement l'ancienne grille à 4 paliers fixes. Le client
**choisit librement son nombre de jours de location, sans blocage ni durée maximale** — le
principe des "4 durées fixes" du tunnel (4j/7j/15j/1mois) n'existe plus.

**Prolongation en cours de location — mécanique confirmée :** si le client veut garder le set
plus longtemps, il peut faire une nouvelle demande (avec nouveau paiement et nouvelle
caution) **sans avoir besoin de bloquer un nouveau RDV de remise en main propre**, puisqu'il a
déjà le set en sa possession. Cela répond directement à la question du "battement lors d'une
prolongation immédiate" (ex. Faucon Millénium loué du 1er au 8, puis reloué par le même
client du 9 au 15 sans jamais rendre le set entre les deux) : **pas de RDV, donc pas de
battement/nettoyage à imposer entre les deux réservations d'un même client sur le même set**.

Impact sur les modules : le sélecteur de durée du tunnel (module 5) devient un simple nombre
de jours (prix = 2 € × nombre de jours) au lieu d'un choix parmi 4 options ; le module 2
(planning) n'a plus de règle de "durée fixe" à appliquer ; les montants des bons cadeaux
(module 6) sont recalculés sur cette base (cf. section dédiée ci-dessous).

Ancienne grille (obsolète, conservée pour mémoire) :

| Durée | Prix |
|---|---|
| 4 jours | 10 € |
| 7 jours | 15 € |
| 15 jours | 25 € |
| 1 mois | 45 € |

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
- Pas de durée maximale ni minimale : le client choisit librement son nombre de jours de
  location (facturé 2 €/jour, cf. grille tarifaire ci-dessus). Une prolongation en cours de
  location se fait via une nouvelle réservation dans le tunnel (nouveau paiement + nouvelle
  caution), **sans nouveau RDV de remise en main propre** puisque le client a déjà le set en
  main — aucun développement dédié nécessaire au-delà du tunnel standard.

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
- ✅ **Calcul du "% de pertes" — résolu (réponse de Marion du 13/09) :** comptage précis du
  nombre de pièces manquantes par rapport au set de départ (pas un calcul par écart de poids).
  Une **zone de commentaire libre** doit être prévue en plus, pour que Marion documente le
  détail au moment de l'état des lieux (module 9).

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

**Validation des réservations (module 5) — résolu (réponse de Marion du 13/09) :**
- ✅ **Confirmé** : validation manuelle de Marion pour **toutes** les locations **et** les
  prolongations, sans exception. Chaque demande passe par une étape d'approbation côté
  back-office (accepter / refuser / proposer une autre date) avant confirmation définitive —
  ce n'est donc pas une confirmation automatique et instantanée dès le paiement.
- ⏳ Reste à préciser (résidu, à poser à Marion) : le paiement est-il pris **avant** ou
  **après** cette validation manuelle ? Et que se passe-t-il en cas de refus d'une réservation
  dont le paiement aurait déjà été prélevé (remboursement automatique) ?

## Réponses de Marion du 13/09 — résolu (dernier lot de questions)

- **Photos par set** : ✅ 10 photos maximum (au lieu des 3-4 proposées initialement).
- **Client suivant en cas de non-retour dans les temps** : ✅ prévenu par email **et**
  téléphone (Marion l'appelle elle-même), puis indemnisé selon son choix : **avoir ou
  remboursement**.
- **Locations simultanées** : ✅ un client peut louer plusieurs sets en même temps, mais cela
  doit passer par **deux réservations distinctes** (pas un panier multi-articles unique).
- **Blocage de dates sur le planning** : ✅ les deux mécanismes sont nécessaires — par **set**
  (indisponible pour cause de retard, problème technique ou non-restitution) et par
  **période** (congés de Marion, indépendamment du statut de chaque set).
- **Horaires de remise/récupération** : ✅ confirmé, pas d'horaires fixes en V1 — tout se
  négocie en direct avec le client par email et/ou téléphone.

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

**Résolu (réponse de Marion du 13/09) — fonctionnement complet du module :**
- ✅ Montants fixes proposés à l'achat : **10 € / 20 € / 30 €** (remplacent les anciens
  10/15/25/45 € qui étaient calés sur les 4 durées fixes, abandonnées avec le nouveau modèle
  tarifaire). Ces montants correspondent respectivement à 5, 10 et 15 jours de location au
  tarif de 2 €/jour, mais le bon reste un **crédit en euros**, pas une durée figée : le client
  choisit n'importe quel set sur le site pour ce montant (ou plus, avec un complément de
  règlement par carte).
- ✅ Utilisation : au moment de réserver un set, le client voit le prix total de sa location
  (2 € × nombre de jours) et **saisit son code cadeau**, qui est **défalqué du prix** — pas de
  sélection de durée imposée par le bon.
- ✅ **Complément de règlement possible par carte** si le montant du bon ne couvre pas la
  totalité du prix de la location choisie.
- ✅ **Le bon cadeau n'inclut jamais la caution** : la caution reste toujours due séparément
  (pré-autorisation Stripe ou TPE), quel que soit le montant du bon utilisé.
- ✅ **Usage unique** : si le bon vaut plus que le prix de la location réglée, le solde restant
  est **perdu** (pas de report sur une prochaine location).
- ✅ **Non nominatif** : utilisable par toute personne détenant le code (pas de lien avec un
  compte client en particulier).
- ✅ **Durée de validité : 1 an** (conforme au minimum légal de la loi Chatel).
- ✅ **Non remboursable/non annulable** une fois acheté, s'il n'est pas utilisé.
- ✅ **Réutilisation du système pour émettre des avoirs gratuits** (offerts par Marion, sans
  achat, ex. pour compenser un client lésé par un retard de retour — cf. section "Réponses de
  Marion du 13/09" ci-dessus) : confirmé, avec un montant égal à **la valeur de la durée de
  la location initiale** du client concerné.
- ✅ Génération automatique d'un code unique par bon, envoyé par email à l'achat (ou à
  l'émission, pour un avoir gratuit).
- ✅ Utilisable comme moyen de paiement dans le tunnel de réservation, au même titre que
  Stripe/TPE.
- ✅ Suivi des bons (valide / utilisé / expiré) depuis le back-office.

Module entièrement tranché, plus aucun point ouvert sur les bons cadeaux.

## Points encore ouverts (bloquants pour certains modules)

Le lot de réponses de Marion du 13/09 a résolu la quasi-totalité des points qui restaient en
suspens (cf. section "Réponses de Marion du 13/09" ci-dessus et le module 6 pour le détail).
Il ne reste que deux points :

1. Si un client paie sur place par TPE, faut-il quand même enregistrer sa carte en amont
   pour la pré-autorisation de la caution, ou la caution est-elle prise directement sur
   place via le TPE ? (module 4)
2. Résidu de la validation manuelle des réservations (module 5) : le paiement est-il pris
   **avant** ou **après** la validation manuelle de Marion ? Et que se passe-t-il en cas de
   refus d'une réservation dont le paiement aurait déjà été prélevé (remboursement
   automatique) ?

**Résolus par le 2ᵉ document du 11/09** : barème de pénalité pièce manquante/cassée, montant
de la pénalité de retard, politique d'annulation, lieux de retrait. **Résolu (réponse
d'Alexis) :** nature du "chat" — souhaité par la cliente mais pas développé pour l'instant,
cf. section V2 ci-dessous. **Résolus par les réponses de Marion du 13/09** : nouveau modèle
tarifaire (2 €/jour, durée libre), validation manuelle des réservations (flux confirmé),
calcul du "% de pertes" (comptage précis + commentaire libre), gestion du client suivant lésé
(avoir/remboursement au choix), locations simultanées (deux réservations distinctes), nombre
de photos par set (10 max), blocage de dates sur le planning (par set et par période),
battement lors d'une prolongation immédiate (pas de RDV donc pas de battement), et
l'intégralité du fonctionnement des bons cadeaux (montants, usage unique, non-nominatif,
validité 1 an, non-remboursable, réutilisation pour avoirs gratuits).

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
