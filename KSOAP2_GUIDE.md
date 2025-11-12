# Guide d'utilisation de ksoap2 dans l'application

## Qu'est-ce que ksoap2 ?

ksoap2-android est une bibliothèque légère qui permet aux applications Android de communiquer avec des services web SOAP. SOAP (Simple Object Access Protocol) est un protocole de communication basé sur XML utilisé pour échanger des messages structurés entre applications.

## Installation de ksoap2

La bibliothèque est ajoutée dans le fichier `app/build.gradle.kts` :

```kotlin
implementation("com.google.code.ksoap2-android:ksoap2-android:3.6.4")
```

## Architecture d'un appel SOAP avec ksoap2

Un appel SOAP avec ksoap2 se décompose en 4 étapes principales :

### 1. Créer la requête SOAP (SoapObject)

```kotlin
val request = SoapObject(NAMESPACE, METHOD_NAME)
request.addProperty("paramName", paramValue)
```

- **NAMESPACE** : L'espace de noms du service SOAP (ex: "http://ws.soapAcount/")
- **METHOD_NAME** : Le nom de la méthode à appeler (ex: "getComptes")
- **addProperty()** : Ajoute des paramètres à la requête

### 2. Créer l'enveloppe SOAP (SoapSerializationEnvelope)

```kotlin
val envelope = SoapSerializationEnvelope(SoapEnvelope.VER11).apply {
    dotNet = false  // false pour les services Java, true pour .NET
    setOutputSoapObject(request)
}
```

L'enveloppe encapsule la requête SOAP et gère la sérialisation/désérialisation.

### 3. Créer le transport HTTP (HttpTransportSE)

```kotlin
val transport = HttpTransportSE(URL)
```

Le transport gère la communication HTTP avec le serveur SOAP.

### 4. Exécuter l'appel et traiter la réponse

```kotlin
try {
    transport.call("", envelope)  // Le premier paramètre est le SOAPAction (souvent vide)
    val response = envelope.bodyIn as SoapObject
    // Traiter la réponse
} catch (e: Exception) {
    e.printStackTrace()
}
```

## Exemples d'implémentation dans le projet

### Exemple 1 : Récupérer la liste des comptes (getComptes)

```kotlin
fun getComptes(): List<Compte> {
    // 1. Créer la requête
    val request = SoapObject(NAMESPACE, METHOD_GET_COMPTES)
    
    // 2. Créer l'enveloppe
    val envelope = SoapSerializationEnvelope(SoapEnvelope.VER11).apply {
        dotNet = false
        setOutputSoapObject(request)
    }
    
    // 3. Créer le transport
    val transport = HttpTransportSE(URL)
    val comptes = mutableListOf<Compte>()
    
    try {
        // 4. Exécuter l'appel
        transport.call("", envelope)
        val response = envelope.bodyIn as SoapObject
        
        // Parser la réponse
        for (i in 0 until response.propertyCount) {
            val soapCompte = response.getProperty(i) as SoapObject
            val compte = Compte(
                id = soapCompte.getPropertySafelyAsString("id")?.toLongOrNull(),
                solde = soapCompte.getPropertySafelyAsString("solde")?.toDoubleOrNull() ?: 0.0,
                dateCreation = SimpleDateFormat("yyyy-MM-dd", Locale.getDefault())
                    .parse(soapCompte.getPropertySafelyAsString("dateCreation") ?: "") ?: Date(),
                type = TypeCompte.valueOf(soapCompte.getPropertySafelyAsString("type") ?: "COURANT")
            )
            comptes.add(compte)
        }
    } catch (e: Exception) {
        e.printStackTrace()
    }
    
    return comptes
}
```

### Exemple 2 : Créer un compte (createCompte)

```kotlin
fun createCompte(solde: Double, type: TypeCompte): Boolean {
    // 1. Créer la requête avec paramètres
    val request = SoapObject(NAMESPACE, METHOD_CREATE_COMPTE).apply {
        addProperty("solde", solde)
        addProperty("type", type.name)
    }
    
    // 2. Créer l'enveloppe
    val envelope = SoapSerializationEnvelope(SoapEnvelope.VER11).apply {
        dotNet = false
        setOutputSoapObject(request)
    }
    
    // 3. Créer le transport
    val transport = HttpTransportSE(URL)
    
    return try {
        // 4. Exécuter l'appel
        transport.call("", envelope)
        true  // Succès
    } catch (e: Exception) {
        e.printStackTrace()
        false  // Échec
    }
}
```

### Exemple 3 : Supprimer un compte (deleteCompte)

```kotlin
fun deleteCompte(id: Long): Boolean {
    // 1. Créer la requête avec un paramètre
    val request = SoapObject(NAMESPACE, METHOD_DELETE_COMPTE).apply {
        addProperty("id", id)
    }
    
    // 2. Créer l'enveloppe
    val envelope = SoapSerializationEnvelope(SoapEnvelope.VER11).apply {
        dotNet = false
        setOutputSoapObject(request)
    }
    
    // 3. Créer le transport
    val transport = HttpTransportSE(URL)
    
    return try {
        // 4. Exécuter l'appel
        transport.call("", envelope)
        envelope.response as? Boolean ?: true
    } catch (e: Exception) {
        e.printStackTrace()
        false
    }
}
```

