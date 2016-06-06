<properties
	pageTitle="Azure AD Connect : Résoudre les problèmes de connectivité | Microsoft Azure"
	description="Explique comment résoudre les problèmes de connectivité liés à Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="andkjell"/>

# Résoudre les problèmes de connectivité liés à Azure AD Connect
Cet article décrit le fonctionnement de la connectivité entre Azure AD Connect et Azure AD ainsi que la résolution des problèmes de connectivité. Ces problèmes sont susceptibles de se produire dans un environnement doté d’un serveur proxy.

## Résoudre les problèmes de connectivité dans l’Assistant Installation
Azure AD Connect utilise l’authentification moderne (à l’aide de la bibliothèque ADAL) pour l’authentification. L’Assistant d’installation et le moteur de synchronisation approprié requièrent que le fichier machine.config soit correctement configuré dans la mesure où il s’agit d’applications .NET.

Dans cet article, nous allons montrer comment Fabrikam se connecte à Azure AD via son proxy. Le serveur proxy est nommé fabrikamproxy et utilise le port 8080.

Nous devons d’abord nous assurer que le fichier [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) est correctement configuré. ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
Dans certains blogs non-Microsoft, il est indiqué qu’il convient plutôt d’apporter des modifications à miiserver.exe.config. Toutefois, ce fichier est remplacé à chaque mise à niveau. Même s’il fonctionne lors de l’installation initiale, le système s’arrêtera donc de fonctionner à la première mise à niveau. C’est pourquoi, il est préférable de mettre à jour le fichier machine.config.

