# Example — New feature ticket

A worked example of a new-feature ticket in the format the skill produces, on a **neutral domain** (B2B SaaS team management). The same shape and discipline applies to any product domain — e-commerce, mobile app, internal tools, healthcare. Domain-specific names below (`PROJ`, *Modal Inviter un membre*, *admin d'équipe*, etc.) are illustrative.

This example shows the **two-block output**: Block A is paste-ready in Jira; Block B stays in the chat as the workspace until questions are closed.

---

## Sources of truth used by the skill (inputs)

When the skill produced this ticket, the requester provided these citations. Every backend specific in the ticket below points back to one of them — Rule 14 (anti-fabrication) is satisfied.

- **Notion PRD** *« Self-service team management — v2 »* (link, dated)
- **Figma** *Team Management / Members list* — node-id preserved on the link
- **Related Jira** PROJ-1100 (organisations and roles model — flag and role enum introduced and documented there)
- **PO message** (dated) — confirmed UI copy verbatim, role enum, error behaviour

---

## Block A — TICKET (à coller dans Jira)

### Ticket metadata

- **Summary:** `Modal Inviter un membre`
- **Type:** Task
- **Parent epic:** PROJ-1050 (Epic: Self-service team management)
- **Labels:** `Team`, `Features`
- **Priority:** Medium
- **Assignee:** [Engineer name]
- **Reporter:** [PO name]
- **Sprint:** [Sprint name]
- **Due date:** [date]
- **Story points:** 2
- **Linked tickets:** `Blocks` PROJ-1201 (modal de confirmation post-invitation) · `Relates to` PROJ-1100 (organisations et rôles)
- **Attachments:** 1 screenshot of the modal design

### Contexte

En tant qu'admin d'une équipe de 10+ membres, lorsque je dois ajouter un nouveau collaborateur sans passer par le support, je souhaite l'inviter par courriel avec un rôle pré-sélectionné afin que mon équipe puisse intégrer la personne en quelques minutes plutôt qu'en quelques jours.

**Contraintes**
- L'envoi de courriels passe par le vendeur d'envoi sous contrat (voir PROJ-1100 pour l'intégration initiale).
- L'invitation n'apparaît dans la liste des membres qu'après confirmation d'envoi par le vendeur — pas au clic dans la modale.

### Acceptance criteria

**Préconditions**

- L'admin est authentifié dans le produit.
- L'admin a la permission « inviter un membre » sur l'organisation courante (introduite dans PROJ-1100).
- La liste des membres est visible dans la section « Équipe » du produit.

**Scénarios**

**Scénario — Ouverture du modal**
- Étant donné qu'un admin est sur la liste des membres
- Quand il clique sur le bouton « Inviter un membre »
- Alors le modal « Inviter un membre » s'ouvre
- Et aucun rôle n'est pré-sélectionné

**Scénario — Invitation envoyée à une adresse valide**
- Étant donné qu'un admin a ouvert le modal
- Quand il saisit une adresse courriel valide, sélectionne « Membre standard », et clique sur « Envoyer l'invitation »
- Alors le bouton « Envoyer l'invitation » affiche un spinner et est désactivé
- Et le système met le courriel d'invitation en file d'envoi
- Et dès que le vendeur d'envoi confirme la mise en file, le modal de confirmation s'affiche (voir PROJ-1201)

**Scénario — Adresse courriel invalide**
- Étant donné qu'un admin a saisi une adresse mal formée et cliqué sur « Envoyer l'invitation »
- Quand le système valide le format
- Alors le modal reste ouvert
- Et le message « Adresse courriel invalide. Veuillez vérifier l'orthographe. » s'affiche sous le champ courriel *(verbatim, source : PO)*
- Et aucun courriel n'est mis en file d'envoi
- Et le bouton « Envoyer l'invitation » redevient cliquable

**Scénario — Échec d'envoi côté vendeur**
- Étant donné qu'un admin a cliqué sur « Envoyer l'invitation »
- Quand le vendeur d'envoi retourne une erreur
- Alors le modal reste ouvert
- Et le message « Échec de l'envoi. Veuillez réessayer. » s'affiche sous le bouton *(verbatim, source : PO)*
- Et aucune invitation n'est créée côté produit
- Et le bouton « Envoyer l'invitation » redevient cliquable

**Scénario — Annulation**
- Étant donné qu'un admin a ouvert le modal
- Quand il clique sur « Annuler »
- Alors le modal se ferme
- Et aucun appel n'est fait au vendeur d'envoi
- Et aucune invitation n'est créée

**Règles & contraintes**

- Titre du modal : « Inviter un membre » *(verbatim, source : Notion PRD §2.1)*
- Paragraphe descriptif du modal : « La personne invitée recevra un courriel avec un lien d'inscription valide pendant 7 jours. » *(verbatim, source : Notion PRD §2.1)*
- Rôles offerts : « Admin », « Membre standard », « Lecture seule ». *(source : PROJ-1100 — enum existant)*
- Une seule adresse courriel par invitation (la saisie multiple est explicitement hors-scope, voir Non-inclus).
- Boutons : « Annuler » (secondaire), « Envoyer l'invitation » (primaire). *(verbatim, source : Figma)*

**Effets de bord système**

- À la confirmation de mise en file par le vendeur d'envoi, l'invitation est créée et apparaît dans la liste des membres avec le statut « En attente » (séquence implémentée dans PROJ-1100 — l'engineer reprend le pattern existant pour les rôles).
- Voir Q3 en Block B pour les détails exacts (table, champs, événements émis) — un ingénieur familier du système doit confirmer avant l'implémentation.

### Design

[Screenshot embedded]

[Figma URL with node-id preserved]

### Non-inclus

- L'invitation **multiple** depuis un fichier CSV (voir PROJ-1300, à drafter séparément).
- La personnalisation de la copie du courriel d'invitation par l'admin (voir PROJ-1305).
- Le suivi en temps réel de l'ouverture du courriel par l'invité (hors-scope produit pour ce sprint).

---

## Block B — Questions à régler (workspace PO, **ne pas coller dans Jira**)

**Pour la PO**

- **Q1** — *(débloque Règles & contraintes)* La copie descriptive du modal mentionne « valide pendant 7 jours ». Confirmer la valeur exacte (7 jours ouvrables ? calendaires ? configurable côté admin ?).

**Pour Ingénieur**

- **Q3** — *(débloque Effets de bord système)* Quels effets observables côté backend se produisent à la confirmation de mise en file par le vendeur d'envoi ? Pour chaque effet : sa nature, où il s'observe (donnée modifiée, message émis, comportement déclenché), et comment le vérifier. Citer depuis le code ou la doc — ne rien lister par hypothèse.

**Validation INVEST — flags surfacés**

Aucun. Les six critères passent avec preuves citables :

- **I — Independent** : Livrable seul ; bloque PROJ-1201 (modal de confirmation).
- **N — Negotiable** : L'AC décrit les outcomes observables ; l'implémentation reste au choix de l'ingénieur.
- **V — Valuable** : Un admin invite un membre par lui-même, sans intervention support — gain mesurable en temps d'intégration.
- **E — Estimable** : 2 points (Q3 ne bloque pas l'estimation — le pattern existe dans PROJ-1100).
- **S — Small** : Une seule capacité (invitation simple) ; multi-invitation et personnalisation de copie scindées.
- **T — Testable** : Cinq scénarios en GWT, copie UI verbatim avec sources, audience qualifiée.

---

## Why this ticket works well

- **Persona qualifié** : « admin d'une équipe de 10+ membres » — rôle + segment + contexte du besoin, pas « en tant qu'utilisateur ».
- **Bénéfice mesurable** : « en quelques minutes plutôt qu'en quelques jours » — avant/après vérifiable.
- **Contraintes isolées** : le vendeur d'envoi est dans Contexte (contrainte produit, citée), pas dans l'AC.
- **Tous les scénarios en GWT** : trigger → état → action → résultat observable.
- **Scénarios happy, alternate, error, dismiss** tous couverts (Rule 4).
- **Copie UI verbatim avec source citée** pour le titre, la description, les messages d'erreur, les libellés de bouton (Rule 3 + Rule 14).
- **Anti-fabrication respectée** : la permission « inviter un membre » et l'enum de rôles sont cités depuis PROJ-1100 plutôt qu'invoqués « parce que ça sonne juste ». Les Effets de bord système ne sont pas inventés — la section pointe explicitement vers Q3 pour confirmation par un ingénieur.
- **Scope resserré verticalement** : invitation simple seule, multi-invitation et personnalisation scindées (Rule 13).
- **Block A est paste-ready, Block B reste dans la conversation** — Rule 15.
- **INVEST surface dans Block B**, pas comme section parasite dans Block A.
- **Liens Jira explicites** : `Blocks` PROJ-1201, `Relates to` PROJ-1100.
- **Non-inclus explicite** : trois capacités adjacentes nommées, chacune avec sa référence ou sa raison.

## Where it could be even stronger

- Q1 (durée de validité de l'invitation) devrait être fermée avant la création — c'est une règle métier, le risque d'incohérence avec le système d'authentification coûte plus cher que l'attente.
- Q3 (effets de bord backend) est volontairement laissée ouverte parce que le pattern existe dans PROJ-1100. Si le ticket frère n'existait pas, la PO aurait dû demander à un ingénieur avant la création.
- Le ticket n'a pas de critère de mesure (combien d'invitations envoyées par semaine ? taux de réussite côté vendeur ?). Pour une feature à plus haut volume, ce serait à ajouter en Section optionnelle « Info supplémentaire ».
