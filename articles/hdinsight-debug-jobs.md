<properties linkid="manage-services-hdinsight-debug-error-messages" urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Debug Hadoop in HDInsight: Error messages | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Débogage de Hadoop dans HDInsight : messages d'erreur

## Introduction

Les messages d'erreur répertoriés dans cette rubrique visent à aider les utilisateurs de Hadoop dans Azure HDInsight à comprendre les conditions d'erreur auxquelles ils sont susceptibles d'être confrontés lorsqu'ils administrent le service avec Azure PowerShell et à les conseiller sur les mesures à prendre pour la récupération à la suite d'une erreur.

Certains de ces messages d'erreur peuvent également apparaître dans le portail Azure lorsqu'il sert à gérer les clusters HDinsight. Mais d'autres messages d'erreur que vous pouvez rencontrer ici sont moins granulaires, en raison des contraintes qui s'appliquent aux mesures correctives possibles dans ce contexte. D'autres encore sont fournis dans les contextes où l'atténuation est évidente. Si les contraintes sur les paramètres sont violées, par exemple, le message s'affiche à droite de la zone dans laquelle la valeur a été entrée. Dans le cas suivant, un trop grand nombre de nœuds de données a été demandé. La solution consiste à réduire ce nombre à une valeur autorisée, c'est-à-dire 22 ou moins.

![HDI.Debugging.ErrorMessages.Portal][HDI.Debugging.ErrorMessages.Portal]

Les erreurs auxquelles un utilisateur peut être confronté dans Azure PowerShell ou dans le portail Azure sont répertoriées dans l'ordre alphabétique des noms dans la section [Erreurs HDInsight][Erreurs HDInsight]. Chaque erreur répertoriée est liée à une entrée de la section [Description et atténuation des erreurs][Description et atténuation des erreurs] qui fournit les informations suivantes sur l'erreur :

-   **Description** : message d'erreur visible pour les utilisateurs
-   **Atténuation** : mesures à prendre pour la récupération à la suite de l'erreur.

### Erreurs HDInsight

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

## <span id="discription-mitigation-errors"></span></a>Diagnostic et atténuation des erreurs

### <span id="AtleastOneSqlMetastoreMustBeProvided"></span></a>AtleastOneSqlMetastoreMustBeProvided

-   **Description** : fournissez des informations détaillées sur la base de données SQL Azure pour au moins un composant afin d'utiliser les paramètres personnalisés pour les metastores Hive et Oozie.
-   **Atténuation** : vous devez fournir un metastore SQL Azure correct et relancer la requête.

### <span id="AzureRegionNotSupported"></span></a>AzureRegionNotSupported

-   **Description** : impossible de créer un cluster dans la région *nom\_région*. Utilisez une région HDInsight valide et relancez la requête.
-   **Atténuation** : créez le cluster dans une région qui prend actuellement en charge les clusters : Asie du Sud-Est, Europe du Nord, Europe de l'Ouest, Est des États-Unis ou Ouest des États-Unis.

### <span id="ClusterContainerRecordNotFound"></span></a>ClusterContainerRecordNotFound

-   **Description** : le serveur n'a pas pu trouver l'enregistrement de cluster demandé.
-   **Atténuation** : retentez l'opération.

### <span id="ClusterDnsNameInvalidReservedWord"></span></a>ClusterDnsNameInvalidReservedWord

-   **Description** : le nom DNS du cluster *nom\_DNS* est incorrect. Assurez-vous que le nom commence et se termine par un caractère alphanumérique et contient uniquement le caractère spécial '-'.
-   **Atténuation** : assurez-vous que vous avez utilisé pour votre cluster un nom DNS correct, qui commence et se termine par un caractère alphanumérique et ne contient aucun caractère spécial à l'exception du tiret '-', puis retentez l'opération.

### <span id="ClusterNameUnavailable"></span></a>ClusterNameUnavailable

-   **Description** : le nom de cluster *nom\_cluster* n'est pas disponible. Choisissez un autre nom.
-   **Atténuation** : vous devez spécifier un nom de cluster qui est unique et qui n'existe pas, puis refaire une tentative. Si vous utilisez le portail, l'interface utilisateur vous avertira si un nom de cluster est déjà utilisé pendant la procédure de création.

### <span id="ClusterPasswordInvalid"></span></a>ClusterPasswordInvalid

