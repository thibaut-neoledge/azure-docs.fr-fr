<properties
 pageTitle="Authentification sortante de Scheduler"
 description="Authentification sortante de Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Authentification sortante de Scheduler

Les travaux de Scheduler peuvent avoir besoin de faire appel à des services qui requièrent une authentification. De cette façon, un service appelé peut déterminer si le travail de Scheduler peut accéder à ses ressources. Certains de ces services incluent d'autres services Azure, Salesforce.com, Facebook et des sites Web personnalisés sécurisés.

## Ajout et suppression de l'authentification

L'ajout de l'authentification à un travail de Scheduler est simple : il suffit d'ajouter un élément enfant JSON `authentication` à l'élément `request` lors de la création ou de la mise à jour d'un travail. Les secrets transmis au service de Scheduler dans une requête PUT, PATCH ou POST, dans le cadre de l'objet `authentication`, ne sont jamais retournés dans les réponses. Dans les réponses, les informations secrètes ont la valeur null ou peuvent disposer d'un jeton public qui représente l'entité authentifiée.

Pour supprimer l'authentification, exécutez PUT ou PATCH de façon explicite sur le travail, définissant l'objet `authentication` sur null. Vous ne verrez pas de propriétés d'authentification en réponse.

Actuellement, les seuls types d'authentification pris en charge sont le modèle `ClientCertificate` (pour utiliser les certificats client SSL/TLS), le modèle `Basic` (pour l'authentification de base) et le modèle `ActiveDirectoryOAuth` (pour l'authentification Active Directory OAuth).

## Corps de la requête pour l'authentification ClientCertificate

Lorsque vous ajoutez l'authentification à l'aide du modèle `ClientCertificate`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément|Description|
|:---|:---|
|_authentification (élément parent)_|Objet d'authentification pour l'utilisation d'un certificat client SSL.|
|_type_|Obligatoire. Type d'authentification. Pour les certificats client SSL, la valeur doit être `ClientCertificate`.|
|_pfx_|Obligatoire. Contenu codé en base64 du fichier PFX.|
|_mot de passe_|Obligatoire. Mot de passe pour accéder au fichier PFX.|


## Corps de la réponse pour l'authentification ClientCertificate

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'utilisation d'un certificat client SSL.|
|_type_ |Type d'authentification. Pour les certificats client SSL, la valeur est `ClientCertificate`.|
|_certificateThumbprint_ |L'empreinte numérique du certificat.|
|_certificateSubjectName_ |Le nom unique du sujet du certificat.|
|_certificateExpiration_ |La date d’expiration du certificat.|

## Corps de la requête pour l'authentification de base

Lorsque vous ajoutez l'authentification à l'aide du modèle `Basic`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification de base.|
|_type_ |Obligatoire. Type d'authentification. Pour l'authentification de base, la valeur doit être `Basic`.|
|_nom d'utilisateur_ |Obligatoire. Nom d'utilisateur à authentifier.|
|_mot de passe_ |Obligatoire. Mot de passe à authentifier.|

## Corps de la réponse pour l'authentification de base

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification de base.|
|_type_ |Type d'authentification. Pour l'authentification de base, la valeur est `Basic`.|
|_nom d'utilisateur_ |Le nom d'utilisateur authentifié.|

## Corps de la requête pour l'authentification ActiveDirectoryOAuth

Lorsque vous ajoutez l'authentification à l'aide du modèle `ActiveDirectoryOAuth`, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification ActiveDirectoryOAuth.|
|_type_ |Obligatoire. Type d'authentification. Pour l'authentification ActiveDirectoryOAuth, la valeur doit être `ActiveDirectoryOAuth`.|
|_locataire_ |Obligatoire. L’identificateur de client pour le client Azure AD.|
|_audience_ |Obligatoire. Cette option est définie sur https://management.core.windows.net/.|.
|_clientId_ |Obligatoire. Indiquer l'identifiant client pour l'application Azure AD.|
|_secret_ |Obligatoire. Secret du client qui demande le jeton.|

### Déterminer votre identificateur de client

Vous pouvez trouver l’identificateur de client pour le client Azure AD en exécutant `Get-AzureAccount` dans Azure PowerShell.

## Corps de la réponse pour l'authentification ActiveDirectoryOAuth

Lorsqu'une requête est envoyée avec des informations d'authentification, la réponse contient les éléments suivants liés à l'authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d'authentification pour l'authentification ActiveDirectoryOAuth.|
|_type_ |Type d'authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`.|
|_locataire_ |L’identificateur de client pour le client Azure AD. |
|_audience_ |Cette option est définie sur https://management.core.windows.net/.|
|_clientId_ |L'identifiant client pour l'application Azure AD.|

## Voir aussi


 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0420_2016-->