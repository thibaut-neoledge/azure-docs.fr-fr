<properties
	pageTitle="Création, gestion ou suppression d’un compte de stockage | Microsoft Azure"
	description="Créez un compte de stockage, gérez vos clés d’accès ou supprimez un compte de stockage dans le portail Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/01/2015" 
	ms.author="tamram"/>


# À propos des comptes de stockage Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Vue d'ensemble

Un compte de stockage Azure vous donne accès aux services Azure Objet blob, File d’attente, Table et Fichier dans Azure Storage. Votre compte de stockage fournit les espaces de noms uniques pour vos objets de données Azure Storage. Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte.

Il existe deux types de comptes de stockage :

- Un compte de stockage standard qui inclut le stockage d’objets blob, de tables, de files d’attente et de fichiers. 
- Un compte de stockage Premium prend actuellement en charge uniquement les disques de machine virtuelle Azure. Pour une présentation détaillée du stockage Premium, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

## Facturation du compte de stockage

La facturation de l’utilisation d’Azure Storage est basée sur votre compte de stockage. Les coûts de stockage sont basés sur quatre facteurs : la capacité de stockage, le schéma de réplication, les transactions de stockage et l’acheminement des données.

- La capacité de stockage fait référence à l’unité de compte de stockage que vous utilisez pour stocker des données. Le coût d’un simple stockage de vos données est déterminé par la quantité de données que vous stockez et leur type de réplication.
- La réplication détermine le nombre de copies de vos données qui sont conservées simultanément et à quels emplacements.
- Les transactions font références à toutes les opérations de lecture et d'écriture vers Azure Storage.
- L'acheminement des données fait référence aux données transférées hors d'une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n'est pas exécutée dans la même région, que cette application soit un service cloud ou un autre type d'application, vous êtes facturé pour l'acheminement des données (pour les services Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais d'acheminement des données).  

