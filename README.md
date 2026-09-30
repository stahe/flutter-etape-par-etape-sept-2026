# Introduction étape par étape au framework mobile Flutter

📖 **Lire le tutoriel : [https://stahe.github.io/flutter-etape-par-etape-sept-2026/](https://stahe.github.io/flutter-etape-par-etape-sept-2026/)**

Ce cours vous apprend à écrire une application **mobile** avec le framework [Flutter](https://flutter.dev) 3.44 et le langage [Dart](https://dart.dev) 3.12 : une application Android, dont les écrans sont fabriqués **sur le téléphone** à partir des données JSON d'un serveur. Le même code produit aussi une application web.

Il reprend le plan du cours [Introduction étape par étape au framework React](https://stahe.github.io/react-etape-par-etape-sept-2026/) (et des cours [Vue.js](https://stahe.github.io/vuejs-etape-par-etape-sept-2026/) et [Angular](https://stahe.github.io/angular-etape-par-etape-sept-2026/)) : même serveur, mêmes écrans, écrits à la manière de Flutter, pour un téléphone.

| Cours React | Cours Flutter |
|---|---|
| une application web, dans le navigateur | une application mobile (Android), et web |
| des composants fonctions, du JSX, des hooks | des widgets, une méthode `build()`, `setState` |
| HTML + CSS (Bootstrap) | le moteur de Flutter dessine chaque pixel (Material 3) |
| React Router | go_router |
| Zustand, TanStack Query | provider (`ChangeNotifier`), un petit cache |
| i18next | des dictionnaires JSON, `intl`, `flutter_localizations` |
| le navigateur garde le cookie du jeton | un client HTTP qui gère les cookies (sur le téléphone) |
| `localStorage` | `shared_preferences` |

Le serveur, lui, ne change pas : c'est le serveur JSON de l'application **RdvMedecins** déjà utilisé par les clients React, Vue.js et Angular.

## L'approche : de nombreux petits exemples, puis une étude de cas

Flutter demande d'apprendre beaucoup de notions à la fois (un langage, des widgets, un cycle de vie, l'asynchrone) : le cours s'articule donc autour de **25 petits exemples**, chacun centré sur une notion. Ils forment un seul projet Flutter : une seule commande `flutter pub get`, puis `flutter run -t lib/<exemple>/main.dart` pour en lancer un.

| Chapitre | Contenu | Exemples |
|---|---|---|
| Premiers pas | un projet Flutter, `StatelessWidget` / `StatefulWidget`, `setState`, l'arbre des widgets, les événements, tous les types de champs, un réducteur (classes scellées, `switch`), la validation, `Form` et `FormField`, la mise en forme (`intl`) | 01–09 |
| Les widgets | paramètres, fonctions en paramètres (`ValueChanged`), composition (`child`, builders, widget générique), cycle de vie (`initState`, `didUpdateWidget`, `dispose`), `InheritedWidget`, mixins et animations, fenêtre de confirmation (`showDialog`) | 10–16 |
| Le routage | go_router 16 : routes, paramètres, `ShellRoute` + `NavigationBar`, chargement différé, `redirect`, gardes | 17–18 |
| L'asynchrone et l'état partagé | `Future`, `Stream`, `async*`, minuteries, anti-rebond, réponses périmées, `FutureBuilder`, `StreamBuilder`, provider, `shared_preferences`, thème sombre | 19–20 |
| Internationalisation | dictionnaires JSON, paramètres, pluriels (`Intl.pluralLogic`), dates, montants, calendrier traduit | 21 |
| Le serveur, une boîte noire | installation du serveur JSON, son API, 48 exemples `curl`, ce qui change pour un client mobile | – |
| Dialoguer avec le serveur | le paquet `http`, l'adresse du serveur (émulateur, téléphone, web), le cookie du jeton sur le téléphone, une couche d'accès à l'API, un modèle de vue (MVVM), les erreurs du serveur attachées aux champs | 22–25 |

Chaque exemple est présenté avec son code complet, commenté ligne par ligne, et des copies d'écran de son exécution.

## Le serveur : une boîte noire

Le serveur est le serveur NestJS des cours précédents, dont les contrôleurs renvoient du **JSON**. Le cours le traite comme une **boîte noire** : on l'installe, on étudie son API, on l'interroge avec `curl` — mais on n'a pas besoin de lire son code (fourni et commenté pour les curieux).

- toutes les erreurs ont la même forme : `{ "statusCode": 409, "cle": "ERRORS.LOGIN_TAKEN", "params": {...}, "champs": {...} }` — des **clés** de traduction, jamais de texte ;
- authentification par jeton JWT dans un cookie `httpOnly` : le navigateur le garde tout seul ; sur le téléphone, c'est l'application qui le range et le renvoie ;
- un « mode test » du captcha pour pouvoir interroger l'API avec `curl` (et fabriquer les copies d'écran par des tests automatiques).

## L'étude de cas : le client Flutter de RdvMedecins

Une application complète de **prise de rendez-vous dans un cabinet médical**, dont **tous** les fichiers (une trentaine) sont listés et commentés.

- **Flutter moderne** : Material 3, Dart 3 (records, filtrage, classes scellées, `switch` expressions, extensions), go_router, provider, une architecture en couches (données, état, interface).
- **Trois rôles** : `ADMIN` (gère les médecins et les clients), `DOCTOR` (prend et annule les rendez-vous), `USER` (le patient : réserve pour lui-même, gère son compte).
- **Confidentialité** : un patient ne reçoit jamais le nom des autres patients — le serveur ne l'envoie pas.
- **Tout l'état de l'écran dans son adresse** : `/agenda?idMedecin=1&jour=2026-10-05&reserver=7` ; sur le téléphone, le bouton Retour ferme la fenêtre de réservation ; sur le web, F5 et Précédent / Suivant fonctionnent.
- **Validation par le serveur** : les formulaires affichent sous chaque champ les erreurs renvoyées par l'API ; verrou optimiste, homonymes, login déjà pris...
- **Session** : conservée d'un lancement à l'autre (le cookie est rangé sur l'appareil), rétablie au démarrage (`GET /api/auth/moi`), expiration gérée en un seul endroit (réponse 401).
- **Adapté au téléphone** : menu en tiroir (ou permanent sur grand écran), listes plutôt que tableaux, gestionnaire de mots de passe, zone d'erreur au-dessus de la barre de navigation.
- **Français / anglais**, y compris le calendrier et les textes de Flutter lui-même.
- **Déploiement** : l'application Android (APK), et la version web compilée, servie par le serveur JSON lui-même.

## Le contenu du dépôt

```
exemples_flutter/          les 25 petits exemples (un seul projet Flutter)
rdvmedecins-nestjs-json/   le serveur JSON RdvMedecins (la « boîte noire »)
rdvmedecins_flutter/       le client Flutter de l'étude de cas
```

Les dossiers propres à chaque plate-forme (`android/`, `web/`...) sont complétés par `flutter create` (cf. le cours).

## Technologies

Flutter 3.44 · Dart 3.12 · Material 3 · go_router 16 · provider 6 · http 1.5 · shared_preferences 2.5 · flutter_svg 2 · intl · flutter_localizations · flutter_test · côté serveur : NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Prérequis

- Des bases de programmation objet (Java, C#, TypeScript...) et du protocole HTTP. Le cours [Dart](https://stahe.github.io/dart-sept-2026/) est une bonne préparation, mais les notions de Dart utilisées sont expliquées au fil des exemples.
- Le SDK Flutter, Visual Studio Code et son extension Flutter, Android Studio (pour le SDK Android et l'émulateur) ou un téléphone Android, Chrome ; Node.js et un serveur MySQL (par exemple Laragon sous Windows) pour le serveur JSON. Les instructions d'installation sont données dans les annexes du cours.

## Auteur

Ce cours, ses exemples, le client Flutter et l'adaptation du serveur JSON ont été rédigés par **Claude**, l'IA d'[Anthropic](https://www.anthropic.com) (septembre 2026), à la demande de Serge Tahé.
