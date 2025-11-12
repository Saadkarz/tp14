# 🔍 Checklist de Dépannage

## ✅ Avant de commencer

### Vérifications préliminaires

- [ ] Java 17 installé (`java -version`)
- [ ] Android Studio installé (version 2022.1+)
- [ ] Projet ouvert dans Android Studio
- [ ] Internet fonctionnel (pour télécharger les dépendances)

---

## 🏗️ Problèmes de Build

### ❌ "Android Gradle plugin requires Java 17 to run"

**Diagnostic**: Gradle utilise Java 11 au lieu de Java 17

**Solutions** (essayez dans l'ordre):

**1. Via Android Studio** ⭐ Recommandé
```
File > Settings > Build, Execution, Deployment > Build Tools > Gradle
Gradle JDK: Sélectionner "jbr-17" ou tout JDK 17
Cliquer OK
File > Sync Project with Gradle Files
```

**2. Via gradle.properties**
```properties
# Ajouter cette ligne avec le chemin vers votre JDK 17
org.gradle.java.home=C:\\Program Files\\Java\\jdk-17
```

**3. Via variable d'environnement**
```cmd
# Windows CMD
set JAVA_HOME=C:\Program Files\Java\jdk-17
set PATH=%JAVA_HOME%\bin;%PATH%

# Vérifier
java -version
```

---

### ❌ "Unresolved reference: recyclerview"

**Diagnostic**: Dépendances non téléchargées

**Solution**:
```
1. File > Sync Project with Gradle Files
2. Attendre la fin de la synchronisation
3. Si ça ne marche pas: File > Invalidate Caches > Restart
```

---

### ❌ "Unresolved reference: ksoap2"

**Diagnostic**: Bibliothèque ksoap2 non téléchargée

**Solution**:
```
1. Vérifier app/build.gradle.kts contient:
   implementation("com.google.code.ksoap2-android:ksoap2-android:3.6.4")

2. Synchroniser:
   File > Sync Project with Gradle Files

3. Si échec, nettoyer:
   Build > Clean Project
   Build > Rebuild Project
```

---

### ❌ "Failed to resolve: androidx...."

**Diagnostic**: Problème de dépendances AndroidX

**Solution**:
```
1. Vérifier gradle.properties contient:
   android.useAndroidX=true
   android.enableJetifier=true

2. Synchroniser Gradle

3. Si problème persiste, supprimer:
   - Dossier .gradle/
   - Dossier .idea/
   - Fichier local.properties
   
4. Rouvrir le projet dans Android Studio
```

---

## 🌐 Problèmes de Connexion SOAP

### ❌ "java.net.ConnectException: Failed to connect"

**Diagnostic**: Application ne peut pas joindre le serveur SOAP

**Vérifications**:

**1. Serveur SOAP actif?**
```bash
# Tester avec curl
curl http://localhost:8082/services/ws?wsdl

# Devrait retourner du XML avec la description WSDL
```

**2. URL correcte dans Service.kt?**
```kotlin
// Pour émulateur Android
private val URL = "http://10.0.2.2:8082/services/ws"

// Pour appareil physique (remplacer par votre IP)
private val URL = "http://192.168.1.100:8082/services/ws"
```

**3. Permissions dans AndroidManifest.xml?**
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

**4. Cleartext traffic autorisé?**
```xml
<application
    android:usesCleartextTraffic="true"
    ...>
```

---

### ❌ "java.net.UnknownHostException"

**Diagnostic**: Nom d'hôte non résolu

**Solutions**:

**Pour émulateur**:
- Utiliser `10.0.2.2` au lieu de `localhost`
- Vérifier que le serveur tourne sur la machine hôte

**Pour appareil physique**:
- Utiliser l'IP locale de votre machine (ex: 192.168.1.100)
- Appareil et PC sur le même réseau WiFi
- Firewall Windows autorise le port 8082

**Trouver votre IP locale**:
```cmd
ipconfig
# Chercher "IPv4 Address" dans la section WiFi/Ethernet
```

---

### ❌ "java.net.SocketTimeoutException"

**Diagnostic**: Serveur trop lent ou inaccessible

**Solutions**:
1. Augmenter le timeout dans Service.kt:
```kotlin
val transport = HttpTransportSE(URL)
transport.setXmlVersionTag("<?xml version=\"1.0\" encoding=\"utf-8\"?>")
HttpTransportSE.debug = true
```

2. Vérifier que le serveur répond rapidement
3. Vérifier la connexion réseau

---

## 📱 Problèmes d'exécution

### ❌ App crash au lancement

**Solution 1: Voir les logs**
```
1. Dans Android Studio: View > Tool Windows > Logcat
2. Filtrer par "Error"
3. Chercher l'exception (ex: NullPointerException, NetworkOnMainThreadException)
```

**Solution 2: Erreurs courantes**

**NetworkOnMainThreadException**:
- Déjà géré par Coroutines dans MainActivity
- Si problème, vérifier que les appels SOAP sont dans `launch(Dispatchers.IO)`

**NullPointerException**:
- Vérifier que le serveur SOAP retourne bien des données
- Vérifier le parsing dans Service.kt

---

### ❌ "Aucun compte trouvé" alors qu'il y en a

**Diagnostic**: Problème de parsing de la réponse SOAP

**Débogage**:
```kotlin
// Dans Service.kt, méthode getComptes(), ajouter:
Log.d("SOAP", "Response: ${envelope.bodyIn}")
Log.d("SOAP", "Property count: ${response.propertyCount}")
```

**Vérifications**:
1. Format de date du serveur: `yyyy-MM-dd`
2. Noms des propriétés: `id`, `solde`, `dateCreation`, `type`
3. Type des valeurs: Long, Double, String, String

---

### ❌ Bouton "Ajouter" ne répond pas

**Diagnostic**: Dialog ne s'affiche pas ou crash

**Vérifications**:
1. Fichier `res/layout/popup.xml` existe
2. IDs corrects: `etSolde`, `radioCourant`, `radioEpargne`
3. Logs dans Logcat pour voir l'erreur

---

## 🎨 Problèmes d'Interface

### ❌ RecyclerView vide

**Diagnostic**: Adapter pas configuré ou données pas chargées

**Vérifications**:
```kotlin
// Dans MainActivity, onCreate():
- setupRecyclerView() est appelé
- loadComptes() est appelé
- adapter est assigné au RecyclerView
```

---

### ❌ Layout item_compte.xml ne s'affiche pas

**Diagnostic**: Fichier layout introuvable ou mal nommé

**Vérification**:
```
Fichier doit être: res/layout/item_compte.xml
Nom dans Adapter: R.layout.item_compte
```

---

### ❌ Boutons Modifier/Supprimer mal alignés

**Diagnostic**: Contraintes ConstraintLayout incorrectes

**Solution**: Vérifier item_compte.xml
```xml
<!-- Boutons doivent être contraints à droite -->
app:layout_constraintEnd_toEndOf="parent"
```

---

## 🔧 Utilitaires de Debug

### Activer les logs SOAP détaillés

Dans `Service.kt`:
```kotlin
val transport = HttpTransportSE(URL)
transport.debug = true

try {
    transport.call("", envelope)
    Log.d("SOAP_REQUEST", transport.requestDump)
    Log.d("SOAP_RESPONSE", transport.responseDump)
} catch (e: Exception) {
    Log.e("SOAP_ERROR", "Error: ${e.message}", e)
}
```

### Tester le serveur avec Postman

**1. Créer requête POST**
```
URL: http://localhost:8082/services/ws
Headers:
  Content-Type: text/xml; charset=utf-8
  SOAPAction: ""
```

**2. Body (raw XML)**
```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <ns:getComptes xmlns:ns="http://ws.soapAcount/" />
    </soap:Body>
</soap:Envelope>
```

### Nettoyer complètement le projet

```bash
# Depuis le terminal du projet
.\gradlew.bat clean

# Ou supprimer manuellement:
# - app/build/
# - .gradle/
# - .idea/
```

---

## 📋 Checklist finale

Avant de demander de l'aide, vérifiez:

- [ ] Java 17 installé et configuré dans Gradle
- [ ] Toutes les dépendances téléchargées (Sync Gradle OK)
- [ ] Aucune erreur dans "Build Output"
- [ ] Permissions INTERNET dans AndroidManifest.xml
- [ ] usesCleartextTraffic="true" dans AndroidManifest.xml
- [ ] Serveur SOAP démarré et accessible
- [ ] URL correcte dans Service.kt (10.0.2.2 pour émulateur)
- [ ] Logs Logcat vérifiés pour voir l'erreur exacte
- [ ] Clean + Rebuild Project effectué

---

## 🆘 Commandes utiles

### Gradle
```bash
# Nettoyer
.\gradlew.bat clean

# Compiler
.\gradlew.bat assembleDebug

# Installer sur appareil
.\gradlew.bat installDebug

# Voir les tâches disponibles
.\gradlew.bat tasks
```

### ADB (Android Debug Bridge)
```bash
# Lister les appareils
adb devices

# Voir les logs
adb logcat

# Filtrer les logs
adb logcat *:E  # Erreurs seulement
adb logcat | findstr "SOAP"  # Filtrer par tag

# Désinstaller l'app
adb uninstall ma.projet.soapcompteapp
```

---

## 📞 Resources d'aide

### Documentation
- [README.md](README.md) - Documentation complète
- [KSOAP2_GUIDE.md](KSOAP2_GUIDE.md) - Guide ksoap2
- [QUICK_START.md](QUICK_START.md) - Démarrage rapide

### Liens externes
- [Android Developers](https://developer.android.com)
- [ksoap2-android GitHub](https://github.com/simpligility/ksoap2-android)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/android)

---

## ✅ Test de validation final

Si tout fonctionne, vous devriez pouvoir:

1. ✅ Compiler sans erreur
2. ✅ Lancer l'app sur émulateur
3. ✅ Voir la liste des comptes (même vide)
4. ✅ Ouvrir le dialog "Ajouter"
5. ✅ Ajouter un compte (si serveur OK)
6. ✅ Supprimer un compte (si serveur OK)

**Si tout passe → Application fonctionnelle ! 🎉**

---

**Dernière mise à jour**: Novembre 2025