La page [Tarification Azure](http://azure.microsoft.com/pricing/details/#storage) fournit des informations de tarification détaillées pour les capacités de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données](http://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour les acheminements de données.

Pour plus d’informations sur la capacité et les objectifs de performance du compte de stockage, consultez la page [Objectifs d’évolutivité et de performances d’Azure Storage](http://msdn.microsoft.com/library/azure/dn249410.aspx).

> [AZURE.NOTE]Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé automatiquement pour vous sur le lieu de déploiement si vous ne disposez pas déjà de ce type de compte à cet emplacement. Il n’est donc pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques de machines virtuelles. Le nom du compte de stockage dépend du nom de la machine virtuelle. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Points de terminaison d’un compte de stockage

Chaque objet que vous stockez dans Azure Storage possède une adresse URL unique. Le nom du compte de stockage constitue le sous-domaine de cette adresse. La combinaison sous-domaine/nom du domaine, qui est propre à chaque service, forme un *point de terminaison* pour votre compte de stockage.

Par exemple, si le nom de votre compte de stockage est *mystorageaccount*, les points de terminaison par défaut de votre compte de stockage sont les suivants :

- Service BLOB : http://*mystorageaccount*.blob.core.windows.net

- Service de table : http://*mystorageaccount*.table.core.windows.net

- Service de file d'attente : http://*mystorageaccount*.queue.core.windows.net

- Service de fichiers : http://*mystorageaccount*.file.core.windows.net

L’URL permettant d’accéder à un objet dans un compte de stockage est constituée de l’ajout de l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d’objet blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez aussi configurer un nom de domaine personnalisé à utiliser avec votre compte de stockage. Pour plus d’informations, consultez [Configuration d’un nom de domaine personnalisé pour des données d’objets blob dans un compte de stockage Azure](storage-custom-domain-name.md).

## Créez un compte de stockage.

1. Connectez-vous au [portail Azure](portal.azure.com).

2. Dans le menu Hub, sélectionnez **Nouveau** -> **Données et stockage** -> **Compte de stockage**.

3. Sélectionnez un modèle de déploiement : **Resource Manager** ou **Classique**. **Resource Manager** est le modèle de déploiement recommandé. Pour plus d’informations, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).

4. Entrez un nom pour votre compte de stockage.

	> [AZURE.NOTE]Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
	>  
	> Le nom de votre compte de stockage doit être unique dans Azure. Le portail Azure indique si le nom du compte de stockage sélectionné est déjà utilisé.

	Pour plus d’informations sur l’utilisation du nom de compte de stockage pour adresser les objets que vous stockez dans Azure Storage, consultez [Points de terminaison d’un compte de stockage](#storage-account-endpoints).

5. Spécifiez le type de compte de stockage à créer. Le type de compte de stockage détermine la manière dont le compte de stockage est répliqué et s’il s’agit d’un compte de stockage standard ou premium.

	Le type de compte de stockage par défaut est **Standard-RAGRS**, qui est un compte de stockage standard doté de la réplication géo-redondante avec accès en lecture. Ce type de compte de stockage est répliqué vers une région secondaire, à des centaines de kilomètres de la région primaire et fournit un accès en lecture à l’emplacement secondaire.

	Pour plus d’informations sur les options de réplication d’Azure Storage, consultez [Réplication Azure Storage](storage-redundancy.md). Pour plus d’informations sur les comptes de stockage standard et premium, consultez [Introduction à Microsoft Azure Storage](storage-introduction.md) et [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md)

6. Indiquez si vous souhaitez activer les diagnostics pour votre compte de stockage. Les diagnostics incluent la journalisation et les mesures Storage Analytics.

7. Si vous disposez de plusieurs abonnements Azure, le champ **Subscription** s'affiche. Sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.

8. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [Utiliser le portail Azure pour gérer des ressources Azure](../resource-group-portal.md).

9. Sélectionnez l’emplacement géographique de votre compte de stockage.

10. Cliquez sur **Créer** pour créer le compte de stockage.

## Gérer vos clés d’accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

> [AZURE.NOTE]Nous vous recommandons d’éviter de partager vos clés d’accès de stockage avec qui que ce soit. Pour autoriser l'accès aux ressources de stockage sans donner vos clés d'accès, vous pouvez utiliser une *signature d'accès partagé*. Celle-ci donne accès à votre compte à une ressource pendant une durée que vous déterminez et avec les autorisations que vous spécifiez. Pour plus d'informations, consultez le [didacticiel sur les signatures d'accès partagé](storage-dotnet-shared-access-signature-part-1.md).

### Afficher et copier les clés d’accès de stockage

Dans le [portail Azure](portal.azure.com), accédez à votre compte de stockage, puis cliquez sur l’icône **Clés** pour afficher, copier et régénérer les clés d’accès de votre compte. Le panneau **Clés d’accès** inclut également des chaînes de connexion préconfigurées utilisant vos clés principales et secondaires, que vous pouvez copier pour utilisation dans vos applications.

### Régénération des clés d'accès de stockage

Nous vous recommandons de modifier périodiquement les clés d’accès à votre compte de stockage pour garantir la sécurité des connexions de stockage. Vous bénéficiez de deux clés d’accès, ce qui vous permet de conserver des connexions au compte de stockage à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

> [AZURE.WARNING]La régénération des clés d’accès a une incidence sur les machines virtuelles, les services de médias et toutes les applications qui dépendent du compte de stockage. Tous les clients qui utilisent la clé d'accès pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Machines virtuelles** : si votre compte de stockage contient des machines virtuelles en cours d'exécution, vous devez redéployer toutes les machines virtuelles après avoir régénéré les clés d'accès. Pour éviter le redéploiement, arrêtez les machines virtuelles avant de régénérer les clés d'accès.

**Media Services** : si des services de médias sont liés à votre compte de stockage, vous devez resynchroniser les clés d’accès avec ces services après avoir régénéré les clés.

**Applications** : si des applications web ou des services cloud utilisent le compte de stockage, vous perdez les connexions en régénérant les clés, à moins de les substituer.

Voici le processus de rotation de vos clés d’accès de stockage :

1. Mettez à jour les chaînes de connexion dans votre code d'application pour désigner la clé d'accès secondaire du compte de stockage.

2. Régénérez la clé d'accès principale de votre compte de stockage. Dans le panneau **Clés d’accès**, cliquez sur **Régénérer Key1**, puis sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d’accès principale.

4. Régénérez la clé d’accès secondaire de la même manière.

## Suppression d'un compte de stockage

Pour supprimer un compte de stockage que vous n’utilisez plus, accédez au compte de stockage dans le [portail Azure](portal.azure.com), puis cliquez sur **Supprimer**. La suppression d’un compte de stockage supprime l’intégralité du compte, y compris toutes les données qu’il contient.

> [AZURE.WARNING]Il n’est pas possible de restaurer un compte de stockage supprimé ou son contenu avant la suppression. Veillez à sauvegarder tout ce que vous souhaitez conserver avant de supprimer le compte. Ceci vaut également pour toutes les ressources du compte : dès que vous supprimez un objet blob, une table, une file d’attente ou un fichier, la suppression est irréversible.

## Étapes suivantes

- Pour en savoir plus sur Azure Storage, consultez la documentation de référence Azure Storage sur [Azure.com](http://azure.microsoft.com/documentation/services/storage/).
- Consultez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

<!---HONumber=AcomDC_1203_2015-->