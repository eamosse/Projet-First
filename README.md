# FoodShare — Projet de synthèse BTS SIO/SLAM

> **Sujet de projet** — 3 semaines, équipes de 4, modules mobilisés : Spring Boot, React, Android (Jetpack Compose / MVVM), Tests logiciels. Usage libre d'agents IA comme assistant de développement.

---

## 1. Contexte et fil rouge

Chaque année, les restaurants universitaires, les cafétérias de campus et les commerces de proximité jettent des quantités importantes d'invendus encore consommables. En parallèle, les étudiants cherchent à réduire leur budget nourriture. **FoodShare** est une plateforme qui met en relation :

- des **offreurs** (resto U, cafétérias, commerces)et£ qui publient leurs invendus du jour à prix réduit ou gratuit,
- des **étudiants consommateurs** qui réservent une portion et viennent la récupérer dans un créneau donné.

La plateforme comprend **trois applications connectées à une API commune** :

- un **back-office web (React)** pour les offreurs (publier / gérer leurs offres),
- une **application mobile Android (Jetpack Compose)** pour les étudiants (découvrir et réserver),
- une **API REST Spring Boot** qui centralise les données et la logique métier.

L'ensemble doit être **testé**, **documenté**, et développé avec l'aide d'agents IA, chaque membre de l'équipe tenant un **journal de bord** de son usage.

---

## 2. Objectifs pédagogiques

Le projet permet à chaque étudiant de mettre en évidence ses acquis dans les quatre modules :

| Module | Ce qui sera démontré |
|---|---|
| **Spring Boot** | Architecture Controller → Service → Repository → Entity ; JPA / base relationnelle ; validation ; gestion d'erreurs ; exposition REST propre |
| **React** | Découpage en composants, hooks (`useState`, `useEffect`, hook custom), gestion d'état local, appels API |
| **Android (Compose + MVVM)** | Composables réutilisables, ViewModel, observation d'état (`StateFlow`/`LiveData`), navigation, appels réseau depuis le ViewModel |
| **Tests logiciels** | Tests unitaires ciblés sur la couche Service et Repository|

**Objectif transverse :** travailler en équipe avec Git, utiliser des agents IA de façon raisonnée, documenter ses choix.

---

## 3. Périmètre fonctionnel

### 3.1. Exigences minimales (MVP — obligatoire)

Le MVP doit être **entièrement fonctionnel de bout en bout** avant toute extension.

**Utilisateurs et authentification**
- Deux types de comptes : `OFFREUR` et `ETUDIANT`.
- Inscription, connexion, session (token simple suffit — JWT ou session HTTP).

**Côté Offreur (web React)**
- Créer une offre : titre, description, quantité disponible, prix (0 € = don), créneau de retrait (début/fin), lieu.
- Consulter la liste de ses offres (actives, passées).
- Voir les réservations reçues pour chacune de ses offres.
- Marquer une réservation comme « retirée » ou « non retirée ».

**Côté Étudiant (mobile Android)**
- Parcourir les offres disponibles du jour (liste triée par créneau de retrait).
- Voir le détail d'une offre.
- Réserver une portion (décrémente la quantité disponible côté serveur).
- Voir ses réservations en cours et passées.

**Côté API Spring Boot**
- Endpoints REST pour toutes les opérations ci-dessus.
- Entités : `Utilisateur`, `Offre`, `Reservation` (au minimum).
- Base relationnelle (H2 en dev, PostgreSQL en bonus).
- Validation des données d'entrée.
- Gestion propre des erreurs (404, 400, 409 si plus de stock, etc.).

### 3.2. Extensions possibles (bonus, à prioriser par l'équipe)

À n'aborder que si le MVP est stable et testé.

- Filtres par lieu ou distance (géolocalisation) sur l'app mobile.
- Notifications push ou email lorsqu'une nouvelle offre est publiée.
- Notation / avis après retrait.
- Tableau de bord offreur avec statistiques (portions sauvées, clients fidèles).
- Catégories de nourriture et allergènes.
- Mode hors-ligne basique côté Android (cache local).
- Pipeline CI GitHub Actions (lint + tests).

**Règle d'or :** un MVP testé et stable vaut mieux qu'un produit riche mais bancal. 

---

## 4. Architecture technique attendue

### 4.1. Schéma global

```
┌──────────────────┐          ┌──────────────────┐
│  React web app   │          │  Android app     │
│  (Offreurs)      │          │  (Étudiants)     │
│                  │          │  Compose + MVVM  │
└────────┬─────────┘          └────────┬─────────┘
         │                             │
         │          HTTP / JSON        │
         │                             │
         └──────────────┬──────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │  Spring Boot API │
              │  Controller →    │
              │  Service →       │
              │  Repository →    │
              │  Entity (JPA)    │
              └────────┬─────────┘
                       │
                       ▼
              ┌──────────────────┐
              │  Base SQL (H2    │
              │  ou PostgreSQL)  │
              └──────────────────┘
```

