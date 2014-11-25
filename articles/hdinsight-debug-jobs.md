<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Débogage de Hadoop dans HDInsight : messages d'erreur | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Débogage de Hadoop dans HDInsight : messages d'erreur

##Introduction
Les messages d'erreur répertoriés dans cette rubrique visent à aider les utilisateurs de Hadoop dans Azure HDInsight à comprendre les conditions d'erreur auxquelles ils sont susceptibles d'être confrontés lorsqu'ils administrent le service avec Azure PowerShell et à les conseiller sur les mesures à prendre pour la récupération à la suite d'une erreur. 

Certains de ces messages d'erreur peuvent également apparaître dans le portail Azure lorsqu'il sert à gérer les clusters HDinsight. Mais d'autres messages d'erreur que vous pouvez rencontrer ici sont moins granulaires, en raison des contraintes qui s'appliquent aux mesures correctives possibles dans ce contexte. D'autres encore sont fournis dans les contextes où l'atténuation est évidente. Si les contraintes sur les paramètres sont violées, par exemple, le message s'affiche à droite de la zone dans laquelle la valeur a été entrée. Dans le cas suivant, un trop grand nombre de nœuds de données a été demandé. La solution consiste à réduire ce nombre à une valeur autorisée, c'est-à-dire 22 ou moins.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Les erreurs auxquelles un utilisateur peut être confronté dans Azure PowerShell ou dans le portail Azure sont répertoriées dans l'ordre alphabétique des noms dans la section [Erreurs HDInsight](#hdinsight-error-messages). Chaque erreur répertoriée est liée à une entrée de la section [Description et atténuation des erreurs](#discription-mitigation-errors) qui fournit les informations suivantes sur l'erreur :
 	
- **Description** : message d'erreur visible pour les utilisateurs	
- **Atténuation** : mesures à prendre pour la récupération à la suite de l'erreur. 

###Erreurs HDInsight

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
[AzureRegionNotSupported](#AzureRegionNotSupported)		
[ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)	 
[ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)		
[ClusterNameUnavailable](#ClusterNameUnavailable)	
[ClusterUserNameInvalid](#ClusterUserNameInvalid)	
[ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord)	
[ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)	
[DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)	
[DeploymentDeletionFailure](#DeploymentDeletionFailure)	
[DnsMappingNotFound](#DnsMappingNotFound)	
[DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest)	
[DuplicateClusterInHostedService](#DuplicateClusterInHostedService)		
[FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus)		
[HdiRestoreClusterAltered](#HdiRestoreClusterAltered)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	 
[HostedServiceCreationFailure](#HostedServiceCreationFailure)	
[HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment)	
[HostedServiceNotFound](#HostedServiceNotFound)		
[HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment)		
[InsufficientResourcesCores](#InsufficientResourcesCores)	
[InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices)		
[InternalErrorRetryRequest](#InternalErrorRetryRequest)		
[InvalidAzureStorageLocation](#InvalidAzureStorageLocation)		
[InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode)	
[InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode)	
[InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion)	
[InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName)	
[InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey)	
[InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat)	
[MoreThanOneHeadNode](#MoreThanOneHeadNode)	
[OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest)	
[ParameterNullOrEmpty](#ParameterNullOrEmpty)	
[PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)	
[RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable)	
[StorageAccountNotColocated](#StorageAccountNotColocated)	
[SubscriptionIdNotActive](#SubscriptionIdNotActive)	
[SubscriptionIdNotFound](#SubscriptionIdNotFound)	
[UnableToResolveDNS](#UnableToResolveDNS)	
[UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource)	
[VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable)	
[VersionNotSupported](#VersionNotSupported)	
[VersionNotSupportedInRegion](#VersionNotSupportedInRegion)	
[WasbAccountConfigNotFound](#WasbAccountConfigNotFound)	



<h2><a id="discription-mitigation-errors"></a>Diagnostic et atténuation des erreurs</h2> 


<h3><a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided</h3>
- **Description** : fournissez des informations détaillées sur la base de données SQL Azure pour au moins un composant afin d'utiliser les paramètres personnalisés pour les metastores Hive et Oozie.   
- **Atténuation** : vous devez fournir un metastore SQL Azure correct et relancer la requête.  

<h3><a id="AzureRegionNotSupported"></a>AzureRegionNotSupported</h3>
- **Description** : impossible de créer un cluster dans la région *nom_région*. Utilisez une région HDInsight valide et relancez la requête.   
- **Atténuation** : créez le cluster dans une région qui prend actuellement en charge les clusters : Asie du Sud-Est, Europe du Nord, Europe de l'Ouest, Est des États-Unis ou Ouest des États-Unis.  

<h3><a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound</h3>
- **Description** : le serveur n'a pas pu trouver l'enregistrement de cluster demandé.  
- **Atténuation** : retentez l'opération. 

<h3><a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord</h3>
- **Description** : le nom DNS du cluster *nom_DNS* est incorrect. Assurez-vous que le nom commence et se termine par un caractère alphanumérique et contient uniquement le caractère spécial '-'.
- **Atténuation** : assurez-vous que vous avez utilisé pour votre cluster un nom DNS correct, qui commence et se termine par un caractère alphanumérique et ne contient aucun caractère spécial à l'exception du tiret '-', puis retentez l'opération.

<h3><a id="ClusterNameUnavailable"></a>ClusterNameUnavailable</h3>
- **Description** : le nom de cluster *nom_cluster* n'est pas disponible. Choisissez un autre nom.  
- **Atténuation** : vous devez spécifier un nom de cluster qui est unique et qui n'existe pas, puis refaire une tentative. Si vous utilisez le portail, l'interface utilisateur vous avertira si un nom de cluster est déjà utilisé pendant la procédure de création. 
 

<h3><a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid</h3>
- **Description** : le mot de passe de cluster n'est pas valide. Le mot de passe doit avoir une longueur d'au moins 10 caractères et contenir au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère spécial, sans espace. Par ailleurs, il ne doit pas contenir le nom d'utilisateur.  
- **Atténuation** : fournissez un mot de passe de cluster correct et retentez l'opération. 

<h3><a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid</h3>
- **Description** : le nom d'utilisateur du cluster est incorrect. Assurez-vous que le nom d'utilisateur ne contient pas de caractères spéciaux ni d'espaces.  
- **Atténuation** : fournissez un nom d'utilisateur de cluster correct et retentez l'opération.

<h3><a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord</h3>
- **Description** : le nom DNS du cluster *nom_DNS_cluster* est incorrect. Assurez-vous que le nom commence et se termine par un caractère alphanumérique et contient uniquement le caractère spécial '-'.
- **Atténuation** : fournissez un nom DNS de cluster correct et retentez l'opération.

<h3><a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName</h3>
- **Description** : le nom du conteneur dans l'URI *URI_conteneur* et le nom DNS *nom_DNS* dans le corps de la requête doivent être identiques.  
- **Atténuation** : assurez-vous que le nom de votre conteneur et le nom de votre DNS sont identiques, puis retentez l'opération.

<h3><a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound</h3>
- **Description** : configuration de cluster incorrecte. Impossible de trouver des définitions de nœud de données dans la taille de nœud.  
- **Atténuation** : retentez l'opération.

<h3><a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure</h3> 	
- **Description** : échec de la suppression du déploiement pour le cluster
- **Atténuation** : retentez l'opération.

<h3><a id="DnsMappingNotFound"></a>DnsMappingNotFound</h3> 
- **Description** : erreur de configuration du service. Informations de mappage DNS requises introuvables.  
- **Atténuation** : supprimez le cluster et recréez-en un.

<h3><a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest</h3>
- **Description** : tentative de création d'un conteneur de cluster en double. Il existe un enregistrement pour *nom_conteneur*, mais les valeurs Etag ne correspondent pas.   
- **Atténuation** : fournissez un nom unique pour le conteneur et retentez l'opération de création. 

<h3><a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService</h3>
- **Description** : le service hébergé *nom_service_hébergé* contient déjà un cluster. Un service hébergé ne peut pas contenir plusieurs clusters.
- **Atténuation** : hébergez le cluster dans un autre service hébergé. 

<h3><a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus</h3>
- **Description** : le serveur n'a pas pu mettre à jour l'état du déploiement du cluster.  
- **Atténuation** : retentez l'opération. Si le problème se reproduit plusieurs fois, contactez CSS. 

<h3><a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered</h3>
- **Description** : Le cluster *nom_cluster* a été supprimé dans le cadre de la maintenance. Recréez le cluster.     
- **Atténuation** : recréez le cluster.

<h3><a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound</h3>
- **Description** : configuration de cluster incorrecte. Configuration de nœud principal introuvable dans les tailles de nœud.
- **Atténuation** : retentez l'opération.

<h3><a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure</h3>
- **Description** : impossible de créer le service hébergé *nom_service_hébergé*. Relancez la requête.  
- **Atténuation** : relancez la requête.

<h3><a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment</h3>
- **Description** : le service hébergé *nom_service_hébergé* a déjà un déploiement de production. Un service hébergé ne peut pas contenir plusieurs déploiements de production. Relancez la requête avec un nom de cluster différent.   
- **Atténuation** : utilisez un nom de cluster différent et relancez la requête.

<h3><a id="HostedServiceNotFound"></a>HostedServiceNotFound</h3>
- **Description** : le service hébergé *nom_service_hébergé* pour le cluster est introuvable.  
- **Atténuation** : si le cluster est dans l'état d'erreur, supprimez-le et retentez l'opération. 

<h3><a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment</h3>
- **Description** : aucun déploiement n'est associé au service hébergé *nom_service_hébergé*.  
- **Atténuation** : si le cluster est dans l'état d'erreur, supprimez-le et retentez l'opération. 

<h3><a id="InsufficientResourcesCores"></a>InsufficientResourcesCores</h3>
- **Description** : l'ID d'abonnement *ID_abonnement* n'a plus de cœurs pour créer le cluster *nom_cluster*. Requis : *ressources_requises*, Disponible : *ressources_disponibles*.  
- **Atténuation** : libérez des ressources dans votre abonnement ou augmentez les ressources disponibles pour l'abonnement et essayez de recréer le cluster.

<h3><a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices</h3>
- **Description** : L'ID d'abonnement *ID_abonnement* n'a pas de quota pour un nouveau service hébergé permettant de créer le cluster *nom_cluster*.  
- **Atténuation** : libérez des ressources dans votre abonnement ou augmentez les ressources disponibles pour l'abonnement et essayez de recréer le cluster.

<h3><a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest</h3>
- **Description** : une erreur interne s'est produite sur le serveur. Relancez la requête.  
- **Atténuation** : relancez la requête. 

<h3><a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation</h3>
- **Description** : l'emplacement du stockage Azure *nom_région_données* n'est pas un emplacement correct. Assurez-vous que la région est correcte et relancez la requête.   
- **Atténuation** : sélectionnez un emplacement de stockage qui prend en charge HDInsight, vérifiez que votre cluster est en colocation et retentez l'opération. 

<h3><a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode</h3>
- **Description** : taille de machine virtuelle incorrecte pour les nœuds de données. Seule la taille " Machine virtuelle large " est prise en charge pour tous les nœuds de données.  
- **Atténuation** : spécifiez la taille de nœud prise en charge pour le nœud de données et retentez l'opération. 

<h3><a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode</h3>
- **Description** : taille de machine virtuelle non valide pour le nœud principal. Seule la taille " Machine virtuelle extra large " est prise en charge pour le nœud principal.  
- **Atténuation** : spécifiez la taille de nœud prise en charge pour le nœud principal et retentez l'opération.

<h3><a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion</h3>
- **Description** : l'ID d'abonnement *ID_abonnement* utilisé ne dispose pas d'autorisations suffisantes pour exécuter l'opération de suppression sur le cluster *nom_cluster*.  
- **Atténuation** : si le cluster est dans l'état d'erreur, supprimez-le et retentez l'opération.  

<h3><a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName</h3>
- **Description** : le nom du conteneur d'objets blob du compte de stockage externe *nom_conteneur* est incorrect. Assurez-vous que le nom commence par une lettre et contient uniquement des lettres minuscules, des chiffres et des tirets.  
- **Atténuation** : spécifiez un nom correct pour le conteneur d'objets blob du compte de stockage et retentez l'opération.

<h3><a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey</h3>
- **Description** : les détails de la clé secrète doivent être définis dans la configuration du compte de stockage externe *nom_compte_stockage*.  
- **Atténuation** : spécifiez une clé secrète correcte pour le compte de stockage et retentez l'opération.

<h3><a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat</h3>
- **Description** : l'en-tête de version *entête_version* n'est pas au format correct : aaaa-mm-jj.  
- **Atténuation** : spécifiez un format correct pour l'en-tête de version et retentez l'opération. 

<h3><a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode</h3>
- **Description** : configuration de cluster incorrecte. Plusieurs configurations de nœud principal trouvées.  
- **Atténuation** : modifiez la configuration de façon à spécifier un seul nœud principal. 

<h3><a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest</h3>
- **Description** : Il n'a pas été possible d'exécuter l'opération dans le temps imparti ou en respectant le nombre maximum de tentatives. Relancez la requête.  
- **Atténuation** : relancez la requête. 

<h3><a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty</h3>
- **Description** : Le paramètre *nom_paramètre* ne peut pas être null ou vide.  
- **Atténuation** : spécifiez une valeur correcte pour le paramètre. 

<h3><a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure</h3>
- **Description** : une ou plusieurs entrées de demande de création de cluster sont incorrectes. Assurez-vous que les valeurs d'entrée sont correctes et relancez la requête.  
- **Atténuation** : Assurez-vous que les valeurs d'entrée sont correctes et relancez la requête. 

<h3><a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable</h3>
- **Description** : capacité de région non disponible pour la région *nom_région* et l'ID d'abonnement *ID_abonnement*.  
- **Atténuation** : spécifiez une région qui prend en charge les clusters HDInsight. Les régions publiquement prises en charge sont les suivantes : Asie du Sud-Est, Europe du Nord, Europe de l'Ouest, Est des États-Unis ou Ouest des États-Unis. 

<h3><a id="StorageAccountNotColocated"></a>StorageAccountNotColocated</h3>
- **Description** : le compte de stockage *nom_compte_stockage* se trouve dans la région *nom_région_actuelle*. La région doit être la même que celle du cluster *nom_région_cluster*.  
- **Atténuation** : spécifiez un compte de stockage dans la même région que votre cluster ou, si vos données se trouvent déjà dans le compte de stockage, créez un cluster dans la même région que le compte de stockage existant. Si vous utilisez le portail, l'interface utilisateur vous avertira de ce problème à l'avance. 

<h3><a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive</h3>
- **Description** : l'ID d'abonnement *ID_abonnement* n'est pas actif.  
- **Atténuation** : Réactivez votre abonnement ou obtenez un nouvel abonnement valide.

<h3><a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound</h3>
- **Description** : l'ID d'abonnement *ID_abonnement* est introuvable.  
- **Atténuation** : vérifiez que votre ID d'abonnement est correct et retentez l'opération. 

<h3><a id="UnableToResolveDNS"></a>UnableToResolveDNS</h3>
- **Description** : impossible de résoudre le DNS *URL_DNS*. Veillez à fournir l'URL complète du point de terminaison d'objet blob.  
- **Atténuation** : fournissez une URL d'objet blob correcte. L'URL DOIT être entièrement correcte, notamment commencer par *http://* et se terminer par *.com*. L'URL complète se trouve généralement sous l'onglet Stockage du portail manage.windowsazure.com.  

<h3><a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource</h3>
- **Description** : impossible de vérifier l'emplacement de la ressource *URL_DNS*. Veillez à fournir l'URL complète du point de terminaison d'objet blob.  
- **Atténuation** : fournissez une URL d'objet blob correcte. L'URL DOIT être entièrement correcte, notamment commencer par *http://* et se terminer par *.com*. L'URL complète se trouve généralement sous l'onglet Stockage du portail manage.windowsazure.com. 

<h3><a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable</h3>
- **Description** : capacité de version non disponible pour la version *version_spécifiée* et l'ID d'abonnement *ID_abonnement*.  
- **Atténuation** : choisissez une version qui est disponible et retentez l'opération. 

<h3><a id="VersionNotSupported"></a>VersionNotSupported</h3>
- **Description** : Version *version_spécifiée* non prise en charge.   
- **Atténuation** : choisissez une version qui est prise en charge et retentez l'opération.

<h3><a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion</h3>
- **Description** : la version *version_spécifiée* n'est pas disponible dans la région Azure *région_spécifiée*.  
- **Atténuation** : choisissez une version qui est prise en charge dans la région spécifiée et retentez l'opération. 

<h3><a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound</h3>
- **Description** : configuration de cluster incorrecte. Configuration de compte WASB requise introuvable dans les comptes externes.  
- **Atténuation** : vérifiez que le compte existe et qu'il est correctement spécifié dans la configuration, puis retentez l'opération. 

<h2><a id="resources"></a>Autres ressources de débogage</h2> 

* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png






