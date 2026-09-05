# Questions à poser à Set et Brique — RDV vendredi (notes brutes)

Objectif : lever les inconnues avant de démarrer le développement, pour ne pas devoir revoir
l'architecture en cours de route.

> Notes de cadrage internes, questions + réponses de la cliente capturées pendant/après le
> RDV. Version nettoyée pour envoi au client : [questions-recap-a-envoyer.docx](questions-recap-a-envoyer.docx).

## 1. Métier / fonctionnement actuel

- **Comment fixe-t-il le prix de location ? Uniquement par durée, ou aussi selon la
  valeur/rareté du set ?**
  → Ce sont des montants fixes.
- **Possède-t-il plusieurs exemplaires du même set ?** (structurant pour le modèle de stock)
  → Non, un exemplaire de chaque set.
- **Combien de références (sets) a-t-il aujourd'hui en catalogue ? Combien en ajoute-t-il en
  moyenne par mois ?**
  → Environ 25 en tout, ajout d'1/2 par mois dans la durée.
- **Gère-t-il un temps de battement entre deux locations (nettoyage, vérification) ou un set
  peut-il repartir immédiatement ?**
  → Oui, actuellement 4 jours, mais ce délai doit pouvoir être réglé par set.
- **A-t-il des créneaux fixes de remise en main propre (jours/horaires précis) ou du
  sur-mesure au cas par cas ?**
  → Oui, heure et lieu définis.
- **Combien de locations traite-t-il par mois actuellement, en moyenne ?** (sert à
  dimensionner l'infra et le back-office)
  → Entre 5 et 10.

## 2. Spécifique produit — contrôle du matériel

- **Comment vérifie-t-il qu'un set est complet au retour ? Pesée, checklist pièce par pièce,
  photo, autre ?**
  → Il faut peser chaque sachet de pièces déterminé par couleurs et vérifier l'état des
  figurines/personnages et des manuels de montage (officiels).
- **A-t-il déjà un barème de pénalité en cas de pièce manquante ou cassée, ou faut-il le
  définir ensemble ?**
  → **À définir exactement.** ⚠️ ouvert
- **Les accessoires (notice, boîte d'origine, sachets numérotés) sont-ils aussi vérifiés et
  facturables en cas de perte ?**
  → Oui.
- **Y a-t-il des sets qu'il retire temporairement de la location (réparation, nettoyage,
  réservés en interne) ?**
  → Oui : réparation, complétion de pièces, ou client n'ayant pas rendu à temps.
  **Problème concret non résolu** ⚠️ : si un client loue un set du 01 au 08 mais ne le
  retourne pas, et qu'un autre client l'a réservé à partir du 13, comment gérer ce
  client suivant ? (avoir, bon cadeau, remboursement, etc.)

## 3. Juridique / fiscal — impact direct sur la facturation

- **Est-il assujetti à la TVA, ou en franchise en base ?**
  → Pas de TVA.
- **Quel est le statut juridique de son entreprise ?**
  → Micro-entreprise actuellement.
- **A-t-il une assurance professionnelle couvrant la location de matériel à des tiers ?**
  → Non, tout est géré par la caution (fixée par set, initialement au prix neuf du set).
- **A-t-il des CGV/CGL de location déjà rédigées, ou faut-il l'orienter vers un
  comptable/juriste ?**
  → Elles n'existent pas, à créer par Set et Brique.
- **Comment gère-t-il aujourd'hui juridiquement la caution ?**
  → Géré par Poppins, à voir pour intégrer cette notion dans la fiche de chaque set + dans
  le document de Conditions Générales de Prêt.
- **Y a-t-il un âge minimum requis pour louer, ou une pièce d'identité à fournir ?**
  → Pas d'âge requis ni de contrôle, mais un avertissement sur la fiche du set (8 ans /
  10 ans, etc.) + un document dans la boîte lors du prêt.

## 3bis. Politique de réservation, annulation et caution

- **Quelle politique d'annulation souhaite-t-il ?** (remboursement total/partiel/aucun selon
  le délai avant la date de retrait)
  → **À définir.** ⚠️ ouvert
- **Restitution de la caution : libération automatique après un délai sans signalement de
  dommage, ou toujours validée manuellement ?**
  → Automatique, 48h après confirmation de la bonne réception du set.
- **Retard de retour : pénalité automatique, ou traité au cas par cas ?**
  → **À définir.** ⚠️ ouvert
- **Délai minimum entre la réservation et le retrait ?**
  → **Délai à définir.** ⚠️ ouvert
- **Réservation possible sans création de compte ("mode invité"), ou compte obligatoire ?**
  → Compte obligatoire.

## 4. Technique

- **A-t-il déjà un compte Stripe, ou faut-il le créer avec lui ?**
  → Déjà un compte.
- **Peut-il exporter son catalogue et son historique clients depuis Poppins ?**
  → Non, ressaisie complète nécessaire.
- **Utilise-t-il d'autres outils à interfacer plus tard (comptabilité, mailing, réseaux
  sociaux) ?**
  → Non, pas en V1.
- **A-t-il une préférence sur l'outil de comptabilité qui recevra les exports de factures ?**
  → Non, pas en V1.
- **Notifications par email uniquement, ou aussi par SMS ?**
  → Uniquement par mail pour la V1.
- **Souhaite-t-il gérer plusieurs lieux de remise en main propre dès la V1 ?**
  → Plusieurs lieux.
- **Un article en cours de réservation doit-il être masqué ou grisé dans le catalogue ?**
  → Non, il reste visible sans grisé ; les dates d'indisponibilité apparaissent dans le
  planning de la fiche article.
- **Quels sont ses objectifs à moyen/long terme ?**
  → Court terme : faire grossir le nombre de locations, à partir du site, toujours en
  remise en main propre. Moyen terme : envoi dans toute la France.

## 5. Cadrage du projet

- **Confirme-t-il le périmètre V1 : uniquement remise en main propre, pas de livraison ?**
  → Oui.
- **Sur les modules du devis, y a-t-il une priorité différente, ou un module à repousser ?**
  → Non.
- **Est-il disponible pour des points d'étape réguliers pour valider chaque module livré ?**
  → Oui.

## Récapitulatif des points encore ouverts (⚠️)

1. Barème de pénalité pour pièce manquante ou cassée.
2. Politique d'annulation.
3. Pénalité de retard de retour.
4. Délai minimum entre réservation et retrait.
5. Gestion du client suivant en cas de non-retour dans les temps (chevauchement de
   réservation).
