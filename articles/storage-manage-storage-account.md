<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Gestion des comptes de stockage
===============================

Sommaire
--------

-   [Gestion de la réplication du compte de stockage](#georeplication)
-   [Affichage, copie et régénération de clés d'accès de stockage](#regeneratestoragekeys)
-   [Suppression d'un compte de stockage](#deletestorageaccount)

Gestion de la réplication du compte de stockage
-----------------------------------------------

Trois options s'offrent à vous pour la réplication de votre compte de stockage :

-   **Réplication géo-redondante.** Cette fonctionnalité est activée par défaut pour votre compte de stockage. Avec la réplication géo-redondante, vos données sont répliquées dans un emplacement secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur dans l'emplacement principal. L'emplacement secondaire se situe dans la même région, mais à des centaines de kilomètres de l'emplacement principal.

-   **Réplication géo-redondante avec accès en lecture.** Cette fonctionnalité réplique vos données dans un emplacement secondaire et permet d'y accéder en lecture. La réplication géo-redondante avec accès en lecture offre un accès aux données, qu'elles se situent dans l'emplacement principal ou secondaire, si l'un des deux n'est pas disponible.

-   **Réplication localement redondante**. Cette fonctionnalité permet de répliquer trois fois les données de votre compte de stockage dans le même centre de données. Elle vous permet de bénéficier de tarifs réduits.

    Cependant, sachez que si vous spécifiez la réplication localement redondante pour votre compte de stockage et décidez d'activer la réplication géo-redondante ultérieurement, la réplication de vos données existantes vers l'emplacement secondaire sera facturée au tarif d'un transfert unique.

-   **Réplication géo-redondante avec accès en lecture (version préliminaire seulement).** Cette fonctionnalité réplique vos données dans un emplacement secondaire et permet d'y accéder en lecture. La réplication géo-redondante avec accès en lecture offre un accès aux données, qu'elles se situent dans l'emplacement principal ou secondaire, si l'un des deux n'est pas disponible.

Pour plus d'informations sur la tarification relative à la réplication du compte de stockage, consultez la page [Tarification – Stockage](http://www.windowsazure.com/fr-fr/pricing/details/storage/).

### Pour spécifier les paramètres de réplication d'un compte de stockage

1.  Dans le [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Storage**, puis sur le nom du compte de stockage pour afficher le tableau de bord.

2.  Cliquez sur **Configurer**.

3.  Dans le champ **Réplication**, sélectionnez le type de réplication souhaité pour votre compte de stockage.

4.  Cliquez sur **Enregistrer**, puis confirmez votre choix si vous y êtes invité.

Affichage, copie et régénération de clés d'accès de stockage
------------------------------------------------------------

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

Dans le [portail de gestion](http://manage.windowsazure.com), utilisez **Manage Keys** sur le tableau de bord ou la page **Storage** pour afficher, copier et régénérer les clés d'accès de stockage qui servent à accéder aux services Blob, de Table et de File d'attente.

### Copie d'une clé d'accès de stockage

L'option **Manage Keys** sert à copier une clé d'accès de stockage à utiliser dans une chaîne de connexion. Cette dernière nécessite le nom du compte de stockage et une clé pour l'authentification. Pour plus d'informations sur la configuration des chaînes de connexion pour accéder aux services de stockage Azure, consultez la page [Configuration des chaînes de connexion Azure](http://msdn.microsoft.com/fr-fr/library/ee758697.aspx).

1.  Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2.  Cliquez sur **Manage Keys**.

 	**Manage Access Keys** s'affiche.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

3. Pour copier une clé d'accès de stockage, sélectionnez le texte de la clé. Cliquez ensuite avec le bouton droit, puis cliquez sur **Copier**.

### Régénération des clés d'accès de stockage

Vous devez modifier périodiquement les clés d'accès à votre compte de stockage pour garantir la sécurité des connexions de stockage. Vous bénéficiez de deux clés d'accès, ce qui vous permet de conserver des connexions au compte de stockage à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

**Avertissement**

La régénération des clés d'accès a une incidence sur les machines virtuelles, les services de médias et toutes les applications qui dépendent du compte de stockage.

**Machines virtuelles** : si votre compte de stockage contient des machines virtuelles en cours d'exécution, vous devez redéployer toutes les machines virtuelles après avoir régénéré les clés d'accès. Pour éviter le redéploiement, arrêtez les machines virtuelles avant de régénérer les clés d'accès.

**Services de médias** : si des services de médias sont liés à votre compte de stockage, vous devez resynchroniser les clés d'accès avec ces services après avoir régénéré les clés.

**Applications** : si des applications Web ou des services cloud utilisent le compte de stockage, vous perdez les connexions en régénérant les clés, à moins de les substituer. Pour ce faire :

1.  Mettez à jour les chaînes de connexion dans votre code d'application pour désigner la clé d'accès secondaire du compte de stockage.

2.  Régénérez la clé d'accès principale de votre compte de stockage. Dans le [portail de gestion](http://manage.windowsazure.com), depuis le tableau de bord ou la page **Configurer**, cliquez sur **Manage Keys**. Cliquez sur **Régénérer** sous la clé d'accès principal, puis sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3.  Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d'accès principale.

4.  Régénérez la clé d'accès secondaire.

Suppression d'un compte de stockage
-----------------------------------

Pour supprimer un compte de stockage obsolète, utilisez **Supprimer** sur le tableau de bord ou la page **Configurer**. **Supprimer** a pour effet d'éliminer l'intégralité du compte de stockage, y compris tous les objets blob, les tables et les files d'attente du compte.

**Avertissement**

Il n'existe aucun moyen de restaurer le contenu d'un compte de stockage supprimé. Assurez-vous de sauvegarder tout ce que vous souhaitez enregistrer avant de supprimer le compte.

Si votre compte de stockage contient des fichiers de disque dur virtuel ou des disques pour une machine virtuelle Azure, vous devez supprimer l'ensemble des images et des disques qui utilisent ces fichiers avant de pouvoir supprimer le compte de stockage. Arrêtez au préalable la machine virtuelle si elle est en cours d'exécution, puis supprimez-la. Pour supprimer des disques, accédez à l'onglet Disques et supprimez tous les disques contenus dans le compte de stockage. Pour supprimer des images, accédez à l'onglet Images et supprimez toutes les images stockées dans le compte.

1.  Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **Storage**.

2.  Cliquez sur un endroit quelconque de l'entrée du compte de stockage, à l'exception du nom, puis sur **Supprimer**.

    -Ou-

    Cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord, puis cliquez sur **Supprimer**.

3.  Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer le compte de stockage.


