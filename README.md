# Application Android SOAP - Gestion des Comptes Bancaires

## 📚 Documentation

Ce projet comprend plusieurs guides documentaires:

- **[README.md](README.md)** (ce fichier) - Vue d'ensemble et architecture complète
- **[QUICK_START.md](QUICK_START.md)** - ⚡ Guide de démarrage rapide (5 minutes)
- **[KSOAP2_GUIDE.md](KSOAP2_GUIDE.md)** - Guide détaillé sur l'utilisation de ksoap2
- **[IMPLEMENTATION_SUMMARY.md](IMPLEMENTATION_SUMMARY.md)** - Résumé complet de l'implémentation
- **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - 🔍 Checklist de dépannage

---

## Description
Application Android en Kotlin utilisant le protocole SOAP pour gérer des comptes bancaires.

---

## Video



https://github.com/user-attachments/assets/25dcccb0-af71-45aa-8894-bde161325d5b



---

## Fonctionnalités
- ✅ Afficher la liste des comptes bancaires
- ✅ Ajouter un nouveau compte (COURANT ou EPARGNE)
- ✅ Supprimer un compte
- ⚠️ Modifier un compte (non implémentée)

## Prérequis
- Android Studio Arctic Fox ou supérieur
- JDK 17 (requis par Android Gradle Plugin 8.8.1)
- SDK Android API 21 minimum
- Un serveur SOAP fonctionnant sur `http://10.0.2.2:8082/services/ws`

## Configuration du projet

### 1. Installer Java 17
Le projet nécessite Java 17. Si vous utilisez Java 11, vous devez :
- Télécharger et installer JDK 17 depuis [Oracle](https://www.oracle.com/java/technologies/downloads/) ou [Adoptium](https://adoptium.net/)
- Configurer la variable d'environnement `JAVA_HOME` pour pointer vers Java 17
- Ou modifier le fichier `gradle.properties` en ajoutant :
```
org.gradle.java.home=CHEMIN_VERS_VOTRE_JDK_17
```

### 2. Dépendances utilisées
Les dépendances suivantes ont été ajoutées dans `app/build.gradle.kts` :

```kotlin
// Material Components et RecyclerView
implementation("com.google.android.material:material:1.11.0")
implementation("androidx.recyclerview:recyclerview:1.3.2")
implementation("androidx.appcompat:appcompat:1.6.1")
implementation("androidx.constraintlayout:constraintlayout:2.1.4")

// Bibliothèque SOAP
implementation("com.google.code.ksoap2-android:ksoap2-android:3.6.4")

// Coroutines pour les appels asynchrones
implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
```

### 3. Permissions AndroidManifest
Les permissions suivantes sont nécessaires pour la communication réseau :

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Et pour autoriser le trafic HTTP en clair (pour le développement) :
```xml
android:usesCleartextTraffic="true"
```

## Structure du projet

```
app/src/main/java/ma/projet/soapcompteapp/
├── MainActivity.kt                    # Activité principale
├── adapter/
│   └── CompteAdapter.kt              # Adapter pour RecyclerView
├── beans/
│   ├── Compte.kt                     # Classe de données Compte
│   └── TypeCompte.kt                 # Énumération des types
└── ws/
    └── Service.kt                    # Service SOAP
```

## Architecture

### 1. Classes de données (beans)
- **TypeCompte** : Énumération définissant les types de comptes (COURANT, EPARGNE)
- **Compte** : Data class représentant un compte bancaire avec :
  - id : Identifiant unique
  - solde : Solde du compte
  - dateCreation : Date de création
  - type : Type du compte

### 2. Service SOAP (ws/Service.kt)
Gère la communication avec le service SOAP :
- `getComptes()` : Récupère la liste de tous les comptes
- `createCompte(solde, type)` : Crée un nouveau compte
- `deleteCompte(id)` : Supprime un compte par son ID

**Configuration du service :**
```kotlin
NAMESPACE = "http://ws.soapAcount/"
URL = "http://10.0.2.2:8082/services/ws"
```
Note : `10.0.2.2` est l'adresse localhost pour l'émulateur Android

### 3. Adapter RecyclerView (adapter/CompteAdapter.kt)
Affiche la liste des comptes avec :
- Recyclage des vues pour optimiser les performances
- Listeners pour les actions Modifier et Supprimer
- Méthodes pour mettre à jour et supprimer des comptes

### 4. MainActivity
Activité principale qui :
- Configure le RecyclerView avec l'Adapter
- Charge les comptes au démarrage
- Affiche une boîte de dialogue pour ajouter un compte
- Gère la suppression avec confirmation
- Utilise des Coroutines pour les appels réseau asynchrones

## Interface utilisateur

### Layouts
- **activity_main.xml** : Layout principal avec RecyclerView et bouton Ajouter
- **item_compte.xml** : Layout d'un élément de compte dans la liste
- **popup.xml** : Formulaire pour ajouter un nouveau compte

## Compilation et exécution

### Depuis Android Studio
1. Ouvrir le projet dans Android Studio
2. Attendre la synchronisation Gradle
3. Sélectionner un émulateur ou connecter un appareil
4. Cliquer sur Run (▶️)

### Depuis la ligne de commande
```bash
# Compiler le projet
.\gradlew.bat assembleDebug

# Installer sur un appareil connecté
.\gradlew.bat installDebug
```

## Configuration du serveur SOAP

Assurez-vous que votre serveur SOAP expose les méthodes suivantes :

### getComptes
Retourne une liste de comptes avec les propriétés :
- id (Long)
- solde (Double)
- dateCreation (String au format "yyyy-MM-dd")
- type (String : "COURANT" ou "EPARGNE")

### createCompte
Paramètres :
- solde (Double)
- type (String : "COURANT" ou "EPARGNE")

Retourne : Boolean

### deleteCompte
Paramètres :
- id (Long)

Retourne : Boolean

## Dépannage

### Erreur "Unresolved reference"
- Synchroniser le projet avec Gradle : File > Sync Project with Gradle Files
- Nettoyer et recompiler : Build > Clean Project puis Build > Rebuild Project

### Erreur "Android Gradle plugin requires Java 17"
- Installer JDK 17
- Configurer JAVA_HOME ou gradle.properties avec le chemin vers JDK 17

### Erreur de connexion au serveur SOAP
- Vérifier que le serveur SOAP est démarré
- Vérifier l'URL dans Service.kt
- Pour un émulateur, utiliser `10.0.2.2` au lieu de `localhost`
- Pour un appareil physique, utiliser l'adresse IP de votre machine

### Permission denied (Cleartext HTTP)
- Vérifier que `android:usesCleartextTraffic="true"` est dans AndroidManifest.xml
- Pour la production, utiliser HTTPS au lieu de HTTP

## Technologies utilisées
- **Kotlin** : Langage de programmation
- **ksoap2-android** : Bibliothèque pour la communication SOAP
- **Material Components** : Composants UI Material Design
- **RecyclerView** : Affichage efficace de listes
- **Coroutines** : Programmation asynchrone
- **AndroidX** : Bibliothèques Android modernes

## Auteur
Application développée dans le cadre d'un TP sur les services web SOAP

## Licence
Projet à usage éducatif

