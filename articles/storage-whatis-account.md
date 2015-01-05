<properties urlDisplayName="About Azure Storage Accounts" pageTitle="À propos des comptes Azure Storage | Microsoft Azure" metaKeywords="" description="Learn about working with Azure storage accounts." metaCanonical="" services="storage" documentationCenter="" title="About Azure Storage Accounts" authors="tamram" solutions="" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />


# À propos des comptes Azure Storage

Un compte de stockage Azure est un compte sécurisé qui vous donne accès à des services dans Azure Storage. Votre compte de stockage fournit l'espace de noms unique pour vos données, et par défaut vous seul (en tant que propriétaire du compte) pouvez y accéder. 

Il existe deux types de comptes de stockage :

- un compte de stockage standard qui inclut le stockage d'objets blob, de tables et de files d'attente. Le stockage de fichiers est disponible via la [page Azure Preview](/fr-fr/services/preview/).
- Un compte de stockage premium prend actuellement en charge uniquement les disques Machine virtuelle Azure. Azure Premium Storage est disponible sur demande via la [page Azure Preview](/fr-fr/services/preview/). Consultez [Stockage premium : Stockage hautes performances pour les charges de travail de machines virtuelles Azure](http://go.microsoft.com/fwlink/?LinkId=521898) pour obtenir une présentation détaillées du stockage premium.

Le stockage Azure vous est facturé en fonction de votre compte de stockage. Les coûts de stockage sont basés sur quatre facteurs : capacité de stockage, modèle de réplication, transactions de stockage et acheminement des données. 

- La capacité de stockage fait référence à l'unité de compte de stockage que vous utilisez pour stocker des données. Le coût d'un simple stockage de vos données est déterminé par la quantité de données que vous stockez et leur type de réplication. 
- La réplication détermine le nombre de copies de vos données qui sont conservées simultanément, et à quels emplacements. 
- Les transactions font références à toutes les opérations de lecture et d'écriture vers Azure Storage. 
- L'acheminement des données fait référence aux données transférées hors d'une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n'est pas exécutée dans la même région, que cette application soit un service cloud ou un autre type d'application, vous êtes facturé pour l'acheminement des données (pour les services Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais d'acheminement des données).  