### 4.2. Contraintes techniques

| Couche | Stack imposée | Points de liberté |
|---|---|---|
| API | Spring Boot 3.x, Spring Data JPA, H2 ou PostgreSQL | Choix du mécanisme d'auth (JWT / session) |
| Web | React 18+, Vite, fetch ou axios | Choix librairie UI (Bootstrap, MUI, Tailwind, ou aucune) |
| Mobile | Android, Kotlin, Jetpack Compose, pattern MVVM | Choix client HTTP (Retrofit recommandé, Ktor accepté) |
| Tests | JUnit 5 (Spring), Jest + React Testing Library (Web), JUnit (Android) | — |
| Versioning | Git + une plateforme (GitHub / GitLab) | — |

### 4.3. Modèle de données minimal

L'équipe est libre d'enrichir, mais doit au minimum modéliser :

- `Utilisateur` (id, email, motDePasse, role `OFFREUR`/`ETUDIANT`, nom, …)
- `Offre` (id, titre, description, quantiteInitiale, quantiteRestante, prix, debutRetrait, finRetrait, lieu, offreurId)
- `Reservation` (id, offreId, etudiantId, dateReservation, statut `EN_ATTENTE`/`RETIREE`/`NON_RETIREE`)

---

## 5. Organisation de l'équipe (4 étudiants)

Le projet est trop large pour qu'une seule personne touche à tout. Chaque étudiant prend un **rôle principal** mais doit contribuer ailleurs (pour l'évaluation individuelle).

| Rôle | Responsabilité principale | Contribution secondaire attendue |
|---|---|---|
| **Back-end lead** | API Spring, entités, services, endpoints | Tests unitaires Spring, modèle de données |
| **Web lead** | App React offreur | Intégration API, tests RTL |
| **Mobile lead** | App Android étudiant (Compose + MVVM) | Tests UI Compose, navigation |
| **Qualité & intégration** | Stratégie de tests, documentation, Git/branches, intégration des 3 apps | Aide sur une des 3 stacks selon les besoins |

**Règles d'équipe**
- Chaque rôle doit livrer du code visible dans Git avec des commits à son nom.
- Chaque membre doit ouvrir et relire au moins **une Pull Request** d'un collègue.
- Une branche `main` stable, des branches `feat/...` par fonctionnalité.

---

## 6. Livrables

À rendre au plus tard la veille de la soutenance :

1. **Dépôt Git** (lien + accès en lecture pour l'évaluateur) contenant les trois projets (`api/`, `web/`, `android/`) et un `README.md` racine expliquant comment lancer l'ensemble.
2. **Dossier de projet** (PDF, 10-15 pages max) :
   - Présentation du projet et de l'équipe, avec rôle de chacun.
   - Modèle de données (schéma MCD/MLD).
   - Choix techniques et justifications.
   - Captures d'écran des trois apps.
   - Stratégie de tests et résultats (couverture).
   - Retour d'expérience sur l'usage des agents IA (synthèse des journaux individuels).
3. **Journaux de bord individuels** (1 par étudiant, 2-3 pages) :
   - Ce que j'ai construit cette semaine.
   - Quand j'ai utilisé un agent IA, comment, pour quoi.
   - Ce que j'ai appris, ce qui m'a bloqué.
4. **Démonstration live** (15 min) + **questions** (10 min) lors de la soutenance.

---

## 7. Critères d'évaluation

La note finale combine une **note de groupe** (sur le produit livré) et une **note individuelle** (sur la contribution et la soutenance).

### 7.1. Note de groupe (sur 60)

| Critère | Points |
|---|---|
| API Spring — architecture Controller/Service/Repository/Entity respectée, code lisible | 10 |
| API Spring — validation, gestion d'erreurs, cohérence REST | 5 |
| Web React — découpage en composants, usage correct des hooks | 8 |
| Web React — UX et parcours offreur complet | 4 |
| Android — composables réutilisables, pattern MVVM respecté | 8 |
| Android — parcours étudiant complet (liste, détail, réservation) | 4 |
| Tests — présence de tests unitaires côté Service / Repository, AAA, tests qui passent | 10 |
| Intégration — les 3 apps se parlent, démo bout-en-bout fonctionne | 5 |
| Git — branches, commits propres, pull requests, pas de gros commits « fourre-tout » | 3 |
| Documentation (README + rapport) | 3 |

### 8.2. Note individuelle (sur 40)

| Critère | Points |
|---|---|
| Contribution technique visible dans Git (commits, PR) | 12 |
| Maîtrise lors de la soutenance (réponse aux questions techniques sur sa partie) | 14 |
| Journal de bord : qualité, honnêteté sur l'usage des agents, réflexion | 8 |
| Capacité à expliquer la partie d'un·e collègue (montre qu'on a compris le projet entier) | 6 |