-   **Description** : le mot de passe de cluster n'est pas valide. Le mot de passe doit avoir une longueur d'au moins 10 caractères et contenir au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère spécial, sans espace. Par ailleurs, il ne doit pas contenir le nom d'utilisateur.
-   **Atténuation** : fournissez un mot de passe de cluster correct et retentez l'opération.

### <span id="ClusterUserNameInvalid"></span></a>ClusterUserNameInvalid

-   **Description** : le nom d'utilisateur du cluster est incorrect. Assurez-vous que le nom d'utilisateur ne contient pas de caractères spéciaux ni d'espaces.
-   **Atténuation** : fournissez un nom d'utilisateur de cluster correct et retentez l'opération.

### <span id="ClusterUserNameInvalidReservedWord"></span></a>ClusterUserNameInvalidReservedWord

-   **Description** : le nom DNS du cluster *nom\_DNS\_cluster* est incorrect. Assurez-vous que le nom commence et se termine par un caractère alphanumérique et contient uniquement le caractère spécial '-'.
-   **Atténuation** : fournissez un nom DNS de cluster correct et retentez l'opération.

### <span id="ContainerNameMisMatchWithDnsName"></span></a>ContainerNameMisMatchWithDnsName

-   **Description** : le nom du conteneur dans l'URI *URI\_conteneur* et le nom DNS *nom\_DNS* dans le corps de la requête doivent être identiques.
-   **Atténuation** : assurez-vous que le nom de votre conteneur et le nom de votre DNS sont identiques, puis retentez l'opération.

### <span id="DataNodeDefinitionNotFound"></span></a>DataNodeDefinitionNotFound

-   **Description** : configuration de cluster incorrecte. Impossible de trouver des définitions de nœud de données dans la taille de nœud.
-   **Atténuation** : retentez l'opération.

### <span id="DeploymentDeletionFailure"></span></a>DeploymentDeletionFailure

-   **Description** : échec de la suppression du déploiement pour le cluster
-   **Atténuation** : retentez l'opération.

### <span id="DnsMappingNotFound"></span></a>DnsMappingNotFound

-   **Description** : erreur de configuration du service. Informations de mappage DNS requises introuvables.
-   **Atténuation** : supprimez le cluster et recréez-en un.

### <span id="DuplicateClusterContainerRequest"></span></a>DuplicateClusterContainerRequest

-   **Description** : tentative de création d'un conteneur de cluster en double. Il existe un enregistrement pour *nom\_conteneur*, mais les valeurs Etag ne correspondent pas.
-   **Atténuation** : fournissez un nom unique pour le conteneur et retentez l'opération de création.

### <span id="DuplicateClusterInHostedService"></span></a>DuplicateClusterInHostedService

-   **Description** : le service hébergé *nom\_service\_hébergé* contient déjà un cluster. Un service hébergé ne peut pas contenir plusieurs clusters.
-   **Atténuation** : hébergez le cluster dans un autre service hébergé.

### <span id="FailureToUpdateDeploymentStatus"></span></a>FailureToUpdateDeploymentStatus

-   **Description** : le serveur n'a pas pu mettre à jour l'état du déploiement du cluster.
-   **Atténuation** : retentez l'opération. Si le problème se reproduit plusieurs fois, contactez CSS.

### <span id="HdiRestoreClusterAltered"></span></a>HdiRestoreClusterAltered

-   **Description** : Le cluster *nom\_cluster* a été supprimé dans le cadre de la maintenance. Recréez le cluster.
-   **Atténuation** : recréez le cluster.

### <span id="HeadNodeConfigNotFound"></span></a>HeadNodeConfigNotFound

-   **Description** : configuration de cluster incorrecte. Configuration de nœud principal introuvable dans les tailles de nœud.
-   **Atténuation** : retentez l'opération.

### <span id="HostedServiceCreationFailure"></span></a>HostedServiceCreationFailure

-   **Description** : impossible de créer le service hébergé *nom\_service\_hébergé*. Relancez la requête.
-   **Atténuation** : relancez la requête.

### <span id="HostedServiceHasProductionDeployment"></span></a>HostedServiceHasProductionDeployment

-   **Description** : le service hébergé *nom\_service\_hébergé* a déjà un déploiement de production. Un service hébergé ne peut pas contenir plusieurs déploiements de production. Relancez la requête avec un nom de cluster différent.
-   **Atténuation** : utilisez un nom de cluster différent et relancez la requête.

### <span id="HostedServiceNotFound"></span></a>HostedServiceNotFound

-   **Description** : le service hébergé *nom\_service\_hébergé* pour le cluster est introuvable.
-   **Atténuation** : si le cluster est dans l'état d'erreur, supprimez-le et retentez l'opération.

