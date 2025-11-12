# 📱 Application Android - Gestion des Comptes Bancaires SOAP

![Android](https://img.shields.io/badge/Android-API%2021+-green.svg)
![Kotlin](https://img.shields.io/badge/Kotlin-2.0.0-blue.svg)
![License](https://img.shields.io/badge/License-Educational-orange.svg)
![Status](https://img.shields.io/badge/Status-Functional-success.svg)

Application Android moderne développée en Kotlin pour la gestion de comptes bancaires utilisant le protocole SOAP.

---

## 📋 Table des matières

- [Aperçu](#-aperçu)
- [Fonctionnalités](#-fonctionnalités)
- [Technologies](#-technologies)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Utilisation](#-utilisation)
- [Architecture](#-architecture)
- [Mode DEMO](#-mode-demo)
- [Captures d'écran](#-captures-décran)
- [Documentation](#-documentation)
- [Dépannage](#-dépannage)
- [Contribution](#-contribution)
- [Licence](#-licence)

---

## 🎯 Aperçu

Cette application Android permet de gérer des comptes bancaires (COURANT et EPARGNE) via une interface Material Design moderne. Elle communique avec un service web SOAP pour effectuer des opérations CRUD (Create, Read, Delete).

### Points forts

✅ **Interface moderne** - Material Design 3  
✅ **Mode DEMO intégré** - Fonctionne sans serveur  
✅ **Programmation asynchrone** - Coroutines Kotlin  
✅ **Architecture propre** - Séparation des responsabilités  
✅ **Performance optimisée** - RecyclerView avec ViewHolder  

---

## ✨ Fonctionnalités

### 📊 Liste des comptes
- Affichage des comptes en cards Material Design
- Scroll fluide avec RecyclerView
- Informations détaillées : ID, solde, type, date de création

### ➕ Ajouter un compte
- Dialog Material avec validation
- Saisie du solde initial
- Choix du type : COURANT ou EPARGNE
- Feedback utilisateur (Toast messages)

### 🗑️ Supprimer un compte
- Dialog de confirmation
- Suppression avec animation
- Mise à jour instantanée de la liste

### 🎭 Mode DEMO
- Fonctionne sans serveur SOAP
- 3 comptes de démonstration
- Toutes les opérations simulées en mémoire
- Parfait pour tests et démonstrations

---

## 🛠️ Technologies

### Langage et Framework
- **Kotlin 2.0.0** - Langage de programmation
- **Android SDK** - API 21 à 35 (Android 5.0 - 15)
- **Jetpack** - Bibliothèques AndroidX

### Bibliothèques principales
```gradle
// Interface utilisateur
implementation("com.google.android.material:material:1.11.0")
implementation("androidx.recyclerview:recyclerview:1.3.2")
implementation("androidx.constraintlayout:constraintlayout:2.1.4")

// Communication réseau
implementation("com.squareup.okhttp3:okhttp:4.12.0")

// XML parsing
implementation("org.simpleframework:simple-xml:2.7.1")

// Coroutines
implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
```

### Architecture
- **MVVM simplifié** - Séparation UI/logique
- **Coroutines** - Programmation asynchrone
- **OkHttp** - Communication HTTP/SOAP
- **XmlPullParser** - Parsing XML natif Android

---

## 🚀 Installation

### Prérequis

- **Java 17** ou supérieur
- **Android Studio** Arctic Fox (2022.1) ou supérieur
- **Gradle 8.10.2** (inclus)
- **Appareil Android** ou émulateur (API 21+)

### Étapes d'installation

1. **Cloner le projet**
   ```bash
   git clone https://github.com/saadkarzouz/soap-compte-app.git
   cd soap-compte-app
   ```

2. **Ouvrir dans Android Studio**
   ```
   File > Open > Sélectionner le dossier tp14
   ```

3. **Synchroniser Gradle**
   ```
   File > Sync Project with Gradle Files
   ```
   Ou cliquer sur l'icône 🐘 en haut

4. **Lancer l'application**
   ```
   Run > Run 'app' (ou Shift + F10)
   ```

### Installation rapide via Gradle

```bash
# Compiler l'application
.\gradlew.bat assembleDebug

# Installer sur un appareil connecté
.\gradlew.bat installDebug
```

---

## ⚙️ Configuration

### Configuration Java 17

Le projet nécessite Java 17. Configurez-le dans `gradle.properties` :

```properties
org.gradle.java.home=C:\\Program Files\\Java\\jdk-17
```

Ou dans Android Studio :
```
File > Settings > Build Tools > Gradle
Gradle JDK: Sélectionner JDK 17
```

### Configuration du serveur SOAP

**Pour utiliser un serveur SOAP réel**, modifiez `Service.kt` :

```kotlin
class Service {
    private val NAMESPACE = "http://ws.soapAcount/"
    private val URL = "http://10.0.2.2:8082/services/ws"  // Émulateur
    // private val URL = "http://192.168.1.100:8082/services/ws"  // Appareil réel
    
    private val DEMO_MODE = false  // Désactiver le mode DEMO
}
```

### Permissions réseau

Les permissions sont déjà configurées dans `AndroidManifest.xml` :

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<application android:usesCleartextTraffic="true">
```

---

## 📖 Utilisation

### Démarrage rapide

1. **Lancer l'app** - Cliquez sur ▶️ Run
2. **Observer** - 3 comptes de démonstration s'affichent
3. **Ajouter** - Cliquez sur "Ajouter" en bas
4. **Supprimer** - Cliquez sur "Supprimer" dans une card

### Scénario d'utilisation complet

#### 1️⃣ Ajouter un compte

```
1. Cliquer sur le bouton "Ajouter"
2. Saisir le solde (ex: 5000)
3. Sélectionner le type (COURANT ou EPARGNE)
4. Cliquer sur "Ajouter"
5. ✅ Le compte apparaît dans la liste
```

#### 2️⃣ Supprimer un compte

```
1. Cliquer sur "Supprimer" dans une card
2. Confirmer dans le dialog
3. ✅ Le compte disparaît de la liste
```

#### 3️⃣ Voir les détails

Chaque compte affiche :
- **ID** - Numéro unique du compte
- **Solde** - Montant en DH
- **Type** - COURANT (orange) ou EPARGNE (bleu)
- **Date** - Date de création

---

## 🏗️ Architecture

### Structure du projet

```
tp14/
├── app/src/main/
│   ├── java/ma/projet/soapcompteapp/
│   │   ├── MainActivity.kt              # 🎯 Activité principale
│   │   ├── adapter/
│   │   │   └── CompteAdapter.kt         # 🔄 Adapter RecyclerView
│   │   ├── beans/
│   │   │   ├── Compte.kt                # 💾 Data class
│   │   │   └── TypeCompte.kt            # 💾 Enum
│   │   └── ws/
│   │       └── Service.kt               # 🌐 Service SOAP
│   └── res/
│       └── layout/
│           ├── activity_main.xml        # 🎨 Layout principal
│           ├── item_compte.xml          # 🎨 Layout item
│           └── popup.xml                # 🎨 Dialog ajout
└── README.md
```

### Composants principaux

#### 1. MainActivity.kt
Point d'entrée de l'application. Gère :
- Initialisation des vues
- Configuration du RecyclerView
- Gestion des dialogs
- Appels asynchrones au service

#### 2. Service.kt
Gère la communication SOAP. Méthodes :
- `getComptes()` - Récupère tous les comptes
- `createCompte()` - Crée un nouveau compte
- `deleteCompte()` - Supprime un compte par ID

#### 3. CompteAdapter.kt
Adapter pour le RecyclerView. Responsabilités :
- Affichage des comptes en liste
- Gestion des clics (modifier/supprimer)
- Optimisation avec ViewHolder pattern

#### 4. Beans
Classes de données :
- **Compte** - Représente un compte bancaire
- **TypeCompte** - Enum (COURANT, EPARGNE)

---

## 🎭 Mode DEMO

### Qu'est-ce que le mode DEMO ?

Le mode DEMO permet d'utiliser l'application **sans serveur SOAP**. Les données sont stockées en mémoire et toutes les opérations sont simulées.

### Activation/Désactivation

**Dans `Service.kt` :**

```kotlin
// MODE DEMO ACTIVÉ (par défaut)
private val DEMO_MODE = true

// MODE SOAP RÉEL
private val DEMO_MODE = false
```

### Avantages du mode DEMO

✅ **Pas de serveur requis** - Fonctionne immédiatement  
✅ **Tests complets** - Toutes les fonctionnalités disponibles  
✅ **Démonstrations** - Parfait pour présenter le projet  
✅ **Développement** - Travail sans infrastructure  

### Fonctionnement

```kotlin
// Logique conditionnelle dans Service.kt
fun getComptes(): List<Compte> {
    if (DEMO_MODE) {
        // Retourner données en mémoire
        return demoComptes.toList()
    }
    // Sinon, appel SOAP
    // ...
}
```

### Données de démonstration

3 comptes sont créés automatiquement :
- Compte 1 : 5000.0 DH (COURANT)
- Compte 2 : 10000.0 DH (EPARGNE)
- Compte 3 : 2500.50 DH (COURANT)

---

## 📸 Captures d'écran

### Écran principal
```
┌─────────────────────────────────┐
│  Gestion des Comptes            │
├─────────────────────────────────┤
│  ┌─────────────────────────┐   │
│  │ Compte Numéro 1         │   │
│  │ 5000.0 DH      [Modifier]│   │
│  │ [COURANT]    [Supprimer]│   │
│  │ 12/11/2025              │   │
│  └─────────────────────────┘   │
│         [  Ajouter  ]           │
└─────────────────────────────────┘
```

### Dialog d'ajout
```
┌─────────────────────────┐
│  Nouveau compte         │
├─────────────────────────┤
│  Solde: [________]      │
│                         │
│  ○ COURANT              │
│  ○ EPARGNE              │
│                         │
│  [Annuler]  [Ajouter]   │
└─────────────────────────┘
```

---

## 📚 Documentation

### Guides disponibles

| Fichier | Description |
|---------|-------------|
| **README_FINAL.md** | Ce fichier - Documentation complète |
| **DEMO_MODE_ACTIVATED.md** | Guide du mode DEMO |
| **TEST_NOW.md** | Guide de test rapide |
| **CRASH_FIXED.md** | Fix du crash au démarrage |
| **FIXED.md** | Correctifs des dépendances |
| **TROUBLESHOOTING.md** | Dépannage complet |
| **INDEX.md** | Navigation dans la documentation |

### Références externes

- [Documentation Android](https://developer.android.com)
- [Kotlin Documentation](https://kotlinlang.org/docs/)
- [Material Design](https://material.io/design)
- [OkHttp](https://square.github.io/okhttp/)

---

## 🔧 Dépannage

### Problème : "App keeps stopping"

**Solution :** Thème corrigé dans `themes.xml`
```xml
<style name="Theme.SOAPCompteApp" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
```
Voir `CRASH_FIXED.md` pour plus de détails.

### Problème : "Erreur lors de l'ajout"

**Solution :** Mode DEMO activé par défaut
```kotlin
private val DEMO_MODE = true
```
Voir `DEMO_MODE_ACTIVATED.md` pour plus de détails.

### Problème : "Android Gradle plugin requires Java 17"

**Solution :** Configurer Java 17 dans `gradle.properties`
```properties
org.gradle.java.home=C:\\Program Files\\Java\\jdk-17
```

### Problème : Build échoue

**Solution :**
```bash
.\gradlew.bat clean build
```

### Plus de solutions

Consultez `TROUBLESHOOTING.md` pour une liste complète des problèmes et solutions.

---

## 🧪 Tests

### Tests manuels

#### Test 1 : Affichage de la liste
```
✓ App s'ouvre
✓ 3 comptes visibles
✓ Informations correctes
```

#### Test 2 : Ajout de compte
```
✓ Dialog s'ouvre
✓ Validation du solde
✓ Compte ajouté
✓ Toast affiché
```

#### Test 3 : Suppression de compte
```
✓ Dialog de confirmation
✓ Compte supprimé
✓ Liste mise à jour
```

### Tests automatisés

Les tests unitaires peuvent être ajoutés dans :
- `app/src/test/` - Tests unitaires
- `app/src/androidTest/` - Tests d'instrumentation

---

## 🚦 Roadmap

### Version actuelle : 1.0

✅ Liste des comptes  
✅ Ajout de compte  
✅ Suppression de compte  
✅ Mode DEMO  
✅ Interface Material Design  

### Fonctionnalités futures

- [ ] Modification de compte
- [ ] Recherche et filtrage
- [ ] SwipeRefreshLayout
- [ ] Persistance locale (Room)
- [ ] Mode hors ligne
- [ ] Authentification utilisateur
- [ ] Tests unitaires
- [ ] Tests UI (Espresso)
- [ ] Migration vers Jetpack Compose

---

## 👥 Contribution

### Comment contribuer

1. Fork le projet
2. Créer une branche (`git checkout -b feature/AmazingFeature`)
3. Commit les changements (`git commit -m 'Add AmazingFeature'`)
4. Push vers la branche (`git push origin feature/AmazingFeature`)
5. Ouvrir une Pull Request

### Règles de contribution

- Code en Kotlin
- Respecter l'architecture existante
- Documenter les nouvelles fonctionnalités
- Tester avant de soumettre

---

## 📄 Licence

Ce projet est développé à des fins **éducatives** dans le cadre d'un TP sur les services web SOAP.

```
Copyright (c) 2025 - Projet TP14
Utilisation libre pour l'apprentissage et l'éducation
```

---

## 👨‍💻 Auteur

**Projet développé par :** Saad Karzouz  
**Date :** Novembre 2025  
**Contexte :** TP Services Web SOAP  
**Institution :** École Supérieure de Technologie  

---

## 🙏 Remerciements

- **Android Developers** - Documentation et outils
- **Kotlin Team** - Langage moderne et concis
- **Material Design** - Guidelines d'interface
- **OkHttp** - Bibliothèque HTTP robuste
- **Community** - Tutoriels et support

---

## 📞 Support

### En cas de problème

1. **Consulter la documentation** - Voir section [Documentation](#-documentation)
2. **Vérifier les issues** - Rechercher dans les problèmes connus
3. **Créer une issue** - Décrire le problème en détail

### Contact

- **Email :** saad.karzouz@example.com
- **GitHub :** [@saadkarzouz](https://github.com/saadkarzouz)
- **LinkedIn :** [Saad Karzouz](https://linkedin.com/in/saadkarzouz)

---

## 📊 Statistiques du projet

```
📝 Lignes de code Kotlin    : ~600 lignes
🎨 Fichiers layout XML      : 3 fichiers
📚 Fichiers documentation   : 10 guides
📦 Dépendances             : 8 bibliothèques
⏱️  Temps de développement  : ~3 heures
✅ Fonctionnalités          : 3 opérations CRUD
```

---

## 🎯 Résumé technique

### Stack technique

```
Frontend  : Kotlin + Android SDK + Material Design
Backend   : Service SOAP (simulé en mode DEMO)
Network   : OkHttp 4.12.0
XML       : XmlPullParser (natif Android)
Async     : Kotlin Coroutines
UI        : RecyclerView + ConstraintLayout
```

### Exigences système

```
Min SDK       : API 21 (Android 5.0 Lollipop)
Target SDK    : API 35 (Android 15)
Compile SDK   : API 35
Java          : JDK 17
Gradle        : 8.10.2
Android Plugin: 8.8.1
```

---

## ✅ Checklist de déploiement

Avant de déployer en production :

- [ ] Désactiver le mode DEMO
- [ ] Configurer l'URL du serveur SOAP
- [ ] Tester avec le serveur réel
- [ ] Désactiver `usesCleartextTraffic` (utiliser HTTPS)
- [ ] Ajouter ProGuard rules
- [ ] Tester sur plusieurs appareils
- [ ] Vérifier les permissions
- [ ] Optimiser les images
- [ ] Tester les performances
- [ ] Générer l'APK release signé

---

## 🎉 Conclusion

Cette application démontre :

✅ **Intégration SOAP** dans Android  
✅ **Architecture propre** et maintenable  
✅ **Interface moderne** Material Design  
✅ **Mode DEMO** pour tests sans serveur  
✅ **Gestion asynchrone** avec Coroutines  
✅ **Code Kotlin idiomatique**  

**L'application est prête pour la démonstration, les tests et le développement futur !**

---

<div align="center">

**Développé avec ❤️ en Kotlin**

[![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)](https://www.android.com/)
[![Kotlin](https://img.shields.io/badge/Kotlin-0095D5?style=for-the-badge&logo=kotlin&logoColor=white)](https://kotlinlang.org/)
[![Material Design](https://img.shields.io/badge/Material%20Design-757575?style=for-the-badge&logo=material-design&logoColor=white)](https://material.io/)

**Merci d'avoir utilisé cette application ! 🚀**

</div>

