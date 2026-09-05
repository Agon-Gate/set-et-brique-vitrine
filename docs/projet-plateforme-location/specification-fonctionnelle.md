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
- ⏳ En attente d'une réponse de la cliente (Marion) — cf. [questions-recap-a-envoyer.docx](questions-recap-a-envoyer.docx).
- 🔜 Reporté à une V2, ne pas développer maintenant.

---

## 1. Location pure — catalogue & stock

**Fiche article (par set) — champs :**
- ✅ Nom, référence LEGO officielle, nombre de pièces, âge conseillé, photo(s), état
  (neuf/occasion), valeur de remplacement (sert de base à la caution).
- ✅ Champ commentaire libre, **visible côté client** sur la fiche du set.

**Stock :**
- ✅ Un seul exemplaire par référence aujourd'hui (~25 références au catalogue, ajout moyen
  de 0,5/mois), mais le modèle de données doit prévoir le multi-stock (plusieurs exemplaires
  d'une même référence) pour une évolution future — ne pas coder en dur "quantité = 1".

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
  1 mois) — pas de durée libre dans le tunnel. Un besoin différent passe par une demande de
  contact par email, hors outil.
- 🔜 **Prolongation d'une location en cours** : souhaitée par Marion mais reportée en V2. Le
  cas du conflit avec une réservation déjà prise juste après (comment refuser/renégocier
  sans léser le client suivant) reste à trancher à ce moment-là.

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

**Caution :**
- ✅ Toujours en **pré-autorisation**, jamais débitée sauf litige.
- ⚠️ Point à vérifier avec Marion : si le loyer est payé par TPE sur place, comment se fait
  la pré-autorisation de la caution (carte enregistrée en amont côté client tout de même) ?
  Pas encore posé — à ajouter aux questions si le sujet n'est pas déjà couvert.

**Hors périmètre :**
- Pas d'option d'assurance/protection additionnelle — la caution est la seule protection.

**Cas d'échec / abandon :**
- ✅ Échec du paiement en ligne (carte refusée) → créneau bloqué **1h** le temps que le
  client trouve une solution.
- ✅ Abandon en cours de paiement (avant tentative) → créneau libéré après **15 min**.

---

## 5. Tunnel de réservation client

- ✅ Parcours : sélection du set → choix de la durée (4 options fixes) → choix du créneau de
  retrait → paiement (en ligne ou "à la remise par TPE").
- ✅ En V1, tous les sets sont disponibles à tous les lieux de retrait (pas de restriction
  géographique par set).

---

## 6. Contrat de location / conditions générales

- ✅ Acceptation par **simple case à cocher** ("J'accepte les conditions générales") — pas de
  signature électronique nominative en V1.
- ✅ Le document physique remis dans la boîte lors du prêt (avertissement d'âge conseillé)
  est **géré indépendamment par Marion**, hors système — rien à générer côté plateforme.
- ⏳ Le contenu réel des CGL dépend des barèmes non tranchés (pénalités, annulation, retard)
  — **ne pas démarrer ce module avant réponse de Marion**.

---

## 7. Facturation

- ✅ Nouvelle numérotation de factures, **repart de zéro** (pas de reprise de Poppins ou
  d'une séquence existante).
- ✅ Facture générée uniquement une fois le paiement confirmé :
  - automatiquement pour un paiement en ligne ;
  - après que Marion ait marqué manuellement la réservation "payée" sur le site, pour un
    paiement TPE sur place.
  - **Jamais de facture avant paiement effectif.**
- 🔜 Export comptable (format, fréquence) : non défini, Marion n'a pas d'outil de
  comptabilité précis à ce jour — reporté en V2.

---

## 8. État des lieux & gestion des dommages

**Processus de retour :**
- ✅ Le client dépose physiquement le set, sans déclaration formelle à faire (il peut
  signaler un souci à l'oral, ce n'est pas obligatoire).
- ✅ Marion réalise l'état des lieux **plus tard**, généralement le soir même — c'est elle
  seule qui valide si le set est rentré en bon état.
- ✅ Cette validation déclenche le compte à rebours des 48h avant libération automatique de
  la caution (cf. module 4 / règle déjà actée).

**Barème de pénalité :**
- ⏳ Non tranché (pièce manquante/cassée) — **ne pas développer les prélèvements
  automatiques sur caution avant réponse de Marion**.

---

## 9. Notifications automatisées

- ✅ Canal : email uniquement en V1 (pas de SMS).
- ✅ Rappel avant retour : envoyé **48h avant** la date de retour prévue.
- ✅ Relance en cas de retard : démarre **le lendemain** de la date de retour dépassée (vers
  10h), puis **se répète chaque jour** tant que le set n'est pas rendu.
- ✅ Textes des emails rédigés par défaut par AGON-GATE, mais **Marion doit pouvoir les
  modifier elle-même** — prévoir un éditeur de templates dans le back-office.

---

## 10. Back-office & reporting

- ✅ Deux comptes d'accès en V1 : Alexis (admin), Marion (compte propriétaire). Pas d'autre
  utilisateur prévu pour l'instant.
- ✅ Tableau de bord : chiffre d'affaires du mois, taux d'occupation par set, sets les plus
  loués.
- 🔜 Export de données : reporté en V2 (même dépendance que l'export comptable du module 7).

---

## 11. Intégration, tests, déploiement, formation

- ✅ Formation destinée à Marion uniquement.
- ✅ Format : documentation écrite + sessions en présentiel chez elle (pas de visio prévue).

---

## Récapitulatif des points bloquants (⏳ en attente de Marion)

Cf. [README.md](README.md) pour le détail — ne pas démarrer les modules 6 et 8 tant que les
barèmes ne sont pas connus :

1. Barème de pénalité en cas de pièce manquante ou cassée (modules 6, 8).
2. Politique d'annulation (module 6).
3. Pénalité de retard de retour (modules 6, 8).
4. Délai minimum entre réservation et retrait (modules 2, 5).
5. Gestion du client suivant en cas de non-retour dans les temps (modules 1, 2).
6. Locations simultanées par client — oui/non (module 3).

## Récapitulatif des points reportés en V2 (🔜)

1. Prolongation d'une location en cours (module 2).
2. Export comptable / export de données (modules 7, 10).
