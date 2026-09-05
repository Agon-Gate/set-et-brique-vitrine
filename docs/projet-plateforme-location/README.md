# Projet plateforme de location — Set et Brique

Contexte : Set et Brique (Marion Lange-Berteaux, EI) loue des sets LEGO à des particuliers,
en remise en main propre uniquement. La gestion des locations se fait aujourd'hui via
**Poppins**, un outil tiers mal adapté à la location longue durée. Ce projet vise à
reprendre la main sur **set-et-brique.com** en intégrant directement réservation, paiement
et gestion des locations, pour progressivement se passer de Poppins.

## Documents de référence

- [devis-set-et-brique.docx](devis-set-et-brique.docx) / [.md](devis-set-et-brique.md) —
  devis détaillé, 12 modules, 5 520 € au total, 2 options de règlement.
- [contrat-set-et-brique.docx](contrat-set-et-brique.docx) / [.md](contrat-set-et-brique.md) —
  contrat de prestation AGON-GATE ↔ Set et Brique (projet de contrat, à faire relire par un
  juriste avant signature).
- [questions-rdv-vendredi.docx](questions-rdv-vendredi.docx) / [.md](questions-rdv-vendredi.md) —
  notes brutes du cadrage : questions posées + réponses de la cliente lors du RDV.
- [questions-recap-a-envoyer.docx](questions-recap-a-envoyer.docx) — version nettoyée à
  envoyer par mail à la cliente, avec les points encore ouverts mis en évidence.

## Grille tarifaire (confirmée)

Montants fixes, identiques pour tous les sets (pas de variation selon la valeur/rareté du set) :

| Durée | Prix |
|---|---|
| 4 jours | 10 € |
| 7 jours | 15 € |
| 15 jours | 25 € |
| 1 mois | 45 € |

> Info transmise par la cliente après le RDV de cadrage (hors notes brutes du RDV).

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

## Financier / juridique — points d'attention

- Devis total : **5 520 €**. Option B (échelonnée) : 10 % à la commande (552 €) + abonnement
  mensuel 6 € → 60 €/mois, engagement 12 mois.
- Le contrat est explicitement marqué comme un **projet de contrat à faire relire par un
  juriste/comptable** avant signature (PI, responsabilité, résiliation, préavis).
- Article 9 du contrat (préavis de reconduction tacite) contient un champ `[à définir]` non
  rempli.