### <span id="HostedServiceWithNoDeployment"></span></a>HostedServiceWithNoDeployment

-   **Description** : aucun déploiement n'est associé au service hébergé *nom\_service\_hébergé*.
-   **Atténuation** : si le cluster est dans l'état d'erreur, supprimez-le et retentez l'opération.

### <span id="InsufficientResourcesCores"></span></a>InsufficientResourcesCores

-   **Description** : l'ID d'abonnement *ID\_abonnement* n'a plus de cœurs pour créer le cluster *nom\_cluster*. Requis : *ressources\_requises*, Disponible : *ressources\_disponibles*.
-   **Atténuation** : libérez des ressources dans votre abonnement ou augmentez les ressources disponibles pour l'abonnement et essayez de recréer le cluster.

### <span id="InsufficientResourcesHostedServices"></span></a>InsufficientResourcesHostedServices

-   **Description** : L'ID d'abonnement *ID\_abonnement* n'a pas de quota pour un nouveau service hébergé permettant de créer le cluster *nom\_cluster*.
-   **Atténuation** : libérez des ressources dans votre abonnement ou augmentez les ressources disponibles pour l'abonnement et essayez de recréer le cluster.

### <span id="InternalErrorRetryRequest"></span></a>InternalErrorRetryRequest

-   **Description** : une erreur interne s'est produite sur le serveur. Relancez la requête.
-   **Atténuation** : relancez la requête.

### <span id="InvalidAzureStorageLocation"></span></a>InvalidAzureStorageLocation

-   **Description** : l'emplacement du stockage Azure *nom\_région\_données* n'est pas un emplacement correct. Assurez-vous que la région est correcte et relancez la requête.
-   **Atténuation** : sélectionnez un emplacement de stockage qui prend en charge HDInsight, vérifiez que votre cluster est en colocation et retentez l'opération.

### <span id="InvalidNodeSizeForDataNode"></span></a>InvalidNodeSizeForDataNode

-   **Description** : taille de machine virtuelle incorrecte pour les nœuds de données. Seule la taille « Machine virtuelle de taille importante » est prise en charge pour tous les nœuds de données.
-   **Atténuation** : spécifiez la taille de nœud prise en charge pour le nœud de données et retentez l'opération.

### <span id="InvalidNodeSizeForHeadNode"></span></a>InvalidNodeSizeForHeadNode

-   **Description** : taille de machine virtuelle non valide pour le nœud principal. Seule la taille « Machine virtuelle de taille très importante » est prise en charge pour le nœud principal.
-   **Atténuation** : spécifiez la taille de nœud prise en charge pour le nœud principal et retentez l'opération.

### <span id="InvalidRightsForDeploymentDeletion"></span></a>InvalidRightsForDeploymentDeletion

-   **Description** : l'ID d'abonnement *ID\_abonnement* utilisé ne dispose pas d'autorisations suffisantes pour exécuter l'opération de suppression sur le cluster *nom\_cluster*
    .
-   **Atténuation** : si le cluster est dans l'état d'erreur, supprimez-le et retentez l'opération.

### <span id="InvalidStorageAccountBlobContainerName"></span></a>InvalidStorageAccountBlobContainerName

-   **Description** : le nom du conteneur d'objets blob du compte de stockage externe *nom\_conteneur* est incorrect. Assurez-vous que le nom commence par une lettre et contient uniquement des lettres minuscules, des chiffres et des tirets.
-   **Atténuation** : spécifiez un nom correct pour le conteneur d'objets blob du compte de stockage et retentez l'opération.

### <span id="InvalidStorageAccountConfigurationSecretKey"></span></a>InvalidStorageAccountConfigurationSecretKey

-   **Description** : les détails de la clé secrète doivent être définis dans la configuration du compte de stockage externe *nom\_compte\_stockage*.
-   **Atténuation** : spécifiez une clé secrète correcte pour le compte de stockage et retentez l'opération.

### <span id="InvalidVersionHeaderFormat"></span></a>InvalidVersionHeaderFormat

-   **Description** : l'en-tête de version *entête\_version* n'est pas au format correct : aaaa-mm-jj.
-   **Atténuation** : spécifiez un format correct pour l'en-tête de version et retentez l'opération.

### <span id="MoreThanOneHeadNode"></span></a>MoreThanOneHeadNode

-   **Description** : configuration de cluster incorrecte. Plusieurs configurations de nœud principal trouvées.
-   **Atténuation** : modifiez la configuration de façon à spécifier un seul nœud principal.

