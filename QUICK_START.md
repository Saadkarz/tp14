# 🚀 Guide de Démarrage Rapide

## ⚡ Démarrage en 5 minutes

### Étape 1: Vérifier Java 17
```cmd
java -version
```
Vous devez voir: `java version "17.x.x"`

Si vous avez Java 11, téléchargez Java 17:
- [Adoptium OpenJDK 17](https://adoptium.net/temurin/releases/?version=17)
- [Oracle JDK 17](https://www.oracle.com/java/technologies/downloads/#java17)

### Étape 2: Configurer le projet dans Android Studio

1. **Ouvrir le projet**
   - File > Open > Sélectionner le dossier `tp14`

2. **Configurer Gradle JDK (si nécessaire)**
   - File > Settings > Build, Execution, Deployment > Build Tools > Gradle
   - Gradle JDK: Choisir `jbr-17` ou votre JDK 17

3. **Synchroniser Gradle**
   - Cliquer sur l'icône 🐘 "Sync Project with Gradle Files"
   - Attendre le téléchargement des dépendances (~2-3 minutes)

### Étape 3: Démarrer le serveur SOAP

Assurez-vous que votre serveur SOAP est actif sur:
```
http://localhost:8082/services/ws
```

**Note importante**: L'URL dans le code utilise `10.0.2.2` (alias localhost pour émulateur Android)

### Étape 4: Lancer l'application

1. **Démarrer un émulateur**
   - Tools > Device Manager
   - Choisir un appareil (ex: Pixel 5, API 33+)
   - Cliquer sur ▶️ Play

2. **Exécuter l'app**
   - Cliquer sur le bouton ▶️ Run
   - Ou: Run > Run 'app'
   - Ou: `Shift + F10`

### Étape 5: Tester l'application

✅ **Liste des comptes** s'affiche automatiquement  
✅ **Ajouter un compte**: Cliquer sur "Ajouter" en bas  
✅ **Supprimer un compte**: Cliquer sur "Supprimer" dans une carte  

---

## 🔧 Résolution des problèmes courants

### ❌ "Android Gradle plugin requires Java 17"

**Solution rapide**: Dans Android Studio
```
File > Settings > Build, Execution, Deployment > Build Tools > Gradle
Gradle JDK: Sélectionner jbr-17 (ou JDK 17)
```

### ❌ "Unresolved reference: ksoap2"

**Solution**: Synchroniser Gradle
```
File > Sync Project with Gradle Files
```
Ou nettoyer le projet:
```
Build > Clean Project
Build > Rebuild Project
```

### ❌ "java.net.ConnectException: Failed to connect"

**Causes possibles**:
1. Serveur SOAP non démarré
2. Mauvaise URL configurée
3. Problème de firewall

**Solutions**:
- Vérifier que le serveur SOAP est actif
- Pour émulateur: utiliser `http://10.0.2.2:8082/services/ws`
- Pour appareil physique: utiliser `http://192.168.x.x:8082/services/ws`

### ❌ "Cleartext HTTP traffic not permitted"

**Solution**: Déjà configuré dans `AndroidManifest.xml`
```xml
android:usesCleartextTraffic="true"
```

---

## 📝 Modifier l'URL du serveur

Si votre serveur SOAP utilise un port différent:

**Fichier**: `app/src/main/java/ma/projet/soapcompteapp/ws/Service.kt`

```kotlin
// Ligne 22 - Modifier l'URL
private val URL = "http://10.0.2.2:VOTRE_PORT/votre/chemin"
```

**Exemples**:
- Émulateur + localhost:9090 → `http://10.0.2.2:9090/services/ws`
- Appareil réel + IP 192.168.1.100 → `http://192.168.1.100:8082/services/ws`
- Serveur distant → `http://votre-serveur.com/services/ws`

---

## 🎯 Vérification rapide

### Checklist avant de lancer:

- [ ] Java 17 installé et configuré
- [ ] Android Studio ouvert avec le projet
- [ ] Gradle synchronisé sans erreur
- [ ] Serveur SOAP démarré sur port 8082
- [ ] Émulateur Android lancé
- [ ] Aucune erreur dans "Build Output"

### Tester le serveur SOAP manuellement:

Avec **curl** (Git Bash ou PowerShell):
```bash
curl http://localhost:8082/services/ws?wsdl
```

Avec **Postman**:
1. Nouvelle requête POST
2. URL: `http://localhost:8082/services/ws`
3. Body > raw > XML
4. Coller une requête SOAP test

---

## 📱 Utilisation de l'application

### Interface principale

```
┌─────────────────────────────────────┐
│  SOAP Compte App                    │
├─────────────────────────────────────┤
│  ┌───────────────────────────────┐  │
│  │ Compte Numéro 1               │  │
│  │ 5000.0 DH           [Modifier]│  │
│  │ [COURANT]          [Supprimer]│  │
│  │ 11/11/2025                    │  │
│  └───────────────────────────────┘  │
│                                     │
│  ┌───────────────────────────────┐  │
│  │ Compte Numéro 2               │  │
│  │ 10000.0 DH          [Modifier]│  │
│  │ [EPARGNE]          [Supprimer]│  │
│  │ 10/11/2025                    │  │
│  └───────────────────────────────┘  │
│                                     │
│           [  Ajouter  ]             │
└─────────────────────────────────────┘
```

### Actions disponibles

1. **Ajouter un compte**
   - Cliquer sur "Ajouter"
   - Saisir le solde (ex: 5000)
   - Choisir COURANT ou EPARGNE
   - Valider

2. **Supprimer un compte**
   - Cliquer sur "Supprimer" dans une carte
   - Confirmer la suppression

3. **Modifier un compte** ⚠️ Non implémenté
   - Message informatif affiché

---

## 🐛 Debug et Logs

### Voir les logs en temps réel:

Dans Android Studio:
```
View > Tool Windows > Logcat
```

Filtrer par tag:
- `SOAP` : Logs des appels SOAP
- `MainActivity` : Logs de l'activité
- `CompteAdapter` : Logs de l'adapter

### Logs utiles dans le code:

```kotlin
// Dans Service.kt, après un appel SOAP:
Log.d("SOAP", "Nombre de comptes: ${comptes.size}")

// Dans MainActivity.kt, après chargement:
Log.d("MainActivity", "Comptes chargés: $comptes")
```

---

## 📦 Structure du projet simplifiée

```
tp14/
├── app/
│   ├── build.gradle.kts          ← Dépendances
│   └── src/main/
│       ├── AndroidManifest.xml   ← Permissions
│       ├── java/.../
│       │   ├── MainActivity.kt   ← Point d'entrée
│       │   ├── adapter/
│       │   │   └── CompteAdapter.kt
│       │   ├── beans/
│       │   │   ├── Compte.kt
│       │   │   └── TypeCompte.kt
│       │   └── ws/
│       │       └── Service.kt    ← ⚡ Appels SOAP
│       └── res/layout/
│           ├── activity_main.xml
│           ├── item_compte.xml
│           └── popup.xml
└── README.md                     ← Documentation complète
```

---

## 🎓 Concepts clés

### SOAP avec ksoap2
```kotlin
// 1. Créer la requête
val request = SoapObject(NAMESPACE, METHOD)

// 2. Ajouter des paramètres
request.addProperty("param", value)

// 3. Créer l'enveloppe
val envelope = SoapSerializationEnvelope(SoapEnvelope.VER11)
envelope.setOutputSoapObject(request)

// 4. Exécuter l'appel
val transport = HttpTransportSE(URL)
transport.call("", envelope)

// 5. Parser la réponse
val response = envelope.bodyIn as SoapObject
```

### Coroutines pour appels asynchrones
```kotlin
lifecycleScope.launch(Dispatchers.IO) {
    // Thread d'arrière-plan
    val data = service.getComptes()
    
    withContext(Dispatchers.Main) {
        // Thread principal (UI)
        adapter.updateComptes(data)
    }
}
```

---

## 📚 Documentation complète

Pour plus de détails, consultez:

1. **README.md** - Vue d'ensemble complète du projet
2. **KSOAP2_GUIDE.md** - Guide détaillé de ksoap2
3. **IMPLEMENTATION_SUMMARY.md** - Résumé de l'implémentation

---

## ✅ Test de validation

### Test manuel complet:

1. ✅ Lancer l'app
2. ✅ Vérifier que la liste se charge
3. ✅ Ajouter un compte COURANT (solde: 1000)
4. ✅ Vérifier qu'il apparaît dans la liste
5. ✅ Ajouter un compte EPARGNE (solde: 5000)
6. ✅ Supprimer un compte
7. ✅ Vérifier qu'il disparaît de la liste

Si tous les tests passent → ✅ **Application fonctionnelle !**

---

## 🆘 Besoin d'aide ?

1. **Gradle ne synchronise pas**
   - Vérifier la connexion Internet
   - Invalider les caches: File > Invalidate Caches > Restart

2. **Émulateur lent**
   - Utiliser un AVD avec API 33
   - Activer "Hardware acceleration"
   - Augmenter la RAM de l'émulateur

3. **App crash au lancement**
   - Vérifier les logs dans Logcat
   - Vérifier que le serveur SOAP est accessible
   - Nettoyer et recompiler le projet

---

## 🎉 Félicitations !

Vous avez maintenant une application Android fonctionnelle avec:
- ✅ Communication SOAP
- ✅ Interface Material Design
- ✅ Gestion asynchrone
- ✅ Architecture propre

**Prochaines étapes**:
- Implémenter la modification de compte
- Ajouter des tests unitaires
- Explorer Jetpack Compose
- Migrer vers REST (optionnel)

---

**Bon développement ! 🚀**

