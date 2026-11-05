# Mini Docto+

Mini-application de **prise de rendez-vous médicaux** qui met en relation patients et professionnels de santé.

Le projet comprend trois applications :

| Dossier | Application | Stack |
|---|---|---|
| `backend/` | API REST | Spring Boot 4, Spring Security, JWT, MongoDB |
| `pro-web/` | Application web des professionnels | React 19, Vite |
| `patient-mobile/` | Application mobile des patients | Flutter, Provider |

## Fonctionnalités

### Authentification
- Inscription et connexion des patients et des professionnels
- Authentification **JWT**, avec rafraîchissement du token (`/api/auth/refresh`)
- Séparation des accès par rôle : `PATIENT` et `PRO`

### Patient (mobile)
- Consulter les professionnels disponibles, **triés par score décroissant**
- Réserver un créneau horaire
- Consulter ses rendez-vous
- Modifier ou annuler uniquement ses propres rendez-vous

### Professionnel (web)
- Ajouter des créneaux de disponibilité
- Supprimer ses propres créneaux
- Consulter les rendez-vous réservés par les patients

## Endpoints principaux

| Préfixe | Rôle |
|---|---|
| `/api/auth` | Inscription, connexion, refresh token |
| `/api/pros` | Liste des professionnels et de leurs créneaux |
| `/api/slots` | Gestion des créneaux (professionnel) |
| `/api/appointments` | Réservation, modification, annulation |
| `/api/patients`, `/api/users` | Gestion des comptes |

La documentation Swagger est disponible sur `http://localhost:5000/swagger-ui/index.html`.

## Installation et lancement

Prérequis : Java 17, Node.js 18+, SDK Flutter, MongoDB (local ou Atlas).

### 1. Backend Spring Boot

Configurer MongoDB dans `backend/src/main/resources/application.properties` :

```properties
server.port=5000
spring.data.mongodb.uri=mongodb://localhost:27017/minidoctoplus
```

```bash
cd backend
./mvnw spring-boot:run        # Windows : .\mvnw.cmd spring-boot:run
```

API locale : `http://localhost:5000/api`

### 2. Application web professionnel

```bash
cd pro-web
npm install
npm run dev                   # http://localhost:5173
```

Si besoin, configurer l'URL de l'API dans `pro-web/.env` :

```env
VITE_API_URL=http://localhost:5000/api
```

### 3. Application mobile patient

```bash
cd patient-mobile
flutter pub get
flutter run                   # ou : flutter run -d chrome
```

URL de l'API selon la cible :

- Web / desktop : `http://localhost:5000/api`
- Émulateur Android : `http://10.0.2.2:5000/api`
- Appareil physique : IP locale du PC, par exemple `flutter run --dart-define=API_HOST=192.168.1.10`

## Tests

```bash
cd backend && ./mvnw test
cd pro-web && npm run lint && npm run build
cd patient-mobile && flutter analyze && flutter test
```

## Sécurité

- Mots de passe hachés avec **BCrypt**
- Routes protégées par Spring Security, avec contrôle des rôles `PATIENT` / `PRO`
- Un patient ne peut modifier ou annuler que ses propres rendez-vous
- Un professionnel ne peut supprimer que ses propres créneaux
- Les mots de passe ne sont jamais renvoyés par l'API

## Performance

- Index unique sur l'e-mail des utilisateurs (recherche rapide, pas de doublons)
- Index composé unique sur `pro` + `date` + `startTime` pour les créneaux
- Index unique sur `slot` pour les rendez-vous : **pas de double réservation**
- Tri des professionnels par score fait côté backend
- Les clients React et Flutter ne récupèrent que les données nécessaires

## Firebase Analytics

![Capture Firebase Analytics](firebase_analytics.png)

## Auteur

**Yosra Dab** — Ingénieure logiciel Full Stack · [Portfolio](https://yosradab.vercel.app) · [GitHub](https://github.com/yosradab) · [LinkedIn](https://linkedin.com/in/yosradab)
