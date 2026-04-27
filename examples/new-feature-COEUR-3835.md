# Example — New feature ticket (COEUR-3835)

An annotated reference ticket in the new-feature format. This is a **cleaned and upgraded** version of a real ticket from the Groupe Jolicoeur (COEUR) project — it shows the ticket as it would look after the current skill drafts and reviews it, not a verbatim snapshot of what was originally filed.

Use it as the canonical style reference: persona qualification, GWT scenarios, INVEST self-check.

---

## Ticket metadata (as filed)

- **Key:** COEUR-3835
- **Summary:** `Modal Activer l'abonnement`
- **Type:** Task
- **Parent:** COEUR-3447 (Epic: WallStreet 2.0)
- **Labels:** `BrokerPortal`, `Features`
- **Priority:** Medium
- **Assignee:** Noah Chamberland
- **Reporter:** Justine Crépeau-Viau
- **Sprint:** COUCOU À MON TOUR
- **Due date:** 2026-04-23
- **Story points:** 2
- **Linked tickets:** `Blocks` COEUR-3836 (modal de confirmation) · `Relates to` COEUR-3202 (intégration Stripe courtiers)
- **Attachments:** 1 screenshot of the modal design

---

## Description (as filed)

### Contexte

En tant que courtier abonné à la boutique de leads, lorsque je veux recevoir automatiquement des demandes entrantes selon ma capacité journalière, je veux activer un abonnement avec un volume choisi afin de ne plus avoir à réclamer manuellement chaque opportunité.

**Contraintes**
- Le traitement du paiement et du statut de l'abonnement passe par Stripe (seul processeur sous contrat).
- L'abonnement n'est actif dans le portail qu'après confirmation de Stripe — pas au clic dans la modale.

### Critères d'acceptation

#### Préconditions
- Le courtier est authentifié dans le portail
- Le courtier a le flag `BrokerShop` activé sur son compte
- La tuile « Abonnement » est visible sur l'accueil du portail

#### Scénarios

**Scénario — Ouverture du modal**
- Étant donné qu'un courtier est sur l'accueil du portail
- Quand il clique sur le bouton « Activer l'abonnement » dans la tuile Abonnement
- Alors le modal « Activer l'abonnement » s'ouvre
- Et aucune option n'est pré-sélectionnée

**Scénario — Activation avec volume pré-défini et carte enregistrée**
- Étant donné qu'un courtier a ouvert le modal
- Et qu'une carte de crédit est enregistrée dans son compte Stripe
- Quand il sélectionne l'option « 20 demandes / jour » et clique sur « Activer »
- Alors le bouton « Activer » affiche un spinner et est désactivé
- Et le système envoie la requête d'activation à Stripe
- Et dès que Stripe confirme l'activation, le modal de confirmation s'affiche (voir COEUR-3836)

**Scénario — Activation avec montant personnalisé**
- Étant donné qu'un courtier a ouvert le modal
- Quand il saisit un entier ≥ 1 dans le champ « Montant personnalisé » et clique sur « Activer »
- Alors le reste du flux est identique au scénario ci-dessus

**Scénario — Carte de crédit absente**
- Étant donné qu'un courtier a sélectionné un volume et cliqué sur « Activer »
- Quand aucune carte de crédit n'est enregistrée dans son compte Stripe
- Alors le système le redirige vers la page de setup de Stripe (même comportement que pour les campagnes — voir COEUR-3202)
- Et aucun abonnement n'est créé tant que la carte n'est pas enregistrée

**Scénario — Échec de l'activation côté Stripe**
- Étant donné qu'un courtier a cliqué sur « Activer »
- Quand Stripe retourne une erreur
- Alors le modal reste ouvert
- Et le message « Échec de l'activation. Veuillez réessayer. » s'affiche sous le bouton « Activer »
- Et aucun abonnement n'est créé côté portail
- Et le bouton « Activer » redevient cliquable

**Scénario — Annulation**
- Étant donné qu'un courtier a ouvert le modal
- Quand il clique sur « Annuler »
- Alors le modal se ferme
- Et aucun appel n'est fait à Stripe
- Et aucun abonnement n'est créé

#### Règles & contraintes
- Titre du modal : « Activer l'abonnement »
- Paragraphe descriptif du modal : « Les leads sont reçues automatiquement selon leur disponibilité. Les montants sont déduits à chaque transaction, pour les leads entrants seulement. »
- Volumes pré-définis offerts : 5, 10, 20, 40, 100 demandes / jour — chaque option affiche son coût maximum mensuel associé
- Champ « Montant personnalisé » : entier ≥ 1, pas de borne supérieure (décision produit 2026-04-22, acceptation du risque de typo confirmée par la PO)
- Les options pré-définies et le champ personnalisé sont mutuellement exclusifs : sélectionner l'un désélectionne l'autre
- Boutons : « Annuler » (secondaire), « Activer » (primaire)

