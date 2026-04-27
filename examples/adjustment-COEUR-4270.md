# Example — Adjustment ticket (COEUR-4270)

An annotated reference ticket in the adjustment format. This is a **cleaned and upgraded** version of a real ticket from the Groupe Jolicoeur (COEUR) project — it shows the ticket as it would look after the current skill drafts and reviews it.

Use it as the canonical style reference for adjustments: contextual framing, GWT for behavioural change, explicit "what stays unchanged", INVEST self-check.

---

## Ticket metadata (as filed)

- **Key:** COEUR-4270
- **Summary:** `Ajouter une notification lorsqu'un lead d'abonnement est assigné dans les opportunités potentielles`
- **Type:** Task
- **Parent:** COEUR-3447 (Epic: WallStreet 2.0)
- **Labels:** `BrokerPortal`, `Notifications`
- **Priority:** Medium
- **Assignee:** guillaume baud
- **Reporter:** Justine Crépeau-Viau
- **Sprint:** COUCOU À MON TOUR
- **Due date:** 2026-04-27
- **Story points:** 1
- **Linked tickets:** `Relates to` COEUR-3202 (notification existante « nouvelle demande web »)
- **Attachments:** none

---

## Description (as filed)

### Contexte

Présentement, la notification « Nouvelle demande web ajoutée » se déclenche lorsqu'une nouvelle demande arrive dans la boutique (voir COEUR-3202 pour l'implémentation initiale). Nous ajoutons une deuxième notification qui se déclenche lorsqu'un lead d'abonnement est assigné dans les opportunités potentielles du courtier. Objectif : informer le courtier au moment où il peut agir sur le lead, pas seulement à l'arrivée de la demande côté boutique. Affecte les courtiers ayant le flag `BrokerShop` activé.

### Comportement souhaité

#### Scénarios de changement

**Scénario — Notification au courtier lors de l'assignation d'un lead d'abonnement**
- Étant donné qu'un courtier a le flag `BrokerShop` activé
- Et que ses préférences de notification sont à leur valeur par défaut (portail et SMS activés)
- Quand un lead d'abonnement lui est assigné dans les opportunités potentielles
- Alors une notification avec la copie « Nouveau prospect de la boutique reçu — consultez votre portail. » apparaît dans le panneau de notifications du portail
- Et un SMS avec la même copie est envoyé au numéro enregistré du courtier
- Et la notification apparaît dans le tableau des opportunités potentielles avec un badge « Nouveau »

**Scénario — Courtier ayant désactivé les notifications SMS**
- Étant donné qu'un courtier a désactivé les SMS dans ses préférences
- Quand un lead d'abonnement lui est assigné
- Alors seule la notification portail est envoyée
- Et aucun SMS n'est transmis

**Scénario — Échec d'envoi SMS côté fournisseur**
- Étant donné qu'une notification SMS doit être envoyée
- Quand le fournisseur SMS retourne une erreur
- Alors la notification portail est livrée normalement
- Et l'échec SMS est loggé avec `notification_sms_failed` pour monitoring, sans retry automatique pour ce premier release

#### Règles, copie et ce qui ne change pas

- Copie portail : « Nouveau prospect de la boutique reçu — consultez votre portail. »
- Copie SMS : identique à la copie portail
- Par défaut à la livraison du ticket : notification portail **et** SMS activés pour tous les courtiers `BrokerShop` existants (migration one-shot)
- Les courtiers peuvent désactiver individuellement l'une ou l'autre dans leurs préférences de notification
- Audience : courtiers avec flag `BrokerShop` activé uniquement
- **Inchangé** : la notification « Nouvelle demande web ajoutée » (COEUR-3202) continue de fonctionner sans modification
- **Inchangé** : les règles d'assignation des leads ne sont pas modifiées — ce ticket porte uniquement sur la notification de l'assignation
- **Inchangé** : la mise en page du tableau des opportunités potentielles, hormis l'ajout du badge « Nouveau » cité en scénario

### Validation INVEST

- **I — Independent** : Livrable seul ; relie COEUR-3202 pour contexte mais n'attend rien de lui. Aucun autre ticket ne doit être livré d'abord.
- **N — Negotiable** : Le Comportement souhaité décrit les événements déclencheurs et les outputs observables ; le choix du canal SMS, de la file d'envoi, et de la stratégie de déduplication reste à l'ingénieur.
- **V — Valuable** : Un courtier reçoit un signal au moment où il peut agir sur un lead assigné, pas seulement quand il arrive en boutique — réduction du délai entre assignation et prise en charge.
- **E — Estimable** : Aucun TODO ; la copie est en verbatim, les défauts sont spécifiés, la stratégie en cas d'échec SMS est décidée (pas de retry).
- **S — Small** : Une seule notification ajoutée. Les autres sources de notification ne sont pas touchées.
- **T — Testable** : Trois scénarios en GWT couvrant le happy path, la préférence utilisateur, et l'échec du fournisseur ; copie verbatim ; audience précise ; inchangés explicites.

---

## Why this ticket works well

- **Summary commence par un verbe de changement** (« Ajouter ») — signale un ajustement, pas une feature nouvelle.
- **Contexte est un état du monde**, pas une user story. Il ancre contre ce qui existe (la notification « nouvelle demande web ») et lie le ticket frère (COEUR-3202) pour que l'engineer trouve le code existant.
- **Driver nommé** : « informer le courtier au moment où il peut agir » — pas juste « on ajoute une notification ».
- **Audience qualifiée** : `flag BrokerShop activé`, pas « les courtiers ».
- **Scénarios en GWT** couvrent happy path, préférence utilisateur modifiée, et échec fournisseur — trois dimensions distinctes, chacune testable.
- **Copie verbatim** en portail et SMS, explicitement identique — l'engineer n'a pas à deviner si les deux canaux partagent la string.
- **« Inchangé » explicite** pour trois zones adjacentes (notification existante, règles d'assignation, mise en page) — anti-régression claire pour reviewers et QA.
- **Stratégie d'échec SMS décidée** (log, pas de retry pour ce release) — évite la question en review.
- **INVEST self-check** rempli avec preuves, pas coches vides.
- **Pas de section Design** — le changement est comportemental. Correctement omis (pas de heading vide).

## Where it could be even stronger

- La copie « badge Nouveau » sur le tableau des opportunités est mentionnée dans le scénario sans apparaître dans les Règles & Copie. Par symétrie, elle devrait être listée verbatim avec la string exacte (« Nouveau », majuscule/minuscule ? accent ? traduction EN ?).
- La migration one-shot qui active les notifications par défaut pour les courtiers `BrokerShop` existants est un effet de bord non-trivial (opt-in vs opt-out rétroactif). Dans un vrai draft, la skill flaggerait cela pour décision produit : est-ce vraiment un opt-out rétroactif, ou un opt-in au prochain login ? La distinction a des implications de communication.
- L'absence de retry SMS est documentée comme « pour ce premier release » — une bonne mention, mais elle mérite un ticket de suivi créé dès maintenant (`Relates to` future work) pour ne pas être oubliée.
