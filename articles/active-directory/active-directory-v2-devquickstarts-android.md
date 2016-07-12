<properties
	pageTitle="Application Android Azure AD v2.0 | Microsoft Azure"
	description="Génération d’une application Android qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leurs comptes professionnel ou scolaire et appelle l’API Graph à l’aide de bibliothèques tierces."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  Ajouter l’authentification dans une application Android à l’aide d’une bibliothèque tierce avec l’API Graph à l’aide du point de terminaison v2.0

La plateforme d’identité Microsoft utilise des normes ouvertes telles que OAuth2 et OpenID Connect. Cela permet aux développeurs de tirer parti de toute bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons rédigé quelques procédures pas à pas comme celle-ci pour présenter la configuration des bibliothèques tierces pour se connecter à la plateforme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) seront en mesure de se connecter à la plateforme Microsoft Identity.

Cette application permet à un utilisateur de se connecter à son entreprise, puis d’effectuer des recherches dans son entreprise à l’aide de l’API Graph.

Si vous découvrez OAuth2 ou OpenID Connect, cet exemple de configuration n’est peut-être pas très parlant pour vous. Nous vous recommandons de consulter une brève [vue d’ensemble du protocole que nous avons décrit ici](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
	Certaines fonctionnalités de notre plateforme qui ont une expression dans ces normes, comme la gestion de la stratégie d’accès conditionnel et d’Intune, requièrent l’utilisation de nos bibliothèques d’identité Microsoft Azure open source.


> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).


## Télécharger
Le code associé à ce didacticiel est stocké [sur GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip) ou la cloner :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Sinon, téléchargez et commencez dès maintenant :

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- ajouter la plateforme **Mobile** pour votre application ;
- copier l'**URI de redirection** à partir du portail. Vous devez utiliser la valeur par défaut de `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## Télécharger la bibliothèque tierce nxoauth2 et lancer un espace de travail

Pour cette procédure pas à pas, nous allons utiliser OIDCAndroidLib de GitHub, une bibliothèque OAuth2 basée sur le code OpenID Connect de Google. Elle implémente le profil de l’application native et prend en charge le point de terminaison de l’autorisation de l’utilisateur final. Voici tout ce dont nous aurons besoin pour l’intégration avec la plateforme d’identité de Microsoft.

*  Cloner

Commencez par cloner le référentiel OIDCAndroidLib sur votre ordinateur.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Configurer votre environnement Android Studio

*  Créez le projet. Créez un nouveau projet AndroidStudio et suivez l’Assistant par défaut.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  Paramétrez les modules du projet. Selon moi, pour cela le plus simple est de déplacer le référentiel cloné vers l’emplacement du projet. Vous pouvez également démarrer en créant le projet puis en le clonant directement dans l’emplacement du projet.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

Ensuite, ouvrez les paramètres des modules du projet dans le menu contextuel ou à l’aide du raccourci `Ctrl + Alt + Maj + S`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

Supprimez le module d’application par défaut pour conserver uniquement les paramètres de conteneur du projet.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

Ensuite, vous devez importer des modules à partir du référentiel cloné dans le projet actuel.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> Répétez ces étapes pour le module `oidlib-sample`.

Vérifiez les dépendances oidclib sur le module `oidlib-sample`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

Cliquez sur « OK » et attendez l’activité de synchronisation Gradle.

Votre fichier settings.gradle doit ressembler à ceci :

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  Générez l’exemple d’application pour vérifier qu’il s’exécute correctement.

Vous ne pourrez pas encore l’utiliser avec Azure Active Directory. Vous devez d’abord configurer certains points de terminaison. Cela permet de vérifier l’absence de problèmes au niveau d’Android Studio avant de commencer à personnaliser l’exemple d’application.

Générez et exécutez `oidlib-sample` comme cible dans Android Studio.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  Nettoyez.

Vous pouvez supprimer sans risque le répertoire `app ` conservé après la suppression du module du projet car AndroidStudio ne le supprime pas par sécurité.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

Vous pouvez également supprimer la configuration d’exécution également conservée lors de la suppression du module du projet en ouvrant le menu « Modifier les configurations ».

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## Configurer les points de terminaison de l’exemple

Maintenant que `oidlib-sample` s’exécute correctement, vous allez modifier quelques points de terminaison pour qu’il fonctionne avec Azure Active Directory.

* Configurez votre client.

Ouvrez le fichier `oidc_clientconf.xml` et effectuez les modifications suivantes :

1. Dans la mesure où nous utilisons seulement les flux OAuth2 pour obtenir un jeton et appeler l’API Graph, nous allons définir le client pour OAuth2 seulement. Nous verrons dans un autre exemple comment utiliser OIDC.

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. Configurez l’ID client que vous avez reçu à partir du portail d’inscription.

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. Configurez l’URI de redirection que vous avez reçue à partir du portail d’inscription.

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Configurez les étendues dont vous avez besoin pour accéder à l’API Graph.

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

Ici, nous définissons `oidc_scopes`. L’étendue que nous allons demander pour cette application est `User.ReadBasic.All` qui nous permet de lire le profil de base de tous les utilisateurs dans notre répertoire. Vous pouvez en savoir plus sur toutes les étendues disponibles pour [l’utilisation avec Microsoft Graph ici](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pour plus d’explications concernant `openid` ou `offline_access` en tant qu’étendues dans OpenID Connect, consultez la [brève vue d’ensemble du protocole que nous avons décrite ici](active-directory-v2-protocols-oauth-code.md).

* Configurer vos points de terminaison clients

Ouvrez le fichier `oidc_endpoints.xml` et effectuez les modifications suivantes :

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

Ces points de terminaison ne doivent jamais changer si vous utilisez OAuth2 comme protocole.

> [AZURE.NOTE] 
Les points de terminaison pour `userInfoEndpoint` et `revocationEndpoint` ne sont actuellement pas pris en charge par Azure Active Directory. Nous allons donc conserver les valeurs par défaut de example.com. Cela nous fournira un rappel utile non disponible dans l’exemple :-)


## Configurer un appel d’API Graph

Ouvrez le fichier `HomeActivity.java` et effectuez les modifications suivantes :

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

Ici, nous créons un appel d’API Graph qui renvoie les informations demandées.

## Vous avez terminé !

Vous avez apporté toutes les modifications nécessaires. Exécutez l’application `oidlib-sample` et cliquez sur Sign-in (Se connecter). O

Une fois que vous avez été authentifié, appuyez sur le bouton « Request Protected Resource » (Demander une ressource protégée) pour tester votre appel à l’API Graph.

## Obtenir des mises à jour de sécurité pour notre produit

Nous vous encourageons à recevoir les notifications des incidents de sécurité éventuels en visitant [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0629_2016-->