#### Effets de bord système
- À la confirmation Stripe, un enregistrement `subscription` est créé avec `status=active`, `daily_volume=<valeur>`, `stripe_subscription_id=<id>`
- Un événement `subscription.activated` est émis pour le pipeline analytics
- Les règles d'assignation automatique des leads pour ce courtier sont activées dans le moteur de distribution dès la confirmation Stripe

### Design (updaté)

[Screenshot embedded from Capture d'écran, le 2026-04-22 à 11.37.19.png]

https://www.figma.com/design/rSahrZ7TR5HEBnvVWFL5zS/Espace-Courtiers?m=auto&node-id=41586-106433&t=fUgeieN9KveMLcjA-1

### Validation INVEST

- **I — Independent** : Livrable seul ; bloque COEUR-3836 (modal de confirmation) qui consomme l'événement `subscription.activated`. Aucune dépendance amont non livrée.
- **N — Negotiable** : L'AC décrit uniquement les outcomes observables (appels Stripe, état du modal, événements émis) ; l'implémentation (library HTTP, gestion d'état du modal, structure exacte de la requête Stripe) reste au choix de l'ingénieur.
- **V — Valuable** : Un courtier abonné à la boutique peut activer son abonnement par lui-même, sans ticket interne ni intervention support — réduction directe du temps jusqu'à la première opportunité automatique.
- **E — Estimable** : Aucun TODO non résolu ; la copie verbatim, les bornes du champ personnalisé, et le comportement Stripe en cas d'échec sont tous explicites. Estimé à 2 points.
- **S — Small** : Une seule capacité (activation). La modification d'un abonnement actif et l'annulation sont scindées dans des tickets séparés.
- **T — Testable** : Six scénarios en GWT, toute copie UI en verbatim, effets de bord système listés pour QA backend.

---

## Why this ticket works well

- **Persona qualifié** : « courtier abonné à la boutique de leads, lorsque je veux recevoir automatiquement des demandes » — rôle + segment + contexte du besoin, pas « en tant qu'utilisateur ».
- **Bénéfice mesurable** : « ne plus avoir à réclamer manuellement chaque opportunité » — avant/après vérifiable, pas circulaire.
- **Contraintes isolées** : Stripe comme seul processeur est dans Contexte (contrainte produit), pas dans l'AC (ce qui laisserait à l'engineer la question « est-ce une contrainte ou une suggestion ? »).
- **Tous les scénarios en GWT** : trigger → état → action → résultat observable. QA peut écrire les cas de test sans suivi.
- **Scénarios happy, alternate happy, error, dismiss** tous couverts — Rule 4 du rubric (états d'erreur, loading, vide).
- **Copie UI verbatim** pour le titre, la description, le message d'erreur, les libellés de bouton — Rule 3.
- **Effets de bord système explicites** (record DB, événement analytics, activation du moteur de distribution) — ce que QA backend doit valider, pas juste ce que l'utilisateur voit.
- **Scope resserré verticalement** : activation seule, modification et annulation scindées. Rule 13 respectée — chaque slice livre une tranche de valeur complète.
- **INVEST self-check rempli** avec preuves concrètes, pas avec des coches vides.
- **Liens Jira** : `Blocks` COEUR-3836, `Relates to` COEUR-3202 — les dépendances sont explicites.

## Where it could be even stronger

- Le coût maximum mensuel par option (« chaque option affiche son coût maximum mensuel associé ») est mentionné mais les valeurs exactes ne sont pas listées. Un PO rigoureux fournirait le tableau : `5 → X$`, `10 → Y$`, etc. Dans une vraie passe, la skill flaggerait `[T — Testable]` et demanderait les valeurs.
- L'absence de borne supérieure sur le montant personnalisé est documentée comme une décision produit — bien — mais la décision reste fragile. Dans un follow-up, un plafond anti-typo (ex. 10 000 $ / jour avec confirmation supplémentaire au-dessus) serait une amélioration sans compromis côté UX.
- Aucun test de « modification en cours de route » ni « annulation » dans ce ticket — ce qui est correct car ils sont scindés, mais les liens vers ces tickets frères devraient apparaître dès qu'ils existent (`Relates to`).