### <span id="OperationTimedOutRetryRequest"></span></a>OperationTimedOutRetryRequest

-   **Description** : Il n'a pas été possible d'exécuter l'opération dans le temps imparti ou en respectant le nombre maximum de tentatives. Relancez la requête.
-   **Atténuation** : relancez la requête.

### <span id="ParameterNullOrEmpty"></span></a>ParameterNullOrEmpty

-   **Description** : Le paramètre *nom\_paramètre* ne peut pas être null ou vide.
-   **Atténuation** : spécifiez une valeur correcte pour le paramètre.

### <span id="PreClusterCreationValidationFailure"></span></a>PreClusterCreationValidationFailure

-   **Description** : une ou plusieurs entrées de demande de création de cluster sont incorrectes. Assurez-vous que les valeurs d'entrée sont correctes et relancez la requête.
-   **Atténuation** : Assurez-vous que les valeurs d'entrée sont correctes et relancez la requête.

### <span id="RegionCapabilityNotAvailable"></span></a>RegionCapabilityNotAvailable

-   **Description** : capacité de région non disponible pour la région *nom\_région* et l'ID d'abonnement *ID\_abonnement*.
-   **Atténuation** : spécifiez une région qui prend en charge les clusters HDInsight. Les régions publiquement prises en charge sont les suivantes : Asie du Sud-Est, Europe du Nord, Europe de l'Ouest, Est des États-Unis ou Ouest des États-Unis.

### <span id="StorageAccountNotColocated"></span></a>StorageAccountNotColocated

-   **Description** : le compte de stockage *nom\_compte\_stockage* se trouve dans la région *nom\_région\_actuelle*. La région doit être la même que celle du cluster *nom\_région\_cluster*.
-   **Atténuation** : spécifiez un compte de stockage dans la même région que votre cluster ou, si vos données se trouvent déjà dans le compte de stockage, créez un cluster dans la même région que le compte de stockage existant. Si vous utilisez le portail, l'interface utilisateur vous avertira de ce problème à l'avance.

### <span id="SubscriptionIdNotActive"></span></a>SubscriptionIdNotActive

-   **Description** : l'ID d'abonnement *ID\_abonnement* n'est pas actif.
-   **Atténuation** : Réactivez votre abonnement ou obtenez un nouvel abonnement valide.

### <span id="SubscriptionIdNotFound"></span></a>SubscriptionIdNotFound

-   **Description** : l'ID d'abonnement *ID\_abonnement* est introuvable.
-   **Atténuation** : vérifiez que votre ID d'abonnement est correct et retentez l'opération.

### <span id="UnableToResolveDNS"></span></a>UnableToResolveDNS

-   **Description** : impossible de résoudre le DNS *URL\_DNS*. Veillez à fournir l'URL complète du point de terminaison d'objet blob.
-   **Atténuation** : fournissez une URL d'objet blob correcte. L'URL DOIT être entièrement correcte, notamment commencer par *<http://>* et se terminer par *[.com][.com]*. L'URL complète se trouve généralement sous l'onglet Stockage du portail manage.windowsazure.com.

### <span id="UnableToVerifyLocationOfResource"></span></a>UnableToVerifyLocationOfResource

-   **Description** : impossible de vérifier l'emplacement de la ressource *URL\_DNS*. Veillez à fournir l'URL complète du point de terminaison d'objet blob.
-   **Atténuation** : fournissez une URL d'objet blob correcte. L'URL DOIT être entièrement correcte, notamment commencer par *<http://>* et se terminer par *[.com][.com]*. L'URL complète se trouve généralement sous l'onglet Stockage du portail manage.windowsazure.com.

### <span id="VersionCapabilityNotAvailable"></span></a>VersionCapabilityNotAvailable

-   **Description** : capacité de version non disponible pour la version *version\_spécifiée* et l'ID d'abonnement *ID\_abonnement*.
-   **Atténuation** : choisissez une version qui est disponible et retentez l'opération.

### <span id="VersionNotSupported"></span></a>VersionNotSupported

-   **Description** : Version *version\_spécifiée* non prise en charge.
-   **Atténuation** : choisissez une version qui est prise en charge et retentez l'opération.

### <span id="VersionNotSupportedInRegion"></span></a>VersionNotSupportedInRegion

-   **Description** : la version *version\_spécifiée* n'est pas disponible dans la région Azure *région\_spécifiée*.
-   **Atténuation** : choisissez une version qui est prise en charge dans la région spécifiée et retentez l'opération.

