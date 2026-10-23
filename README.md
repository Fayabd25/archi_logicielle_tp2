# TP2 — Architecture hexagonale · ShopLite

##  Présentation

Ce projet correspond au **TP2 de Master 1**, consacré à la mise en place et à l'analyse d'une **architecture hexagonale** autour du module Commande de ShopLite.

Participants : 
ABID Faycal - KHETACHE Billal

L'objectif est de comprendre la séparation entre :

- le **cœur métier**
-  les **ports**
-  les **adaptateurs primaires**
-  les **adaptateurs secondaires**
-  la **composition de l'application**
-  la **règle de dépendance**
-  les **ADR (Architecture Decision Records)**

## Architecture

L'application suit le principe de l'architecture hexagonale :

```text
              Adaptateurs primaires
            ┌───────────────────────┐
            │   CLI / HTTP / Tests  │
            └───────────┬───────────┘
                        │
                        ▼
              ┌───────────────────┐
              │    Cœur métier    │
              │                   │
              │    PlaceOrder     │
              │      Order        │
              └─────────┬─────────┘
                        │
                        ▼
             Adaptateurs secondaires
          ┌───────────────────────────┐
          │ Repository / Catalog /    │
          │ Notifier                  │
          └───────────────────────────┘
```

Le domaine ne dépend d'aucun élément technique extérieur.

##  Organisation

```text
seance2-architecture/
└── hexagonal/
    ├── domain/
    │   └── Order.js
    │
    ├── application/
    │   └── PlaceOrder.js
    │
    ├── adapters/
    │   ├── primary/
    │   │   ├── cli.js
    │   │   └── httpServer.js
    │   │
    │   └── secondary/
    │       ├── InMemoryOrderRepository.js
    │       ├── StaticCatalog.js
    │       └── ConsoleNotifier.js
    │
    ├── container.js
    └── PlaceOrder.test.js
```

##  Tests

Les tests automatisés vérifient notamment :

- l'enregistrement d'une commande valide ;
- le rejet d'un produit inconnu ;
- le rejet d'une commande vide.

### Résultat

**3 tests passent et 0 test échoue.**

![Résultat des tests](./docs/tests.png)

##  Lancer les tests

Depuis la racine du projet :

```bash
node --test seance2-architecture/hexagonal/PlaceOrder.test.js
```

Résultat attendu :

```text
ℹ tests 3
ℹ pass 3
ℹ fail 0
```

##  Vérification de l'architecture

La règle d'architecture peut être vérifiée avec :

```bash
npm run lint:archi
```

La règle vérifie notamment que le domaine ne dépend pas de composants extérieurs.

Résultat attendu :

```text
✓ Règle de dépendance respectée :
aucun fichier domain/ n'importe l'extérieur.
```

##  Fonctionnement

Une commande contient notamment :

- un identifiant ;
- un identifiant client ;
- des lignes de commande ;
- une date de création.

Le **total de la commande est calculé** à partir de la quantité et du prix unitaire de chaque ligne.

Le prix unitaire est conservé dans chaque ligne afin de garder l'historique du prix appliqué au moment de la commande.

## Persistance

Le projet utilise actuellement un :

```text
InMemoryOrderRepository
```

Les commandes sont donc conservées uniquement en mémoire.

Cela signifie qu'elles sont perdues lorsque l'application est arrêtée.

Une évolution possible serait de remplacer cet adaptateur par un :

```text
PostgresOrderRepository
```

tout en conservant le même port `OrderRepository`.

##  Architecture Decision Record

Une décision d'architecture est documentée dans :

```text
adr/0001-architecture-hexagonale.md
```

Cette décision concerne l'isolation du stockage derrière le port :

```text
OrderRepository
```

##  Technologies

- JavaScript
- Node.js
- Architecture hexagonale
- Tests Node.js
- Mermaid
- PostgreSQL — évolution envisagée

##  Objectif du TP

Ce TP permet de mettre en pratique les principes de l'architecture hexagonale et notamment :

> **Le cœur métier ne doit pas dépendre des détails techniques.**

Les détails techniques sont isolés dans les adaptateurs et reliés au cœur par des ports.