Les URL requises doivent également être ouvertes dans le serveur proxy. La liste officielle est documentée dans [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Extrait de celle-ci, le tableau suivant indique le strict minimum pour pouvoir se connecter à Azure AD. Cette liste n’inclut pas de fonctionnalités facultatives, comme l’écriture différée du mot de passe ou Azure AD Connect Health. Elle est documentée ici pour faciliter le dépannage de la configuration initiale.

URL | Port | Description
---- | ---- | ----
mscrl.microsoft.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats.
*.verisign.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats.
*.entrust.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats pour MFA.
*.windows.net | HTTPS/443 | Permet de se connecter à Azure AD.
secure.aadcdn.microsoftonline-p.com | HTTPS/443 | Utilisé pour MFA.
*.microsoftonline.com | HTTPS/443 | Permet de configurer votre annuaire Azure AD et pour importer/exporter des données.

## Erreurs dans l’Assistant
L’Assistant Installation utilise deux contextes de sécurité différents. Dans la page **Connexion à Azure AD**, il utilise l’utilisateur actuellement connecté. Dans la page **Configurer**, il passe au [compte exécutant le service pour le moteur de synchronisation](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Les configurations de proxy que nous effectuons sont globales pour la machine. En cas de problème, ce dernier apparaît donc très probablement déjà dans la page **Connexion à Azure AD** de l’Assistant.

Voici les erreurs les plus courantes de l’Assistant Installation.

### L’Assistant Installation n’a pas été configuré correctement
Cette erreur s’affiche quand l’Assistant ne peut pas joindre le proxy. ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Si cette erreur apparaît, vérifiez que le fichier [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) a été configuré correctement.
- Si la configuration semble correcte, suivez les étapes de la section [Vérifier la connectivité du proxy](#verify-proxy-connectivity) pour voir si le problème existe également en dehors de l’Assistant.

### Impossible d’atteindre le point de terminaison de l’authentification MFA
Cette erreur s’affiche si le point de terminaison ****https://secure.aadcdn.microsoftonline-p.com** ne peut pas être atteint et que l’authentification MFA est activée chez votre administrateur général. ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Dans ce cas, vérifiez que le point de terminaison secure.aadcdn.microsoftonline-p.com a été ajouté au serveur proxy.

### Le mot de passe ne peut pas être vérifié
Si l’Assistant Installation réussit à se connecter à Azure AD, mais que le mot de passe proprement dit ne peut pas être vérifié, le message suivant s’affiche : ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- Le mot de passe est-il temporaire et doit-il être modifié ? Le mot de passe est-il correct ? Connectez-vous à https://login.microsoftonline.com (sur un ordinateur autre que le serveur Azure AD Connect) et vérifiez que le compte est utilisable.

### Vérifier la connectivité du proxy
Pour vérifier si le serveur Azure AD Connect dispose d’une connectivité réelle avec le proxy et Internet, nous allons utiliser certaines commandes PowerShell pour voir si le proxy autorise ou non les requêtes web. À une invite PowerShell, exécutez `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Techniquement, le premier appel s’adresse à https://login.microsoftonline.com, et il fonctionne également, mais la réactivité de l’autre URI est plus rapide.)

PowerShell utilise la configuration du fichier machine.config pour contacter le proxy. Les paramètres de winhttp/netsh ne doivent pas affecter ces applets de commande.

Si le proxy est configuré correctement, vous devez obtenir un état de réussite : ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Si vous recevez l’erreur **Impossible de se connecter au serveur distant**, cela signifie que PowerShell tente d’effectuer un appel direct sans utiliser le proxy ou que DNS n’est pas configuré correctement. Vérifiez que le fichier **machine.config** est configuré correctement. ![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Si le proxy n’est pas configuré correctement, une erreur apparaît : ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Erreur | Texte d’erreur | Commentaire
---- | ---- | ---- |
403 | Interdit | Le proxy n’a pas été ouvert pour l’URL demandée. Revisitez la configuration du proxy et vérifiez que les [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) ont été ouvertes.
407 | Authentification proxy requise | Une connexion au serveur proxy était requise, et aucune connexion n’a été établie. Si le serveur proxy requiert une authentification, assurez-vous que celle-ci a été configurée dans le fichier machine.config. Vérifiez également que vous utilisez des comptes de domaine pour l’utilisateur qui exécute l’Assistant, ainsi que pour le compte de service.

## Modèle de communication entre Azure AD Connect et Azure AD
Si vous avez suivi l’ensemble des étapes indiquées ci-dessus et que vous ne pouvez toujours pas vous connecter, vous pouvez commencer à examiner les journaux du réseau. Cette section documente un modèle de connectivité réussi et normal. Elle indique également les fausses pistes courantes qui peuvent être ignorées si vous lisez les journaux du réseau.

- Des appels sont effectués vers https://dc.services.visualstudio.com. Il n’est pas impératif que cette URL soit ouverte dans le proxy pour que l’installation réussisse, et vous pouvez ignorer ces appels.
- Vous constatez que la résolution DNS répertorie les hôtes réels de l’espace de noms DNS nsatc.net et d’autres espaces de noms ne figurant pas sous microsoftonline.com. Néanmoins, il n’existe pas de requêtes de service web sur les noms de serveur réels, et vous n’avez pas à les ajouter au proxy.
- Les points de terminaison adminwebservice et provisioningapi (voir les journaux ci-dessous) sont des points de terminaison de détection. Ils permettent de rechercher le point de terminaison réel à utiliser et sont différents en fonction de votre région.

### Journaux de proxy de référence
Voici une image mémoire d’un journal de proxy réel et la page de l’Assistant Installation d’où elle a été prise (les entrées en double pour un même point de terminaison ont été supprimées). Cette image peut être utilisée comme référence pour vos propres journaux de proxy et de réseau. Les points de terminaison réels peuvent être différents dans votre environnement (en particulier ceux qui sont en *italique*).

**Connexion à Azure AD**

Time | URL
--- | ---
11/01/2016 8:31 | connect://login.microsoftonline.com:443
11/01/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
11/01/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
11/01/2016 8:32 | connect://login.microsoftonline.com:443
11/01/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
11/01/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**Configurer**

Time | URL
--- | ---
11/01/2016 8:43 | connect://login.microsoftonline.com:443
11/01/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
11/01/2016 8:43 | connect://login.microsoftonline.com:443
11/01/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
11/01/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
11/01/2016 8:44 | connect://login.microsoftonline.com:443
11/01/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
11/01/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
11/01/2016 8:44 | connect://login.microsoftonline.com:443
11/01/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
11/01/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**Synchronisation initiale**

Time | URL
--- | ---
11/01/2016 8:48 | connect://login.windows.net:443
11/01/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
11/01/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
11/01/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

## Erreurs d’authentification
Cette section décrit les erreurs qui peuvent être retournées par la bibliothèque ADAL (la bibliothèque d’authentification utilisée par Azure AD Connect) et PowerShell. L’erreur expliquée doit vous aider à comprendre les étapes suivantes.

### Licence non valide
Nom d’utilisateur ou mot de passe non valide. Pour plus d’informations, consultez [Impossible de vérifier le mot de passe](#the-password-cannot-be-verified).

### Type d’utilisateur inconnu
Votre annuaire Azure AD est introuvable ou n’a pas pu être résolu. Vous essayez peut-être de vous connecter avec un nom d’utilisateur d’un domaine non vérifié ?

### Échec de la découverte de domaine d’utilisateur
Problèmes de configuration du réseau ou du proxy. Impossible d’accéder au réseau. Consultez [Résoudre les problèmes de connectivité dans l’Assistant Installation](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### Mot de passe utilisateur expiré
Vos informations d’identification ont expiré. Modifiez votre mot de passe.

### AuthorizationFailure
Problème inconnu.

### Authentification annulée
Le test d’authentification multifacteur (MFA) a été annulé.

### ConnectToMSOnline
L’authentification a réussi, mais Azure AD PowerShell a un problème d’authentification.

### AzureRoleMissing
L’authentification a réussi. Vous n’êtes pas administrateur global.

### PrivilegedIdentityManagement
L’authentification a réussi. Privileged Identity Management a été activé et vous n’êtes actuellement pas administrateur global. Pour plus d’informations, consultez [Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

### CompanyInfoUnavailable
L’authentification a réussi. Impossible de récupérer les informations de société à partir d’Azure AD.

### RetrieveDomains
L’authentification a réussi. Impossible de récupérer les informations de domaine à partir d’Azure AD.

## Étapes de dépannage pour les versions précédentes.
Dans les versions commençant par le numéro de build 1.1.105.0 (publiées en février 2016), l’assistant de connexion a été supprimé. Cette section et la configuration ne sont plus requises, mais sont conservées en tant que référence.

Pour que l’Assistant de connexion unique fonctionne, winhttp doit être configuré. Pour ce faire, utilisez [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity). ![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### L’Assistant de connexion n’a pas été configuré correctement
Cette erreur apparaît quand l’Assistant de connexion ne peut pas accéder au proxy ou quand le proxy n’autorise pas la demande. ![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Si cette erreur apparaît, examinez la configuration du proxy dans [netsh](active-directory-aadconnect-prerequisites.md#connectivity) et vérifiez si elle est correcte. ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Si la configuration semble correcte, suivez les étapes de la section [Vérifier la connectivité du proxy](#verify-proxy-connectivity) pour voir si le problème existe également en dehors de l’Assistant.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0525_2016-->