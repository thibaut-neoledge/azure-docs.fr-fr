<properties 
	pageTitle="Gestion des comptes de stockage | Microsoft Azure" 
	description="Découvrez comment gérer des comptes de stockage dans Azure en utilisant le portail de gestion." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tamram"/>



<h1><a id="managestorageaccounts"></a>Gestion des comptes de stockage</h1>

##Sommaire##

* [Procédure : Gestion de la réplication du compte de stockage](#georeplication)
* [Procédure : Affichage, copie et régénération de clés d'accès de stockage](#regeneratestoragekeys)
* [Procédure : Suppression d'un compte de stockage](#deletestorageaccount)

<h2><a id="georeplication"></a>Procédure : de vos données de compte de stockage pour la durabilité et la haute disponibilité</h2>

[AZURE.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Pour spécifier les paramètres de réplication d'un compte de stockage ###

1. Dans le [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Stockage**, puis sur le nom du compte de stockage pour afficher le tableau de bord.

2. Cliquez sur **Configurer**.

3. Dans le champ **Réplication**, sélectionnez le type de réplication souhaité pour votre compte de stockage.

4. Cliquez sur **Enregistrer**, puis confirmez votre choix si vous y êtes invité.


<h2><a id="regeneratestoragekeys"></a>Procédure : Affichage, copie et régénération de clés d'accès de stockage</h2>
Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

Dans le [portail de gestion](http://manage.windowsazure.com), utilisez **Gérer les clés** du tableau de bord ou la page **Stockage** pour afficher, copier et régénérer les clés d'accès de stockage qui servent à accéder aux services Blob, de Table et de File d'attente. 

### Copie d'une clé d'accès de stockage ###

L'option **Gérer les clés** sert à copier une clé d'accès de stockage à utiliser dans une chaîne de connexion. Cette dernière nécessite le nom du compte de stockage et une clé pour l'authentification. Pour plus d'informations sur la configuration des chaînes de connexion pour accéder aux services de stockage Azure, consultez la page [Configuration des chaînes de connexion](http://msdn.microsoft.com/fr-FR/library/ee758697.aspx).

1. Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **Stockage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Gérer les clés**.

 	La fenêtre **Gestion des clés d'accès** s'affiche.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

 
3. Pour copier une clé d'accès de stockage, sélectionnez le texte de la clé. Cliquez ensuite avec le bouton droit, puis cliquez sur **Copier**.

### Régénération des clés d'accès de stockage ###
Vous devez modifier périodiquement les clés d'accès à votre compte de stockage pour garantir la sécurité des connexions de stockage. Vous bénéficiez de deux clés d'accès, ce qui vous permet de conserver des connexions au compte de stockage à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé. 

> [AZURE.WARNING] La régénération des clés d'accès a une incidence sur les machines virtuelles, les services de médias et toutes les applications qui dépendent du compte de stockage. Tous les clients qui utilisent la clé d'accès pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Machines virtuelles** : si votre compte de stockage contient des machines virtuelles en cours d'exécution, vous devez redéployer toutes les machines virtuelles après avoir régénéré les clés d'accès. Pour éviter le redéploiement, arrêtez les machines virtuelles avant de régénérer les clés d'accès.
 
**Services de médias** : si des services de médias sont liés à votre compte de stockage, vous devez resynchroniser les clés d'accès avec ces services après avoir régénéré les clés.
 
**Applications** : si des applications Web ou des services cloud utilisent le compte de stockage, vous perdez les connexions en régénérant les clés, à moins de les substituer. Pour ce faire :

1. Mettez à jour les chaînes de connexion dans votre code d'application pour désigner la clé d'accès secondaire du compte de stockage. 

2. Régénérez la clé d'accès principale de votre compte de stockage. Dans le [portail de gestion](http://manage.windowsazure.com), depuis le tableau de bord ou la page **Configurer**, cliquez sur **Manage Keys**. Cliquez sur **Régénérer** sous la clé d'accès principal, puis sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d'accès principale.

4. Régénérez la clé d'accès secondaire.


<h2><a id="deletestorageaccount"></a>Procédure : Suppression d'un compte de stockage</h2>

Pour supprimer un compte de stockage que vous n'utilisez plus, utilisez **Supprimer** sur le tableau de bord ou la page **Configurer**. **L'option Supprimer** a pour effet d'éliminer l'intégralité du compte de stockage, y compris tous les objets blob, les tables et les files d'attente du compte. 

> [AZURE.WARNING] Il n'existe aucun moyen de restaurer le contenu d'un compte de stockage supprimé. Assurez-vous de sauvegarder tout ce que vous souhaitez enregistrer avant de supprimer le compte.
> 
> Si votre compte de stockage contient des fichiers de disque dur virtuel ou des disques pour une machine virtuelle Azure, vous devez supprimer l'ensemble des images et des disques qui utilisent ces fichiers avant de pouvoir supprimer le compte de stockage. Arrêtez au préalable la machine virtuelle si elle est en cours d'exécution, puis supprimez-la. Pour supprimer des disques, accédez à l'onglet Disques et supprimez tous les disques contenus dans le compte de stockage. Pour supprimer des images, accédez à l'onglet Images et supprimez toutes les images stockées dans le compte.

1. Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **Stockage**.

2. Cliquez sur un endroit quelconque de l'entrée du compte de stockage, à l'exception du nom, puis sur **Supprimer**.

	 -Ou-

	Cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord, puis cliquez sur **Supprimer**.

3. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer le compte de stockage.

<!--HONumber=42-->