## Fonction utilitaire : getPropertySafelyAsString

Pour éviter les erreurs lors de la récupération des propriétés :

```kotlin
fun SoapObject.getPropertySafelyAsString(name: String): String? {
    return try {
        this.getProperty(name)?.toString()
    } catch (e: Exception) {
        null
    }
}
```

Cette fonction extension :
- Tente de récupérer une propriété du SoapObject
- Retourne null en cas d'erreur au lieu de crasher l'application
- Permet un traitement sûr avec l'opérateur Elvis (?:)

## Appels asynchrones avec Coroutines

Les appels réseau doivent être exécutés dans un thread d'arrière-plan. On utilise les Coroutines Kotlin :

```kotlin
lifecycleScope.launch(Dispatchers.IO) {
    // Appel SOAP dans un thread d'arrière-plan
    val comptes = service.getComptes()
    
    // Mise à jour de l'UI dans le thread principal
    withContext(Dispatchers.Main) {
        adapter.updateComptes(comptes)
    }
}
```

- **Dispatchers.IO** : Thread optimisé pour les opérations I/O (réseau, fichiers)
- **Dispatchers.Main** : Thread principal (UI thread)
- **withContext()** : Change de contexte pour mettre à jour l'UI

## Structure d'un message SOAP

### Requête SOAP typique

```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <ns:createCompte xmlns:ns="http://ws.soapAcount/">
            <solde>1000.0</solde>
            <type>COURANT</type>
        </ns:createCompte>
    </soap:Body>
</soap:Envelope>
```

### Réponse SOAP typique

```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <ns:createCompteResponse xmlns:ns="http://ws.soapAcount/">
            <return>true</return>
        </ns:createCompteResponse>
    </soap:Body>
</soap:Envelope>
```

ksoap2 gère automatiquement la création et le parsing de ces messages XML.

## Configuration pour l'émulateur Android

### URL du serveur

```kotlin
private val URL = "http://10.0.2.2:8082/services/ws"
```

- **10.0.2.2** : Adresse spéciale de l'émulateur Android qui pointe vers localhost de la machine hôte
- Pour un appareil physique, utilisez l'adresse IP réelle de votre machine (ex: "http://192.168.1.100:8082/services/ws")

### Permissions nécessaires

Dans `AndroidManifest.xml` :

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Et pour autoriser HTTP (non sécurisé) en développement :

```xml
<application
    android:usesCleartextTraffic="true"
    ...>
```

## Gestion des erreurs

### Types d'erreurs courants

1. **Connexion réseau** : Serveur inaccessible
2. **Parsing XML** : Réponse malformée
3. **Timeout** : Serveur trop lent
4. **Propriété manquante** : Champ absent dans la réponse

### Pattern de gestion d'erreur

```kotlin
try {
    transport.call("", envelope)
    val response = envelope.bodyIn as SoapObject
    // Traitement de la réponse
} catch (e: Exception) {
    e.printStackTrace()
    // Log détaillé de l'erreur
    when (e) {
        is IOException -> Log.e("SOAP", "Erreur réseau: ${e.message}")
        is XmlPullParserException -> Log.e("SOAP", "Erreur parsing XML: ${e.message}")
        else -> Log.e("SOAP", "Erreur inconnue: ${e.message}")
    }
}
```

## Débogage

### Activer les logs détaillés

Pour voir les requêtes et réponses SOAP :

```kotlin
transport.debug = true
transport.call("", envelope)
Log.d("SOAP_REQUEST", transport.requestDump)
Log.d("SOAP_RESPONSE", transport.responseDump)
```

### Tester avec Postman ou SoapUI

Avant d'intégrer dans l'application, testez vos services SOAP avec des outils dédiés :
- **Postman** : Supporte les requêtes SOAP
- **SoapUI** : Outil spécialisé pour SOAP

## Avantages de ksoap2

✅ **Léger** : Optimisé pour Android (taille réduite)  
✅ **Simple** : API intuitive pour SOAP  
✅ **Compatible** : Support SOAP 1.1 et 1.2  
✅ **Flexible** : Gestion des types complexes  
✅ **Stable** : Bibliothèque mature et éprouvée  

## Limitations et alternatives

### Limitations de ksoap2
- Pas de support natif pour WS-Security avancé
- Documentation parfois limitée
- Projet peu maintenu (dernière version 2014)

### Alternatives modernes
- **Retrofit avec SimpleXML** : Pour les APIs REST/SOAP
- **CXF** : Client SOAP complet (plus lourd)
- **Migrer vers REST** : API plus moderne si possible

## Ressources

- [Documentation ksoap2](https://github.com/simpligility/ksoap2-android)
- [Spécification SOAP 1.1](https://www.w3.org/TR/2000/NOTE-SOAP-20000508/)
- [Guide Android sur les appels réseau](https://developer.android.com/training/basics/network-ops)

## Résumé

ksoap2 simplifie l'intégration de services SOAP dans Android en :
1. Encapsulant la construction des requêtes XML
2. Gérant le transport HTTP
3. Désérialisant les réponses XML

Pour chaque appel SOAP, suivez ces 4 étapes :
1. **SoapObject** : Créer la requête
2. **SoapSerializationEnvelope** : Créer l'enveloppe
3. **HttpTransportSE** : Créer le transport
4. **call() + parse** : Exécuter et traiter