**Seuil d'admissibilité** : le MVP doit être fonctionnel de bout en bout. Une équipe qui livre une API sans client, ou un client sans API, ne peut pas dépasser la moyenne quel que soit le reste.

---

## 9. Règles d'usage des agents IA

Les agents (Claude, ChatGPT, Copilot, Cursor…) sont **autorisés et même encouragés**, à condition d'être utilisés avec discernement.

**Vous devez :**
- Tenir un **journal de bord individuel** des interactions significatives : quel prompt, pour quelle tâche, qu'avez-vous gardé / modifié / rejeté.
- Être capable d'**expliquer** tout code présent dans votre partie, y compris si un agent l'a généré. En soutenance, on vous demandera d'expliquer ce que fait telle ou telle ligne.
- **Ne jamais** copier-coller du code sans le comprendre.

**Vous ne devez pas :**
- Déléguer l'architecture globale à un agent sans réfléchir par vous-mêmes.
- Utiliser un agent pour rédiger votre rapport à votre place.
- Partager des données utilisateur (réelles ou simulées contenant des infos sensibles) avec un agent externe.

**Bon usage typique :** générer un squelette d'entité JPA, débugger une erreur Compose, expliquer un message d'erreur incompréhensible, proposer des cas de tests auxquels vous n'aviez pas pensé, reformuler un commit.

**Mauvais usage typique :** demander à l'agent de faire tout le projet pendant que vous regardez.

---

## 10. Conseils et pièges à éviter

- **Commencez par le squelette bout-en-bout** : un `GET /api/offres` qui renvoie `[]`, un écran web et un écran Android qui l'appellent. Même vide, ça prouve que l'architecture tient.
- **Modèle de données d'abord.** Tant que le schéma n'est pas stable, tout le reste bouge. Passez du temps dessus en semaine 1.
- **CORS** : vous aurez une erreur, c'est obligatoire. Configurez `@CrossOrigin` ou un filter global côté Spring dès le début.
- **Testez la couche Service, pas le framework.** Un test qui vérifie que Spring sait injecter un bean n'apporte rien. Un test qui vérifie que « réserver la dernière portion disponible renvoie une erreur » a de la valeur.
- **Ne mergez pas en fin de projet.** Intégrez les 3 apps dès la fin de la semaine 1, puis en continu. Le « on assemble tout vendredi » est la cause d'échec n°1 de ce type de projet.
- **Git discipline** : un commit = une intention. Pas de `wip` ni de `fix` à répétition à la fin.
- **Soignez votre README racine.** On doit pouvoir démarrer le projet à partir du README.

---

## 11. Ressources conseillées

- Spring Boot : [spring.io/guides](https://spring.io/guides), tutoriels officiels « Building a RESTful Web Service » et « Accessing Data with JPA »
- React : documentation officielle [react.dev](https://react.dev) (le guide « Thinking in React » est essentiel)
- Android Compose : [developer.android.com/jetpack/compose](https://developer.android.com/jetpack/compose), codelab « Compose basics » et « State in Compose »
- Tests : documentation JUnit 5, React Testing Library, et le livre *Working Effectively with Unit Tests* (extraits)
- Git : si l'équipe n'est pas à l'aise, passer 1h collectivement sur [learngitbranching.js.org](https://learngitbranching.js.org)

---

## 12. Questions fréquentes

**Peut-on utiliser une autre base que H2 ou PostgreSQL ?**
Oui pour le bonus, non pour le MVP. Une base relationnelle est imposée (le module Spring a été vu avec une base SQL).

**Peut-on utiliser TypeScript côté React ?**
Oui, recommandé même. JavaScript reste accepté.

**Peut-on faire une app iOS à la place d'Android ?**
Non. Le module Android / Compose est explicitement évalué.

**Peut-on utiliser un framework CSS (Tailwind, Bootstrap) ?**
Oui, totalement libre.

**Que se passe-t-il si on n'arrive pas à faire l'Android à temps ?**
Le MVP exige les 3 apps. Mieux vaut une app Android simple (une liste + un détail + un bouton Réserver) que pas d'app du tout. Priorisez.

**Un membre du groupe ne contribue pas. Que fait-on ?**
Alerter l'équipe pédagogique **en semaine 1 ou 2**, pas en semaine 3. Un déséquilibre détecté tôt se corrige.

---

*Bon projet à tous — et amusez-vous : FoodShare doit rester un projet qu'on a envie de montrer.*
