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
	ms.date="09/15/2015" 
	ms.author="sdanie"/>

# Comment implémenter une récupération d'urgence à l'aide d'une sauvegarde de service et la récupérer dans Gestion des API Azure

En choisissant de publier et de gérer vos API via la Gestion des API Azure, vous bénéficiez de nombreuses fonctionnalités de tolérance de panne et d'infrastructure que vous auriez à concevoir, implémenter et gérer sans ce service. La plateforme Azure permet de limiter une grande partie des risques de défaillance à moindres frais.

Pour récupérer à la suite de problèmes de disponibilité affectant la région où votre service Gestion des API est hébergé, vous devez être prêt à reconstituer votre service dans une autre région à tout moment. En fonction de vos objectifs de disponibilité et de temps de récupération, vous pouvez réserver un service de sauvegarde dans une ou plusieurs régions et tenter de maintenir la synchronisation de leur configuration et de leur contenu avec le service actif. La fonctionnalité de sauvegarde et de récupération de service fournit le bloc de construction nécessaire pour implémenter votre stratégie de récupération d'urgence.

Ce guide montre comment authentifier des demandes Azure Resource Manager et comment sauvegarder et restaurer vos instances de service de gestion des API.

>[AZURE.NOTE]Le processus de sauvegarde et de restauration d'une instance de service de gestion des API pour la récupération d'urgence permet également de répliquer les instances de service de gestion des API dans les scénarios intermédiaires.

## Demandes d'authentification Azure Resource Manager

>[AZURE.IMPORTANT]L'API REST pour la sauvegarde et la restauration utilise Azure Resource Manager et dispose d'un autre mécanisme d'authentification que pour les API REST pour la gestion de vos entités de gestion des API. Les étapes de cette section décrivent comment authentifier les requêtes Azure Resource Manager. Pour plus d’informations, consultez [Demandes d'authentification Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Toutes les tâches que vous effectuez sur les ressources à l'aide d’Azure Resource Manager doivent être authentifiées avec Azure Active Directory en procédant comme suit.

-	Ajoutez une application au locataire Azure Active Directory.
-	Définissez les autorisations pour l'application que vous avez ajoutée.
-	Obtenez le jeton d'authentification des demandes pour Azure Resource Manager.

La première étape consiste à créer une application Azure Active Directory. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/) à l'aide de l'abonnement qui contient votre instance de service de gestion des API et accédez à l’onglet **Applications** pour votre Azure Active Directory par défaut.

>[AZURE.NOTE]Si le répertoire par défaut de Azure Active Directory n'est pas visible sur votre compte, contactez l'administrateur de l'abonnement Azure pour accorder les autorisations requises de votre compte. Pour plus d'informations sur l'emplacement de votre répertoire par défaut, consultez [Localiser votre répertoire par défaut dans le portail Azure](../virtual-machines/resource-group-create-work-id-from-persona.md/#locate-your-default-directory-in-the-azure-portal).

![Création d’une application Azure Active Directory][api-management-add-aad-application]

Cliquez sur **Ajouter**, **Ajouter une application développée par mon organisation**, puis choisissez **Application cliente native**. Saisissez un nom descriptif puis cliquez sur la flèche Suivant. Entrez une URL d'espace réservé telle que `http://resources` pour l’**URI de redirection**, étant donné qu’il s’agit d’un champ obligatoire, mais la valeur ne sera pas utilisée par la suite. Cliquez sur la case à cocher pour enregistrer l'application.

Une fois l’application enregistrée, cliquez sur **Configurer**, faites défiler la page vers le bas jusqu’à la section **Autorisations à d’autres applications** et cliquez sur **Ajouter une application**.

![Ajout d’autorisations][api-management-aad-permissions-add]

Sélectionnez **Windows** **API de gestion des services Azure** et cliquez sur la case à cocher pour ajouter l'application.

![Ajout d’autorisations][api-management-aad-permissions]

Cliquez sur **Autorisations déléguées** à côté de la nouvelle application **Windows**, **API de gestion des services Azure**, cochez la case **Accès à la gestion des services Azure (aperçu)**, puis cliquez sur **Enregistrer**.

![Ajout d’autorisations][api-management-aad-delegated-permissions]

Avant d'appeler les API qui génèrent la sauvegarde et permettent la restauration, il est nécessaire obtenir un jeton. L'exemple suivant utilise le package nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pour récupérer le jeton.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System;

	namespace GetTokenResourceManagerRequests
	{
        class Program
	    {
	        static void Main(string[] args)
	        {
	            var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
	            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

	            if (result == null) {
	                throw new InvalidOperationException("Failed to obtain the JWT token");
	            }

	            Console.WriteLine(result.AccessToken);

	            Console.ReadLine();
	        }
    	}
	}

Remplacez `{tentand id}`, `{application id}`, et `{redirect uri}` en suivant les instructions suivantes.

Remplacez `{tenant id}` avec l'ID client de l'application Azure Active Directory que vous venez de créer. Vous pouvez accéder à l'ID en cliquant sur **Afficher les points de terminaison**.

![Points de terminaison][api-management-aad-default-directory]

![Points de terminaison][api-management-endpoint]

Remplacez `{application id}` et `{redirect uri}` à l'aide de l’**ID Client** et l'URL de la section des **URI de redirection** à partir de l’onglet **Configurer** de votre application Azure Active Directory.

![Ressources][api-management-aad-resources]

