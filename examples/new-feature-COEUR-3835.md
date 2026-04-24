# Example — New feature ticket (COEUR-3835)

This is a cleaned, annotated version of a real new-feature ticket from the Groupe Jolicoeur (COEUR) project. Use it as a reference for style and structure.

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
- **Attachments:** 1 screenshot of the modal design

---

## Description (as filed)

### Contexte

En tant que courtier, je veux pouvoir activer un abonnement afin de recevoir automatiquement des opportunités selon le volume journalier ~~hebdomadaire~~ choisi.

### Critères d'acceptation

- Le modal s'ouvre via le bouton « Activer l'abonnement » sur la tuile abonnement
- Le modal affiche une description expliquant que les leads sont reçues automatiquement selon leur disponibilité et que les montants sont déduits à chaque transaction pour les leads entrants seulement
- Le courtier peut choisir parmi 4 options de volume journalier ~~hebdomadaire~~ : 5, 10, 20, 40 ~~ou 100~~ demandes par jour ~~par mois~~, chacune affichant le coût maximum associé
  - Ajout : ou le courtier peut mettre un montant custom via un champ numérique: il n'y a pas de max ni min.
- Une seule option peut être sélectionnée à la fois
- Le modal contient un bouton « Annuler » et un bouton « Activer »
  - Annuler ferme la modale.
- Si aucune carte de crédit n'est enregistrée dans Stripe, le courtier est redirigé vers une page de setup de Stripe (même behavior que pour les campagnes)
  - L'abonnement débute à partir du moment où l'information est activée dans Stripe et non au clic du bouton de la modale dans le portail.
- En cas d'échec lors de l'activation, un message d'erreur est affiché et l'abonnement n'est pas créé
- En cas de succès, le modal de confirmation s'affiche (voir autre ticket)
- L'abonnement peut être désactivé à tout moment sans pénalité, seules les transactions complétées sont facturées.
  - Si le volume mensuel choisi n'est pas atteint, le solde de leads potentiels ne s'accumulent pas.
- L'abonnement peut être modifié en cours de route, on affiche la modale qui permet de modifier le nb de leads par jour

### Design (updaté)

[Screenshot embedded from Capture d'écran, le 2026-04-22 à 11.37.19.png]

https://www.figma.com/design/rSahrZ7TR5HEBnvVWFL5zS/Espace-Courtiers?m=auto&node-id=41586-106433&t=fUgeieN9KveMLcjA-1

---

## Why this ticket works well

- **Summary is a noun phrase** that names the specific artifact ("Modal Activer l'abonnement"), not a goal or verb.
- **Contexte is a real user story** with a clearly stated role (courtier), goal (activer un abonnement), and benefit (recevoir automatiquement des opportunités).
- **ACs cover all the important paths:** trigger, content, selection constraint, cancel, success redirect (Stripe setup), error, success (confirmation modal), later edits, later cancel.
- **UI strings are in quotation marks** ("Activer l'abonnement", "Annuler", "Activer") — engineers know what text to render.
- **Cross-ticket reference** for the confirmation modal (`voir autre ticket`) keeps this ticket focused.
- **Strike-through for revisions** shows reviewers what changed (`~~hebdomadaire~~`, `~~ou 100~~`, `~~par mois~~`) without losing the history.
- **Design section** has both the screenshot and the Figma link with node-id — engineers can preview in Jira and drill into Figma for specifics.

## Where it could be even stronger

- No explicit **loading state** AC ("Activer" is clicked → what does the button do while the request is in flight?).
- **"Montant custom... pas de max ni min"** is risky — a real upper bound would protect against typos. Worth a clarifying question to the PO during a real drafting session.
- The sub-bullet `Si le volume mensuel choisi n'est pas atteint` mentions "volume mensuel" after earlier ACs say the volume is daily — inconsistency worth flagging.
