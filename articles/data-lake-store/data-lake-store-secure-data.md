<properties 
   pageTitle="Sécurisation des données stockées dans Azure Data Lake Store | Microsoft Azure" 
   description="Apprenez à sécuriser les données dans Azure Data Lake Store à l'aide de groupes et de listes de contrôle d'accès" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/22/2016"
   ms.author="nitinme"/>

# Sécurisation des données stockées dans Azure Data Lake Store

La sécurisation des données dans Azure Data Lake Store se fait en trois étapes.

1. Commencez par créer des groupes de sécurité dans Azure Active Directory (AAD). Ces groupes de sécurité sont utilisés pour implémenter le contrôle d'accès en fonction du rôle (RBAC) dans le portail Azure. Pour plus d'informations, consultez la page [Contrôle d'accès en fonction du rôle dans Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Affectez les groupes de sécurité AAD au compte Azure Data Lake Store. Ceci contrôle l'accès au compte Data Lake Store à partir du portail et les opérations de gestion à partir du portail ou des API.

3. Affectez les groupes de sécurité AAD comme listes de contrôle d'accès (ACL) sur le système de fichiers Data Lake Store.

4. En outre, vous pouvez définir une plage d’adresses IP pour les clients pouvant accéder aux données de Data Lake Store.

Cet article explique comment utiliser le portail Azure pour effectuer les tâches ci-dessus. Pour obtenir des informations détaillées sur la manière dont le Data Lake Store implémente la sécurité au niveau du compte et des données, consultez [Sécurité dans Azure Data Lake Store](data-lake-store-security-overview.md).

## Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)

## Les vidéos vous permettent-elles d’apprendre rapidement ?

[Regardez cette vidéo](https://mix.office.com/watch/1q2mgzh9nn5lx) sur la sécurisation des données stockées dans Data Lake Store.

## Créer des groupes de sécurité dans Azure Active Directory

Pour obtenir des instructions sur la création de groupes de sécurité AAD et l'ajout d'utilisateurs au groupe, consultez [Gestion des groupes de sécurité dans Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## Affecter les utilisateurs ou les groupes de sécurité aux comptes Azure Data Lake Store

Lorsque vous affectez des utilisateurs ou des groupes de sécurité aux comptes Azure Data Lake Store, vous contrôlez l'accès aux opérations de gestion sur le compte à l'aide du portail Azure et des API Azure Resource Manager.

1. Ouvrez un compte Azure Data Lake Store. Dans le volet gauche, cliquez sur **Parcourir**, puis sur **Data Lake Store**. Ensuite, dans le panneau Data Lake Store, cliquez sur le nom du compte auquel vous souhaitez affecter un utilisateur ou un groupe de sécurité.

2. Dans le panneau de votre compte Data Lake Store, cliquez sur l'icône de l'utilisateur.

	![Affecter un groupe de sécurité à un compte Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter un groupe de sécurité à un compte Azure Data Lake Store")

3. Le panneau **Utilisateur** liste par défaut le groupe **Administrateurs de l'abonnement** en tant que propriétaire.

	![Ajouter des utilisateurs et des rôles](./media/data-lake-store-secure-data/adl.add.group.roles.png "Ajouter des utilisateurs et des rôles")
 
	Il existe deux façons d'ajouter un groupe et d'affecter des rôles appropriés.

	* Ajouter un utilisateur/groupe au compte, puis lui affecter un rôle, ou
	* Ajouter un rôle, puis affecter les utilisateurs/groupes au rôle.

	Dans cette section, nous examinons la première approche : ajouter un groupe, puis affecter les rôles. Vous pouvez suivre les mêmes étapes pour commencer par sélectionner un rôle puis affecter des groupes à ce rôle.
	
4. Dans le panneau **Utilisateurs**, cliquez sur **Ajouter** pour ouvrir le panneau **Ajouter un accès**. Dans le panneau **Ajouter un accès**, cliquez sur **Sélectionner un rôle**, puis sélectionnez un rôle pour l'utilisateur ou le groupe.

	 ![Ajouter un rôle à l'utilisateur](./media/data-lake-store-secure-data/adl.add.user.1.png "Ajouter un rôle à l'utilisateur")

	Les rôles **Propriétaire** et **Collaborateur** fournissent l'accès à différentes fonctions d'administration sur le compte du lac de données. En ce qui concerne les utilisateurs qui interagissent avec les données dans le lac de données, vous pouvez les ajouter au rôle **Lecteur**. L'étendue de ces rôles est limitée aux opérations de gestion associées au compte Azure Data Lake Store.

	Pour les opérations sur les données, des autorisations pour chaque système de fichiers définissent ce que les utilisateurs peuvent faire. Par conséquent, un utilisateur ayant un rôle de Lecteur peut afficher uniquement les paramètres d'administration associés au compte mais peut potentiellement lire et écrire des données en fonction des autorisations du système de fichiers qui lui sont affectées. Les autorisations de système de fichiers Data Lake Store sont décrites dans [Affecter un groupe de sécurité comme ACL au système de fichiers Azure Data Lake Store](#filepermissions).



5. Dans le panneau **Ajouter un accès**, cliquez sur **Ajouter des utilisateurs** pour ouvrir le panneau **Ajouter un accès**. Dans ce panneau, recherchez le groupe de sécurité que vous avez créé précédemment dans Azure Active Directory. Si la liste de groupes est longue, utilisez la zone de texte en haut pour filtrer par nom de groupe. Cliquez sur **Sélectionner**.

	![Ajouter un groupe de sécurité](./media/data-lake-store-secure-data/adl.add.user.2.png "Ajouter un groupe de sécurité")

	Si vous souhaitez ajouter un utilisateur ou un groupe qui n'est pas répertorié, vous pouvez l'inviter en utilisant l'icône **Inviter** et en spécifiant l'adresse de messagerie de l'utilisateur ou du groupe.

6. Cliquez sur **OK**. Vous devez voir le groupe de sécurité ajouté comme indiqué ci-dessous.

	![Groupe de sécurité ajouté](./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité ajouté")

7. Votre groupe de sécurité ou utilisateur a désormais accès au compte Azure Data Lake Store. Si vous souhaitez fournir un accès à des utilisateurs en particulier, vous pouvez les ajouter au groupe de sécurité. De même, si vous souhaitez révoquer l'accès d'un utilisateur, vous pouvez le supprimer du groupe de sécurité. Vous pouvez également affecter plusieurs groupes de sécurité à un compte.

## <a name="filepermissions"></a>Affecter des utilisateurs ou un groupe de sécurité comme ACL au système de fichiers Azure Data Lake Store

En affectant des groupes de sécurité ou des utilisateurs au système de fichiers Azure Data Lake, vous définissez un contrôle d'accès aux données stockées dans Azure Data Lake Store.

>[AZURE.NOTE] Dans la version actuelle, vous pouvez définir des ACL uniquement au nœud racine de votre compte Data Lake Store. En outre, seuls les utilisateurs ayant le rôle Propriétaire peuvent ajouter/modifier des listes de contrôle d’accès.

1. Dans le panneau de votre compte Data Lake Store, cliquez sur **Explorateur de données**.

	![Créer des répertoires dans un compte Data Lake Store](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer des répertoires dans un compte Data Lake")

2. Dans le panneau **Explorateur de données**, cliquez sur la racine de votre compte puis, dans le panneau de votre compte, cliquez sur l'icône **Accès**.

	![Définir des ACL sur le système de fichiers Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Définir des ACL sur le système de fichiers Data Lake")

3. Le panneau **Accès** liste les accès standard et personnalisés déjà affectés à la racine. Cliquez sur l'icône **Ajouter** pour ajouter des ACL personnalisées.

	![Lister les accès standard et personnalisés](./media/data-lake-store-secure-data/adl.acl.2.png "Lister les accès standard et personnalisés")

	* **L’accès standard** est l’accès de type UNIX, où vous spécifiez la lecture, l’écriture et l’exécution (rwx) pour trois classes d’utilisateurs distinctes : propriétaire, groupe et autres.
	* **L’accès personnalisé** correspond aux listes de contrôle d’accès (ACL) POSIX, et vous permet de définir des autorisations pour des utilisateurs ou des groupes nommés spécifiques, et pas seulement pour le propriétaire du fichier ou le groupe.
	
	Pour plus d'informations, consultez la page [ACL HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists).

4. Cliquez sur l'icône **Ajouter** pour ouvrir le panneau **Ajouter un accès personnalisé**. Dans ce panneau, cliquez sur **Sélectionner un utilisateur ou un groupe** puis, dans le panneau **Sélectionner un utilisateur ou un groupe**, recherchez le groupe de sécurité créé précédemment dans Azure Active Directory. Si la liste de groupes est longue, utilisez la zone de texte en haut pour filtrer par nom de groupe. Cliquez sur le groupe que vous souhaitez ajouter, puis cliquez sur **Sélectionner**.

	![Ajouter un groupe](./media/data-lake-store-secure-data/adl.acl.3.png "Ajouter un groupe")

5. Cliquez sur **Sélectionner les autorisations**, sélectionnez les autorisations que vous souhaitez attribuer à ce groupe, puis cliquez sur **OK**.

	![Affecter des autorisations à un groupe](./media/data-lake-store-secure-data/adl.acl.4.png "Affecter des autorisations à un groupe")

	Les autorisations peuvent être comprises comme suit :

	* **Lire** : si cette autorisation est définie sur un répertoire, elle donne la possibilité de lire les noms des fichiers dans le répertoire.
	* **Écrire** : si cette autorisation est définie sur un répertoire, elle donne la possibilité de modifier les entrées dans le répertoire, comme créer un fichier, supprimer un fichier ou renommer un fichier.
	* **Exécuter** : si cette autorisation est définie sur un répertoire, elle donne la possibilité d’accéder au contenu du fichier dans le répertoire. Elle donne également accès aux métadonnées du fichier, si le nom de fichier est connu. Cependant, cette autorisation ne vous autorise pas à répertorier les fichiers dans le répertoire, sauf si l’autorisation **Lire** est également définie.

	>[AZURE.NOTE] L’autorisation **Lire + Exécuter** est requise pour l’énumération des répertoires, et elle est souvent requise quand vous fournissez un accès en lecture seule aux données à un utilisateur ou à un groupe.


6. Dans le panneau **Ajouter un accès personnalisé**, cliquez sur **OK**. Le groupe récemment créé, avec les autorisations associées, sera désormais répertorié dans le panneau **Accès**.

	![Affecter des autorisations à un groupe](./media/data-lake-store-secure-data/adl.acl.5.png "Affecter des autorisations à un groupe")

	> [AZURE.IMPORTANT] Dans la version actuelle, vous pouvez avoir seulement 9 entrées sous **Accès personnalisé**. Si vous souhaitez ajouter plus de 9 utilisateurs, vous devez créer des groupes de sécurité, ajouter les utilisateurs aux groupes de sécurité et fournir à ces groupes de sécurité un accès au compte Data Lake Store.

7. Si nécessaire, vous pouvez également modifier les autorisations d'accès après avoir ajouté le groupe. Cochez ou décochez la case de chaque type d'autorisation (lecture, écriture, exécution) selon que vous souhaitez retirer ou affecter cette autorisation au groupe de sécurité. Cliquez sur **Enregistrer** pour enregistrer les modifications, ou sur **Ignorer** pour annuler les modifications.

## Définir la plage d’adresses IP pour accéder aux données

Azure Data Lake Store vous permet de mieux verrouiller l’accès à votre magasin de données au niveau du réseau. Vous pouvez activer le pare-feu, spécifier une adresse IP ou définir une plage d’adresses IP pour vos clients approuvés. Une fois qu’il est activé, seuls les clients qui possédant des adresses IP dans la plage définie peuvent se connecter au magasin.

![Paramètres de pare-feu et accès IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Paramètres de pare-feu et adresse IP")

## Supprimer les groupes de sécurité d'un compte Azure Data Lake Store

Lorsque vous supprimez des groupes de sécurité de comptes Azure Data Lake Store, vous ne modifiez que l’accès aux opérations de gestion sur le compte à l’aide du portail Azure et des API Azure Resource Manager.

1. Dans le panneau de votre compte Data Lake Store, cliquez sur l'icône de l'utilisateur.

	![Affecter un groupe de sécurité à un compte Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter un groupe de sécurité à un compte Azure Data Lake")

2. Dans le panneau **Utilisateurs**, cliquez sur le groupe de sécurité que vous souhaitez supprimer.

	![Groupe de sécurité à supprimer](./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité à supprimer")

3. Dans le panneau du groupe de sécurité, cliquez sur **Supprimer**.

	![Groupe de sécurité supprimé](./media/data-lake-store-secure-data/adl.remove.group.png "Groupe de sécurité supprimé")

## Supprimer les ACL d'un groupe de sécurité du système de fichiers Azure Data Lake Store

Lorsque vous supprimez des ACL de groupes de sécurité du système de fichiers Azure Data Lake Store, vous modifiez l'accès aux données du Data Lake Store.

1. Dans le panneau de votre compte Data Lake Store, cliquez sur **Explorateur de données**.

	![Créer des répertoires dans un compte Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer des répertoires dans un compte Data Lake")

2. Dans le panneau **Explorateur de données**, cliquez sur la racine de votre compte puis, dans le panneau de votre compte, cliquez sur l'icône **Accès**.

	![Définir des ACL sur le système de fichiers Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Définir des ACL sur le système de fichiers Data Lake")

3. Dans le panneau **Accès**, à partir de la section **Accès personnalisé**, cliquez sur le groupe de sécurité que vous souhaitez supprimer. Dans le panneau **Accès personnalisé**, cliquez sur **Supprimer**, puis sur **OK**.

	![Affecter des autorisations à un groupe](./media/data-lake-store-secure-data/adl.remove.acl.png "Affecter des autorisations à un groupe")


## Voir aussi

- [Présentation d'Azure Data Lake Store](data-lake-store-overview.md)
- [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Prise en main de Data Lake Store avec PowerShell](data-lake-store-get-started-powershell.md)
- [Prise en main de Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)

<!---HONumber=AcomDC_0629_2016-->