Une fois que les valeurs sont spécifiées, l'exemple de code doit renvoyer un jeton similaire à l'exemple suivant.

![Le chiffrement][api-management-arm-token]

Avant d'appeler les opérations de sauvegarde et de restauration décrites dans les sections suivantes, définissez l'en-tête de demande d'autorisation de votre appel REST.

	request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Sauvegarde d’un service Gestion des API
Pour sauvegarder un service Gestion des API, envoyez la demande HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

où :

* `subscriptionId` : ID de l’abonnement qui inclut le service Gestion des API que vous tentez de sauvegarder
* `resourceGroupName` : chaîne au format « Api-Default-{service-region} », où `service-region` identifie la région Azure où est hébergé le service Gestion des API que vous tentez de sauvegarder ; par exemple `North-Central-US`
* `serviceName` : nom du service Gestion des API que vous tentez de sauvegarder, spécifié au moment de sa création
* `api-version` : remplacez par `2014-02-14`

Dans le corps de la demande, spécifiez le nom du compte de stockage Azure cible, la clé d’accès, le nom du conteneur d’objets blob et le nom de la sauvegarde :

	'{  
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

Définissez la valeur de l’en-tête de la demande `Content-Type` sur `application/json`.

La sauvegarde est une opération de longue durée qui peut prendre plusieurs minutes. Si la demande a réussi et que le processus de sauvegarde a été lancé, vous recevez un code d’état de réponse `202 Accepted` avec un en-tête `Location`. Envoyez des demandes « GET » à l’URL dans l’en-tête `Location` pour connaître l’état de l’opération. Lorsque la sauvegarde est en cours, vous continuez à recevoir le code d'état « 202 Accepted ». Un code de réponse `200 OK` indique que l’opération de sauvegarde a réussi.

**Remarque** :

- Le **conteneur** spécifié dans le corps de la demande **doit exister**.
* Lorsque la sauvegarde est en cours, **vous ne devez tenter aucune opération de gestion des services** telle que la mise à niveau vers une version supérieure/antérieure, la modification d'un nom de domaine, etc. 
* La récupération d'une **sauvegarde n'est garantie que pendant 7 jours** à partir du moment de sa création. 
* Les **données d'utilisation** utilisées pour la création des rapports d'analyse **ne sont pas incluses** dans la sauvegarde. Utilisez l'[API REST de Gestion des API Azure][] pour récupérer régulièrement les rapports d'analyse et les conserver en toute sécurité.
* La fréquence à laquelle vous effectuez les sauvegardes du service affecte votre objectif de point de récupération. Afin de le réduire au maximum, nous vous conseillons d'implémenter des sauvegardes régulières, ainsi que des sauvegardes à la demande lorsque vous apportez des modifications importantes à votre service Gestion des API.
* Les **modifications** de la configuration du service (par ex., API, stratégies, apparence du portail des développeurs) pendant qu'une opération de sauvegarde est en cours **peuvent ne pas être incluses dans la sauvegarde et donc être perdues**.

## <a name="step2"> </a>Récupération d’un service Gestion des API
Pour récupérer un service Gestion des API à partir d'une sauvegarde précédemment créée, envoyez la demande HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

où :

* `subscriptionId` : ID de l’abonnement qui inclut le service Gestion des API que vous tentez de récupérer à partir d’une sauvegarde
* `resourceGroupName` : chaîne au format « Api-Default-{service-region} », où `service-region` identifie la région Azure où est hébergé le service Gestion des API que vous tentez de récupérer à partir d’une sauvegarde ; par exemple `North-Central-US`
* `serviceName` : nom du service Gestion des API à récupérer, spécifié au moment de sa création
* `api-version` : remplacez par `2014-02-14`

Dans le corps de la demande, spécifiez l’emplacement du fichier de sauvegarde (c’est-à-dire le nom du compte de stockage Azure), la clé d’accès, le nom du conteneur d’objets blob et le nom de la sauvegarde :

	'{  
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

Définissez la valeur de l’en-tête de la demande `Content-Type` sur `application/json`.

La récupération est une opération de longue durée qui peut prendre jusqu'à 30 minutes, voire plus. Si la demande a réussi et que le processus de récupération a été lancé, vous recevez un code d’état de réponse `202 Accepted` avec un en-tête `Location`. Envoyez des demandes « GET » à l’URL dans l’en-tête `Location` pour connaître l’état de l’opération. Lorsque la récupération est en cours, vous continuez à recevoir le code d'état « 202 Accepted ». Un code de réponse `200 OK` indique que l’opération de récupération a réussi.

>[AZURE.IMPORTANT]Le **SKU** du service à restaurer **doit correspondre** à celui du service sauvegardé utilisé pour la restauration.
>
>Les **modifications** de configuration du service (par ex., API, stratégies, apparence du portail des développeurs) pendant qu'une opération de sauvegarde est en cours **peuvent être écrasées**.

## Étapes suivantes
Consultez les blogs Microsoft suivants pour les deux procédures pas à pas différent du processus de sauvegarde et de restauration.

-	[Répliquer des comptes de gestion des API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
	-	Merci à Gisela pour sa contribution à cet article.
-	[Gestion des API Azure : sauvegarde et restauration de la configuration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
	-	L'approche détaillée par Stuart ne correspond pas à l'aide officielle mais est très intéressante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[API REST de Gestion des API Azure]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 

<!---HONumber=Sept15_HO3-->