### <span id="WasbAccountConfigNotFound"></span></a>WasbAccountConfigNotFound

-   **Description** : configuration de cluster incorrecte. Configuration de compte WASB requise introuvable dans les comptes externes.
-   **Atténuation** : vérifiez que le compte existe et qu'il est correctement spécifié dans la configuration, puis retentez l'opération.

## <span id="resources"></span></a>Autres ressources de débogage

-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][Documentation du Kit de développement logiciel (SDK) Azure HDInsight]

  [HDI.Debugging.ErrorMessages.Portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png
  [Erreurs HDInsight]: #hdinsight-error-messages
  [Description et atténuation des erreurs]: #discription-mitigation-errors
  [AtleastOneSqlMetastoreMustBeProvided]: #AtleastOneSqlMetastoreMustBeProvided
  [AzureRegionNotSupported]: #AzureRegionNotSupported
  [ClusterContainerRecordNotFound]: #ClusterContainerRecordNotFound
  [ClusterDnsNameInvalidReservedWord]: #ClusterDnsNameInvalidReservedWord
  [ClusterNameUnavailable]: #ClusterNameUnavailable
  [ClusterUserNameInvalid]: #ClusterUserNameInvalid
  [ClusterUserNameInvalidReservedWord]: #ClusterUserNameInvalidReservedWord
  [ContainerNameMisMatchWithDnsName]: #ContainerNameMisMatchWithDnsName
  [DataNodeDefinitionNotFound]: #DataNodeDefinitionNotFound
  [DeploymentDeletionFailure]: #DeploymentDeletionFailure
  [DnsMappingNotFound]: #DnsMappingNotFound
  [DuplicateClusterContainerRequest]: #DuplicateClusterContainerRequest
  [DuplicateClusterInHostedService]: #DuplicateClusterInHostedService
  [FailureToUpdateDeploymentStatus]: #FailureToUpdateDeploymentStatus
  [HdiRestoreClusterAltered]: #HdiRestoreClusterAltered
  [HeadNodeConfigNotFound]: #HeadNodeConfigNotFound
  [HostedServiceCreationFailure]: #HostedServiceCreationFailure
  [HostedServiceHasProductionDeployment]: #HostedServiceHasProductionDeployment
  [HostedServiceNotFound]: #HostedServiceNotFound
  [HostedServiceWithNoDeployment]: #HostedServiceWithNoDeployment
  [InsufficientResourcesCores]: #InsufficientResourcesCores
  [InsufficientResourcesHostedServices]: #InsufficientResourcesHostedServices
  [InternalErrorRetryRequest]: #InternalErrorRetryRequest
  [InvalidAzureStorageLocation]: #InvalidAzureStorageLocation
  [InvalidNodeSizeForDataNode]: #InvalidNodeSizeForDataNode
  [InvalidNodeSizeForHeadNode]: #InvalidNodeSizeForHeadNode
  [InvalidRightsForDeploymentDeletion]: #InvalidRightsForDeploymentDeletion
  [InvalidStorageAccountBlobContainerName]: #InvalidStorageAccountBlobContainerName
  [InvalidStorageAccountConfigurationSecretKey]: #InvalidStorageAccountConfigurationSecretKey
  [InvalidVersionHeaderFormat]: #InvalidVersionHeaderFormat
  [MoreThanOneHeadNode]: #MoreThanOneHeadNode
  [OperationTimedOutRetryRequest]: #OperationTimedOutRetryRequest
  [ParameterNullOrEmpty]: #ParameterNullOrEmpty
  [PreClusterCreationValidationFailure]: #PreClusterCreationValidationFailure
  [RegionCapabilityNotAvailable]: #RegionCapabilityNotAvailable
  [StorageAccountNotColocated]: #StorageAccountNotColocated
  [SubscriptionIdNotActive]: #SubscriptionIdNotActive
  [SubscriptionIdNotFound]: #SubscriptionIdNotFound
  [UnableToResolveDNS]: #UnableToResolveDNS
  [UnableToVerifyLocationOfResource]: #UnableToVerifyLocationOfResource
  [VersionCapabilityNotAvailable]: #VersionCapabilityNotAvailable
  [VersionNotSupported]: #VersionNotSupported
  [VersionNotSupportedInRegion]: #VersionNotSupportedInRegion
  [WasbAccountConfigNotFound]: #WasbAccountConfigNotFound
  [.com]: http://
  [Documentation du Kit de développement logiciel (SDK) Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx
