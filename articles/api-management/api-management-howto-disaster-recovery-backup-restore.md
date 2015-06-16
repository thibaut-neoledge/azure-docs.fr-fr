<properties 
	pageTitle="Comment implémenter une récupération d'urgence à l'aide d'une sauvegarde de service et la récupérer dans Gestion des API Azure" 
	description="Apprenez à utiliser la sauvegarde et la restauration pour effectuer une récupération d'urgence dans Gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Comment implémenter une récupération d'urgence à l'aide d'une sauvegarde de service et la récupérer dans Gestion des API Azure

En choisissant de publier et de gérer vos API via la Gestion des API Azure, vous bénéficiez de nombreuses fonctionnalités de tolérance de panne et d'infrastructure que vous auriez à concevoir, implémenter et gérer sans ce service. La plateforme Azure permet de limiter une grande partie des risques de défaillance à moindres frais.

Pour récupérer à la suite de problèmes de disponibilité affectant la région où votre service Gestion des API est hébergé, vous devez être prêt à reconstituer votre service dans une autre région à tout moment. En fonction de vos objectifs de disponibilité et de temps de récupération, vous pouvez réserver un service de sauvegarde dans une ou plusieurs régions et tenter de maintenir la synchronisation de leur configuration et de leur contenu avec le service actif. La fonctionnalité de sauvegarde et de récupération de service fournit le bloc de construction nécessaire pour implémenter votre stratégie de récupération d'urgence.

Cette fonctionnalité est accessible via l'API REST de gestion des services. Voir la section [Authentification des demandes Gestionnaire de ressources Azure][Authentification des demandes Gestionnaire de ressources Azure] pour des instructions sur la marche à suivre pour accéder à l'API.

## Dans cette rubrique

-   [Sauvegarde d'un service Gestion des API][Sauvegarde d'un service Gestion des API]
-   [Récupération d'un service Gestion des API][Récupération d'un service Gestion des API]

## <a name="step1"> </a>Sauvegarde d'un service Gestion des API

Pour sauvegarder un service Gestion des API, envoyez la demande HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

où :

-   `subscriptionId` - ID de l'abonnement qui inclut le service Gestion des API que vous tentez de sauvegarder
-   `resourceGroupName` - une chaîne au format 'Api-Default-{service-region}', où `service-region` identifie la région Azure où est hébergé le service Gestion des API que vous tentez de sauvegarder ; par ex., `North-Central-US`
-   `serviceName` - le nom du service Gestion des API que vous tentez de sauvegarder, spécifié au moment de sa création
-   `api-version` - remplacé par `2014-02-14`

Dans le corps de la demande, spécifiez le compte de stockage Azure cible, la clé d'accès, le nom du conteneur d'objet blob et le nom de la sauvegarde :

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Définissez la valeur de l'en-tête de la demande `Content-Type` sur `application\json`.

La sauvegarde est une opération de longue durée qui peut prendre plusieurs minutes. Si la demande est réussie et que le processus de sauvegarde a été lancé, vous recevez un code d'état de réponse `202 Accepted` avec un en-tête `Location`. Envoyez des demandes « GET » à l'URL dans l'en-tête `Location` pour connaître l'état de l'opération. Lorsque la sauvegarde est en cours, vous continuez à recevoir le code d'état « 202 Accepted ». Un code de réponse `200 OK` indique que l'opération de sauvegarde a réussi.

**Remarque** :

-   Le **conteneur** spécifié dans le corps de la demande **doit exister**.
-   Lorsque la sauvegarde est en cours, **vous ne devez tenter aucune opération de gestion de services** (par ex., la mise à niveau vers une version supérieure/antérieure, la modification d'un nom de domaine, etc.).
-   La récupération d'une **sauvegarde n'est garantie que pendant 7 jours** à partir du moment de sa création.
-   Les **données d'utilisation** utilisées pour la création des rapports d'analyse **ne sont pas incluses** dans la sauvegarde. Utilisez l'[API REST de Gestion des API Azure][API REST de Gestion des API Azure] pour récupérer régulièrement les rapports d'analyse et les conserver en toute sécurité.
-   La fréquence à laquelle vous effectuez les sauvegardes du service affecte votre objectif de point de récupération. Afin de le réduire au maximum, nous vous conseillons d'implémenter des sauvegardes régulières, ainsi que des sauvegardes à la demande lorsque vous apportez des modifications importantes à votre service Gestion des API.
-   Les **modifications** de la configuration du service (par ex., API, stratégies, apparence du portail des développeurs) pendant qu'une opération de sauvegarde est en cours **peuvent ne pas être incluses dans la sauvegarde et donc être perdues**.

## <a name="step2"> </a>Récupération d'un service Gestion des API

Pour récupérer un service Gestion des API à partir d'une sauvegarde précédemment créée, envoyez la demande HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

où :

-   `subscriptionId` - ID de l'abonnement qui inclut le service Gestion des API que vous tentez de récupérer à partir d'une sauvegarde
-   `resourceGroupName` - une chaîne au format « Api-Default-{service-region} », où `service-region` identifie la région Azure où est hébergé le service Gestion des API que vous tentez de récupérer à partir d'une sauvegarde ; par ex., `North-Central-US`
-   `serviceName` - le nom du service Gestion des API à récupérer, spécifié au moment de sa création
-   `api-version` - remplacé par `2014-02-14`

Dans le corps de la demande, spécifiez l'emplacement du fichier de sauvegarde (c'est-à-dire du compte de stockage Azure), la clé d'accès, le nom du conteneur d'objet blob et le nom de la sauvegarde :

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Définissez la valeur de l'en-tête de la demande `Content-Type` sur `application\json`.

La récupération est une opération de longue durée qui peut prendre jusqu'à 30 minutes, voire plus. Si la demande a réussi et que le processus de récupération a été lancé, vous recevez un code d'état de réponse `202 Accepted` avec un en-tête `Location`. Envoyez des demandes « GET » à l'URL dans l'en-tête `Location` pour connaître l'état de l'opération. Lorsque la récupération est en cours, vous continuez à recevoir le code d'état « 202 Accepted ». Un code de réponse `200 OK` indique que l'opération de récupération a réussi.

**Remarque** :

-   Le **niveau** du service à récupérer **doit correspondre** à celui du service sauvegardé utilisé pour la récupération.
-   Les **modifications** de configuration du service (par ex., API, stratégies, apparence du portail des développeurs) pendant qu'une opération de sauvegarde est en cours **peuvent être écrasées**.

  [Authentification des demandes Gestionnaire de ressources Azure]: http://msdn.microsoft.com/library/dn790557.aspx
  [Sauvegarde d'un service Gestion des API]: #step1
  [Récupération d'un service Gestion des API]: #step2
  [API REST de Gestion des API Azure]: http://msdn.microsoft.com/library/azure/dn781421.aspx

<!--HONumber=46--> 
 