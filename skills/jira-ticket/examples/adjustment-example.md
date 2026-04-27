# Example: Adjustment ticket

A worked example of an adjustment ticket in the format the skill produces, on a **neutral domain** (e-commerce, order confirmation email). The same shape and discipline applies to any product domain. Domain-specific names below (`PROJ`, *commande*, *centre logistique*, etc.) are illustrative.

This example shows the **two-block output**: Block A is paste-ready in Jira; Block B stays in the chat as the workspace until questions are closed.

---

## Sources of truth used by the skill (inputs)

When the skill produced this ticket, the requester provided these citations.

- **Related Jira** PROJ-1234 (courriel de confirmation de commande, copie initiale et template documentés)
- **PO message** (dated), confirmed UI copy verbatim, format de date par langue, position dans le courriel
- **Product meeting note** (dated), driver: « informer le client au moment où il peut planifier la réception »

---

## Block A: TICKET (à coller dans Jira)

### Ticket metadata

- **Summary:** `Ajouter une section d'expédition au courriel de confirmation de commande`
- **Type:** Task
- **Parent epic:** PROJ-1180 (Epic: Order communication improvements)
- **Labels:** `Email`, `Orders`
- **Priority:** Medium
- **Assignee:** [Engineer name]
- **Reporter:** [PO name]
- **Sprint:** [Sprint name]
- **Due date:** [date]
- **Story points:** 1
- **Linked tickets:** `Relates to` PROJ-1234 (courriel existant de confirmation de commande)
- **Attachments:** none

### Contexte

Présentement, le courriel de confirmation de commande contient uniquement le récapitulatif des articles et le total (voir PROJ-1234 pour l'implémentation initiale). Nous ajoutons une section dans le même courriel qui annonce la date d'expédition prévue dès que le centre logistique a programmé l'envoi. Objectif : informer le client au moment où il peut planifier la réception, pas seulement à l'achat. *(driver cité d'une réunion produit, dated.)* Affecte tous les clients ayant passé une commande livrable.

### Comportement souhaité

- **Scénario, Section d'expédition dans le courriel de confirmation**
  - Étant donné qu'une commande livrable a été planifiée pour expédition
  - Et que la date d'expédition est connue
  - Quand le courriel de confirmation de commande est généré
  - Alors le courriel contient une section « Expédition prévue le [date] » sous le récapitulatif des articles
  - Et la date est formatée selon la langue du destinataire (voir bullet ci-dessous)

- **Scénario, Commande sans date d'expédition connue**
  - Étant donné qu'une commande livrable n'a pas encore de date d'expédition planifiée
  - Quand le courriel de confirmation est généré
  - Alors la section d'expédition affiche « Expédition prévue : à confirmer sous 24h » à la place de la date
  - Et le reste du courriel reste identique au comportement actuel

- **Scénario, Commande non-livrable (téléchargement numérique, billet)**
  - Étant donné qu'une commande ne nécessite pas d'expédition physique
  - Quand le courriel de confirmation est généré
  - Alors la nouvelle section n'apparaît pas dans le courriel
  - Et le courriel reste identique au comportement actuel

- Copie de la section : `Expédition prévue le [date]` *(verbatim, source : PO)*
- Copie de la section sans date : `Expédition prévue : à confirmer sous 24h` *(verbatim, source : PO)*
- Format de la date : `JJ/MM/AAAA` en français, `MM/DD/YYYY` en anglais, `DD/MM/YYYY` dans les autres langues. *(règle, source : PO, voir Q1 pour les autres langues supportées)*
- Position dans le courriel : sous le récapitulatif des articles, au-dessus du total.
- Audience : tous les destinataires du courriel de confirmation pour une commande livrable.
- **Inchangé** : le récapitulatif des articles, le total, et le pied de page du courriel restent identiques au comportement actuel.
- **Inchangé** : les courriels pour les commandes non-livrables (téléchargement numérique, billet d'événement) ne sont pas affectés.
- **Inchangé** : le déclenchement et la cadence d'envoi du courriel de confirmation ne changent pas, seule la composition du contenu est modifiée.

*(Pas de section Design, le changement est dans le template de courriel et les copies sont fournies verbatim. Si une maquette du courriel modifié existe, elle peut être ajoutée en pièce jointe.)*

---

## Block B: Questions à régler (workspace PO, **ne pas coller dans Jira**)

**Pour la PO**

- **Q1**: *(débloque Comportement souhaité, format de date)* Quelles autres langues sont supportées par le courriel de confirmation aujourd'hui ? Le format `DD/MM/YYYY` couvre-t-il toutes les langues non-FR/EN, ou y a-t-il des exceptions à documenter ?

**Pour Ingénieur**

- **Q2**: *(débloque Comportement souhaité, distinction livrable / non-livrable)* Comment le système identifie-t-il aujourd'hui qu'une commande est livrable vs. non-livrable ? Confirmer le mécanisme (citer depuis le code ou PROJ-1234) pour que le scénario du courriel utilise la même règle.

**Validation INVEST, flags surfacés**

Aucun. Les six critères passent avec preuves citables :

- **I, Independent** : Livrable seul ; relie PROJ-1234 pour contexte mais n'attend rien de lui.
- **N, Negotiable** : Le Comportement souhaité décrit les conditions et le contenu observables ; le mécanisme de récupération de la date d'expédition reste au choix de l'ingénieur.
- **V, Valuable** : Le client connaît la date d'expédition au moment de l'achat, réduction des demandes au support « quand vais-je recevoir ma commande ? ».
- **E, Estimable** : Q1 et Q2 ouvertes mais aucune ne bloque l'estimation initiale.
- **S, Small** : Une seule modification du courriel, périmètre clair.
- **T, Testable** : Trois scénarios en GWT, copie verbatim avec sources, formats de date énumérés, inchangés explicites.

---

## Why this ticket works well

- **Summary commence par un verbe de changement** (« Ajouter »), signale un ajustement, pas une feature nouvelle.
- **Contexte est un état du monde**: pas une user story. Il ancre contre ce qui existe et lie le ticket frère PROJ-1234 pour que l'engineer trouve le code existant.
- **Driver cité** : « informer le client au moment où il peut planifier la réception », sourcé d'une vraie conversation, pas inventé.
- **Audience qualifiée** : « tous les clients ayant passé une commande livrable ».
- **Trois scénarios distincts** couvrent le cas principal, le cas sans date connue, et le cas non-livrable, Rule 4 (états et bords).
- **Copie verbatim avec source citée**: l'engineer voit qui l'a confirmée. Anti-fabrication respectée.
- **Format de date énuméré par langue**: règle non-comportementale en bullet plate, pas en GWT (Rule 12).
- **« Inchangé » explicite** pour trois zones adjacentes, anti-régression claire pour reviewers et QA.
- **Pas de section Design**: le changement n'est pas visuel au sens classique. Correctement omis (pas de heading vide).
- **Block A est paste-ready, Block B porte les questions ouvertes**: Rule 15.
- **INVEST dans Block B uniquement**: pas de section parasite dans Block A.

## Where it could be even stronger

- Q1 (autres langues supportées) devrait être fermée avant la création, c'est de la copie UI multilingue, le risque de manquer une langue ou un format coûte plus cher que l'attente.
- Q2 (distinction livrable / non-livrable) est une règle déjà existante dans le système, la PO devrait pouvoir la citer depuis PROJ-1234 ou la doc. Si elle ne sait pas, c'est un signal qu'une rétrospective sur la documentation est utile.
- Le ticket n'a pas de critère de mesure (combien de courriels par jour ? taux d'erreur acceptable si la date d'expédition est manquante ?). Pour une feature à plus haut volume, ce serait à ajouter en Section optionnelle « Info supplémentaire ».