La page [Tarification - Stockage](http://www.windowsazure.com/fr-fr/pricing/details/#storage) fournit des informations de tarification détaillées pour les capacités de stockage, la réplication et les transactions. La page [Détails de la tarification - Transferts de données](http://www.windowsazure.com/fr-fr/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour l'acheminement des données.

Pour plus d'informations sur la capacité et les cibles de performances du compte de stockage, consultez la page [Objectifs d'évolutivité et de performances d'Azure Storage](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx).

> [WACOM.NOTE] Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé automatiquement pour vous sur le lieu de déploiement si vous ne disposez pas déjà de ce type de compte à cet emplacement. Il n'est donc pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques de machines virtuelles. Le nom du compte de stockage dépend du nom de la machine virtuelle. Consultez la [documentation sur les machines virtuelles Azure](/fr-fr/documentation/services/virtual-machines/) pour obtenir plus de détails. <br />

## Sommaire ##

Cet article décrit comment créer un compte de stockage standard et présente certaines décisions que vous devrez prendre. Il explique aussi comment gérer vos clés d'accès de compte de stockage et comment supprimer un compte de stockage.


- [Procédure : Création d'un compte de stockage](#create)
- [Procédure : Affichage, copie et régénération de clés d'accès de stockage](#regeneratestoragekeys)
- [Procédure : Suppression d'un compte de stockage](#deletestorageaccount)
* [Étapes suivantes](#next)

## <a id="create"></a>Utilisation Créer un compte de stockage ##

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).

2. Cliquez sur **Créer un nouveau**, sur **Stockage**, puis sur **Création rapide**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Dans **URL**, entrez un nom pour votre compte de stockage. Consultez [Points de terminaison d'un compte de stockage](#account-endpoints) ci-dessous pour obtenir plus de détails sur la façon dont ce nom sera utilisé pour gérer les objets que vous stockerez dans Azure Storage.

4. Dans **Groupe d'emplacements ou d'affinités**, sélectionnez un emplacement pour votre compte de stockage à proximité de vos clients ou de vous-même. Si vous accèderez aux données dans votre stockage à partir d'un autre service Azure, par exemple une machine virtuelle Azure ou un service cloud, vous souhaiterez peut-être sélectionner un groupe d'affinités dans la liste afin de placer votre compte de stockage dans le même centre de données que d'autres services Azure que vous utilisez pour améliorer les performances et réduire les coûts. 

	> [WACOM.NOTE] Notez que vous devez sélectionner un groupe d'affinités lors de la création de votre compte de stockage ; vous ne pouvez pas déplacer un compte existant vers un groupe d'affinités.

	Pour plus de détails sur les groupes d'affinités, consultez [Colocalisation de service avec un groupe d'affinités](#affinity-group) ci-dessous.

	
5. Si vous disposez de plusieurs abonnements Azure, le champ **Abonnement** s'affiche. Dans **Abonnement**, entrez l'abonnement Azure à utiliser avec le compte de stockage. Vous pouvez créer jusqu'à cinq comptes de stockage par abonnement.

6. Dans **Réplication**, sélectionnez le niveau de réplication souhaité pour votre compte de stockage. L'option de réplication recommandée est Réplication géo-redondante, car elle procure une durabilité maximale pour vos données. Pour plus de détails sur les options de réplication Azure Storage, consultez [Options de réplication des comptes de stockage](#replication-options) ci-dessous.

6. Cliquez sur **Créer un compte de stockage**.

	La création de votre compte de stockage peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Une fois le compte de stockage créé, le statut **En ligne** lui est associé et il est prêt à être utilisé. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Points de terminaison d'un compte de stockage 

Chaque objet que vous stockez dans Azure Storage possède une adresse URL unique ; le nom du compte de stockage forme le sous-domaine de cette adresse. Le sous-domaine, associé au nom de domaine (qui est spécifique à chaque service) forme un  *point de terminaison* pour votre compte de stockage. 

Par exemple, si votre compte de stockage se nomme *mystorageaccount*, les points de terminaison par défaut pour votre compte de stockage sont les suivants : 

- Service d'objets blob: http://*mystorageaccount*.blob.core.windows.net

- Service de Table: http://*mystorageaccount*.table.core.windows.net

- Service de File d'attente: http://*mystorageaccount*.queue.core.windows.net

- Service de fichiers: http://*mystorageaccount*.file.core.windows.net

Les points de terminaison pour votre compte de stockage sont visibles dans le tableau de bord de stockage du portail de gestion Azure une fois le compte créé.

L'URL permettant d'accéder à un objet dans un compte de stockage est constituée de l'ajout de l'emplacement de l'objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d'objet blob peut avoir ce format: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez aussi configurer un nom de domaine personnalisé à utiliser avec votre compte de stockage. Consultez [Configuration d'un nom de domaine personnalisé pour des données d'objets blob dans un compte de stockage](../storage-custom-domain-name/) pour plus de détails.

### <a id="affinity-group"></a>Colocalisation de service avec un groupe d'affinités 

Un *groupe d'affinités* est un regroupement géographique de vos services et machines virtuelles Azure avec votre compte de stockage Azure. Un groupe d'affinités peut améliorer les performances d'un service en situant les charges de travail d'un ordinateur dans le même centre de données ou près des utilisateurs cibles. De même, vous n'êtes pas facturé pour les acheminements lorsque les données d'un compte de stockage sont utilisées par un service faisant partie du même groupe d'affinités.

> [WACOM.NOTE]  Pour créer un groupe d'affinités, ouvrez la zone <b>Paramètres</b> du portail de gestion, cliquez sur <b>Groupe d'affinités</b>, puis sur <b>Ajouter un groupe d'affinités</b> ou <b>Ajouter</b> . Vous pouvez également créer et gérer des groupes d'affinités par le biais de l'API Azure Service Management. Consultez <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460798.aspx">Opérations sur les groupes d'affinités</a> pour plus d'informations.


### <a id="replication-options"></a>Options de réplication des comptes de stockage

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Utilisation Affichage, copie et régénération de clés d'accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

> [WACOM.NOTE] Nous vous recommandons d'éviter de partager les clés d'accès à votre compte de stockage avec qui que ce soit. Pour autoriser l'accès aux ressources de stockage sans donner vos clés d'accès, vous pouvez utiliser une *signature d'accès partagé*. Celle-ci donne accès à votre compte à une ressource pendant une durée que vous déterminez et avec les autorisations que vous spécifiez. Consultez le [didacticiel sur les signatures d'accès partagé](../storage-dotnet-shared-access-signature-part-1/) pour plus d'informations.

Dans le [portail de gestion](http://manage.windowsazure.com), utilisez **Gérer les clés** sur le tableau de bord ou la page **Stockage** pour afficher, copier et régénérer les clés d'accès de stockage qui servent à accéder aux services Blob, de Table et de File d'attente. 

### Copie d'une clé d'accès de stockage ###

Vous pouvez utiliser **Gérer les clés** pour copier une clé d'accès de stockage à utiliser dans une chaîne de connexion. Cette dernière nécessite le nom du compte de stockage et une clé pour l'authentification. Pour plus d'informations sur la configuration des chaînes de connexion pour accéder aux services de stockage Azure, consultez la page [Configuration des chaînes de connexion Azure](http://msdn.microsoft.com/fr-fr/library/ee758697.aspx).

1. Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **Stockage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord..

2. Cliquez sur **Gérer les clés**.

 	**Gérer les clés d'accès** s'ouvre.

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
3. Pour copier une clé d'accès de stockage, sélectionnez le texte de la clé. Cliquez ensuite avec le bouton droit, puis cliquez sur **Copier**.

### Régénération des clés d'accès de stockage ###
Vous devez modifier périodiquement les clés d'accès à votre compte de stockage pour garantir la sécurité des connexions de stockage. Vous bénéficiez de deux clés d'accès, ce qui vous permet de conserver des connexions au compte de stockage à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé. 

<div class="dev-callout"> 
    <b>Avertissement</b> 
    <p>La régénération des clés d'accès a une incidence sur les machines virtuelles, les services de médias et toutes les applications qui dépendent du compte de stockage. Tous les clients qui utilisent la clé d'accès pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.
    </p> 
    </div>

**Machines virtuelles** : si votre compte de stockage contient des machines virtuelles en cours d'exécution, vous devez redéployer toutes les machines virtuelles après avoir régénéré les clés d'accès. Pour éviter le redéploiement, arrêtez les machines virtuelles avant de régénérer les clés d'accès.
 
**Services de médias** : si des services de médias sont liés à votre compte de stockage, vous devez resynchroniser les clés d'accès avec ces services après avoir régénéré les clés.
 
**Applications** : si des applications Web ou des services cloud utilisent le compte de stockage, vous perdez les connexions en régénérant les clés, à moins de les substituer. Pour ce faire :

1. Mettez à jour les chaînes de connexion dans votre code d'application pour désigner la clé d'accès secondaire du compte de stockage. 

2. Régénérez la clé d'accès principale de votre compte de stockage. Dans le [portail de gestion](http://manage.windowsazure.com), à partir du tableau de bord ou de la page **Configurer**, cliquez sur **Gérer les clés**. Cliquez sur **Régénérer** sous la clé d'accès principal, puis sur  **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d'accès principale.

4. Régénérez la clé d'accès secondaire.

## <a id="deletestorageaccount"></a>Utilisation Suppression d'un compte de stockage

Pour supprimer un compte de stockage obsolète, utilisez **Supprimer** dans le tableau de bord ou la page  **Configurer**. **Supprimer** a pour effet d'éliminer l'intégralité du compte de stockage, y compris tous les objets blob, les tables et les files d'attente du compte. 

<div class="dev-callout">
	<b>Avertissement</b>
	<p>Il n'existe aucun moyen de restaurer le contenu d'un compte de stockage supprimé. Assurez-vous 
	de sauvegarder tout ce que vous souhaitez enregistrer avant de supprimer le compte.
	</p>
	<p>
	Si votre compte de stockage contient des fichiers de disque dur virtuel 
	ou des disques pour une machine virtuelle Azure, vous devez supprimer l'ensemble des images 
	et des disques qui utilisent ces fichiers avant de pouvoir supprimer le compte de stockage. Arrêtez au préalable la machine virtuelle si elle est en cours d'exécution, puis supprimez-la. Pour supprimer des disques, accédez à l'onglet Disques et supprimez tous les disques contenus dans le compte de stockage. Pour supprimer des images, accédez à l'onglet Images et supprimez toutes les images stockées dans le compte.
	</p>
</div>


1. Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **Stockage**.

2. Cliquez n'importe où dans l'entrée du compte de stockage, à l'exception du nom, puis sur **Supprimer**.

	 -Ou-

	Cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord, puis cliquez sur **Supprimer**.

3. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer le compte de stockage.

## <a id="next"></a>Étapes suivantes

- Pour en savoir plus sur Azure Storage, consultez la documentation de référence sur [azure.com](http://azure.microsoft.com/fr-fr/documentation/services/storage/) et [MSDN](http://msdn.microsoft.com/fr-fr/library/gg433040.aspx). 

- Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

 


<!--HONumber=35.1-->
