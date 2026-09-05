# Projet plateforme de location — Set et Brique

Contexte : Set et Brique (Marion Lange-Berteaux, EI) loue des sets LEGO à des particuliers,
en remise en main propre uniquement. La gestion des locations se fait aujourd'hui via
**Poppins**, un outil tiers mal adapté à la location longue durée. Ce projet vise à
reprendre la main sur **set-et-brique.com** en intégrant directement réservation, paiement
et gestion des locations, pour progressivement se passer de Poppins.

## Documents de référence

- [devis-set-et-brique.docx](devis-set-et-brique.docx) / [.md](devis-set-et-brique.md) —
  devis détaillé, 11 modules, 5 520 € au total, 2 options de règlement. Ordre de
  développement revu (voir ci-dessous) : module 11 "Espace client" d'origine fusionné dans
  le module 3 "Comptes clients".
- [contrat-set-et-brique.docx](contrat-set-et-brique.docx) / [.md](contrat-set-et-brique.md) —
  contrat de prestation AGON-GATE ↔ Set et Brique (projet de contrat, à faire relire par un
  juriste avant signature).
- [questions-rdv-vendredi.docx](questions-rdv-vendredi.docx) / [.md](questions-rdv-vendredi.md) —
  notes brutes du cadrage : questions posées + réponses de la cliente lors du RDV.
- [questions-recap-a-envoyer.docx](questions-recap-a-envoyer.docx) — version nettoyée à
  envoyer par mail à la cliente, avec les points encore ouverts mis en évidence.
- [specification-fonctionnelle.md](specification-fonctionnelle.md) — document technique
  métier pour Alexis & Madus : règles métier, statuts et cas limites par module, à jour des
  échanges de cadrage. Référence principale pour développer.

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
6. Contrat de location / conditions générales (300 €)
7. Facturation (420 €)
8. État des lieux & gestion des dommages (450 €)
9. Notifications automatisées (300 €)
10. Back-office & reporting (450 €)
11. Intégration, tests, déploiement, formation (360 €)

## Périmètre V1 (confirmé)

- Remise en main propre uniquement, pas de livraison (extension livraison possible plus tard).
- Plusieurs lieux de retrait dès la V1.
- Compte client obligatoire (pas de mode invité).
- Notifications par email uniquement (pas de SMS).
- Pas de TVA (micro-entreprise, franchise en base).
- Caution = prix neuf du set, libérée automatiquement 48h après confirmation du bon retour.
- Pas d'export catalogue/historique depuis Poppins possible → ressaisie manuelle.

## Points encore ouverts (bloquants pour certains modules)

Ces points ne sont pas tranchés côté cliente — cf. section "Points à trancher" du récap
envoyé. Ne pas démarrer le développement des modules concernés (caution / planning / gestion
des dommages) tant qu'ils ne sont pas clarifiés :

1. Barème de pénalité en cas de pièce manquante ou cassée.
2. Politique d'annulation (remboursement total/partiel/aucun selon délai).
3. Pénalité de retard de retour (automatique sur la caution, ou au cas par cas).
4. Délai minimum entre réservation et retrait.
5. Gestion d'un chevauchement de réservation quand un client ne rend pas le set à temps
   (avoir, bon cadeau, remboursement pour le client suivant lésé).
6. Un client peut-il avoir plusieurs locations en cours en même temps (plusieurs sets
   réservés simultanément), ou une seule location active à la fois par client ?

## Reporté à une V2 (hors périmètre actuel)

- **Prolongation d'une location en cours** : un client doit pouvoir demander à prolonger
  avant de rendre son set. Confirmé par Marion comme souhaité, mais explicitement reporté à
  une V2 — question non tranchée à traiter à ce moment-là : que faire si un autre client a
  déjà réservé ce set juste après (comment refuser/renégocier sans le léser) ? Ne pas
  développer cette fonctionnalité en V1.
- **Export comptable** (module 7 Facturation / module 10 Back-office) : format et fréquence
  d'export non définis pour l'instant (pas d'outil de comptabilité précis côté cliente à ce
  jour). À revoir en V2.

## Financier / juridique — points d'attention

- Devis total : **5 520 €**. Option B (échelonnée) : 10 % à la commande (552 €) + abonnement
  mensuel 6 € → 60 €/mois, engagement 12 mois.
- Le contrat est explicitement marqué comme un **projet de contrat à faire relire par un
  juriste/comptable** avant signature (PI, responsabilité, résiliation, préavis).
- Article 9 du contrat (préavis de reconduction tacite) contient un champ `[à définir]` non
  rempli.
