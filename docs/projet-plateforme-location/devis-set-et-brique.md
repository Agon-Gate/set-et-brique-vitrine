# DEVIS — Plateforme de location en ligne

**Set et Brique — remplacement de la solution Poppins**
agon-gate.com — Pluvigner, Morbihan

Date du devis : à compléter
Validité de l'offre : 30 jours

## Contexte

Set et Brique gère aujourd'hui ses locations de LEGO via la plateforme Poppins, peu adaptée
à la location longue durée. L'objectif de ce projet est de reprendre la main sur le site
set-et-brique.com en y intégrant directement les fonctionnalités de réservation, de paiement
et de gestion des locations, afin de se passer progressivement de Poppins.

## Détail du développement, module par module

Les modules sont présentés dans l'ordre de mise en place recommandé : les cinq premiers
forment le socle indispensable pour un site fonctionnel, les suivants sécurisent et
complètent l'activité.

| # | Module | Prix |
|---|---|---|
| 1 | Location pure — catalogue & stock | 900 € |
| 2 | Planning des locations | 660 € |
| 3 | Comptes clients & espace de suivi | 660 € |
| 4 | Paiement Stripe | 540 € |
| 5 | Tunnel de réservation client | 480 € |
| 6 | Contrat de location / conditions générales | 300 € |
| 7 | Facturation | 420 € |
| 8 | État des lieux & gestion des dommages | 450 € |
| 9 | Notifications automatisées | 300 € |
| 10 | Back-office & reporting | 450 € |
| 11 | Intégration, tests, déploiement, formation | 360 € |

**Total développement complet : 5 520 €**

### 1. Location pure — catalogue & stock — 900 €
- Fiches articles avec informations détaillées (nom, référence, état, valeur)
- Gestion du stock et des quantités disponibles
- Définition du montant de caution par article
- Suivi des entrées et sorties de matériel

### 2. Planning des locations — 660 €
- Calendrier des périodes de location
- Gestion des créneaux de retrait / retour
- Disponibilité en temps réel des articles

### 3. Comptes clients & espace de suivi — 660 €
- Inscription client (coordonnées, contact)
- Authentification de base (pour être reconnu lors d'une prochaine réservation, sans
  ressaisir ses informations)
- Stockage des informations nécessaires à la facturation
- Historique des locations rattaché à chaque client (consultable depuis le back-office)
- Espace client en ligne : suivi de l'avancement de ses locations en cours (prochaine date
  de retrait, set concerné, statut récupéré / à rendre, date de retour prévue, retard
  éventuel)

### 4. Paiement Stripe — 540 €
- Intégration Stripe pour le règlement des locations
- Gestion de la caution en pré-autorisation (jamais débitée, sauf litige)

### 5. Tunnel de réservation client — 480 €
- Parcours de réservation en ligne : sélection → durée → créneau → paiement
- Intégration directe au site vitrine existant
- Réutilise les composants déjà développés pour le planning et le catalogue (module allégé
  grâce à cette mutualisation)

### 6. Contrat de location / conditions générales — 300 €
- Conditions générales de location intégrées au tunnel de réservation
- Acceptation obligatoire avant validation de la commande

### 7. Facturation — 420 €
- Génération automatique de devis et factures au format PDF
- Numérotation légale
- Export pour la comptabilité

### 8. État des lieux & gestion des dommages — 450 €
- Constat de retour — méthode à confirmer avec le client (photos et/ou checklist)
- Prélèvement partiel ou total sur la caution en cas de dommage ou de pièce manquante

### 9. Notifications automatisées — 300 €
- Email de confirmation de réservation
- Rappel avant la date de retour
- Relance en cas de retard

### 10. Back-office & reporting — 450 €
- Vue d'ensemble des locations en cours
- Suivi du stock et du chiffre d'affaires
- Exports de données

### 11. Intégration, tests, déploiement, formation — 360 €
- Mise en production
- Tests du parcours complet
- Formation à l'utilisation de l'outil

> \* La méthode exacte de constat de retour (module 8) sera précisée avec vous avant le
> démarrage du développement, selon votre fonctionnement actuel.

## Méthode de travail et jalons

Chaque module fonctionnel donnera lieu à un point d'étape : présentation, démonstration et
tests avant de passer au suivant. Objectif indicatif de mise en service : fin octobre — cet
objectif est optimiste et non garanti, le calendrier réel dépendra des difficultés techniques
rencontrées en cours de développement.

## Modalités de règlement

**Option A — Paiement intégral**
5 520 € réglés selon les modalités habituelles (acompte à la commande, solde à la livraison).

**Option B — Règlement échelonné (proposée à Set et Brique)**
- 10 % du montant réglés à la commande, soit 552 €
- Le solde est financé par le passage de l'abonnement mensuel actuel de 6 € à 60 €/mois
- Engagement minimum de 12 mois sur ce nouvel abonnement
- Cette option permet d'étaler l'investissement dans le temps tout en bénéficiant des
  fonctionnalités dès leur développement achevé

*Bon pour accord — Date : _______________ Signature (précédée de la mention « Bon pour
accord ») :*
