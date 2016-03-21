<properties
   pageTitle="Prendre en main l’audit de base de données SQL Data Warehouse | Microsoft Azure"
   description="Prendre en main de l’audit de base de données SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/03/2016" 
   ms.author="mausher;barbkess;sonyama"/>

# Prendre en main de l’audit de base de données SQL Data Warehouse
L'audit Azure SQL Data Warehouse effectue le suivi des événements de base de données et consigne les événements audités dans un journal dans votre compte Azure Storage.

L'audit peut vous aider à respecter une conformité réglementaire, à comprendre l'activité de la base de données et à découvrir des discordances et anomalies susceptibles d'indiquer des problèmes pour l'entreprise ou des violations de la sécurité.

Les outils d'audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d'informations sur les programmes Azure prenant en charge la conformité aux normes, consultez le <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centre de gestion de la confidentialité Azure</a>.

+ [Principes fondamentaux de l’audit de base de données]
+ [Configuration de l'audit pour votre base de données]
+ [Analyse des journaux et des rapports d’audit]

##<a id="subheading-1"></a>Principes fondamentaux de l’audit de base de données Azure SQL Data Warehouse


Éléments rendus possibles par l’audit de bases de données SQL Data Warehouse :

- **La rétention** d'une piste d'audit d'événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
- **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
- **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Vous pouvez configurer l'audit pour les catégories d'événements suivantes :

**SQL ordinaire** et **SQL paramétré** pour lesquels les journaux d'audit collectés sont classés comme

- **accès aux données ;**
- **modifications de schéma (DDL) ;**
- **modifications de données (DML) ;**
- **comptes, rôles et autorisations (DCL) ;**
- **Procédure stockée**, **connexion** et **Gestion des transactions**.

Pour chaque catégorie d'événements, les audits des opérations **Succès** et **Échec** sont configurées séparément.

Pour d'autres informations sur les activités et événements audités, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Référence sur le format du journal d'audit (téléchargement d'un fichier doc)</a>.

Les journaux d'audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention des journaux d'audit.

Une stratégie d'audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut. Une stratégie d'audit de serveur par défaut s'appliquera à toutes les bases de données d’un serveur qui ne dispose d’aucune stratégie d'audit de remplacement de base de données spécifique définie.

Avant de configurer l'audit, assurez-vous que vous utilisez bien un [« Client de bas niveau »](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configuration de l'audit pour votre base de données

1. Ouvrez le <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

2. Accédez au panneau de configuration de la base de données SQL Data Warehouse/du SQL Server que vous voulez auditer. Cliquez sur le bouton **Paramètres** situé en haut de l’écran, puis, dans le volet de configuration, sélectionnez **audit**.

	![][1]

3. Dans le panneau de configuration de l’audit, décochez la case **Hériter des paramètres d’audit du serveur**. Vous pouvez ainsi définir les paramètres pour une base de données spécifique.

	![][2]

4. Ensuite, activez la fonction d’audit en cliquant sur le bouton **ACTIVÉ**.

	![][3]

5. Dans le panneau de configuration de l’audit, sélectionnez **DÉTAILS DU STOCKAGE** pour ouvrir le panneau de stockage de journaux d’audit. Sélectionnez le compte de stockage Azure dans lequel les journaux seront enregistrés, ainsi que la période de rétention. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport préconfigurés.

	![][4]

6. Cliquez sur le bouton **OK** pour enregistrer la configuration des détails du stockage.


7. Sous **ENREGISTREMENT PAR ÉVÈNEMENT**, cliquez sur **SUCCÈS** et **ÉCHEC**pour enregistrer tous les événements, ou choisissez des catégories d’événements individuelles.


8. Si vous configurez l’audit pour une base de données, vous pouvez être amené à modifier la chaîne de connexion de votre client pour garantir que l’audit des données est correctement capturé. Consultez la rubrique [Modifier le nom de domaine complet du serveur dans la chaîne de connexion](sql-data-warehouse-auditing-downlevel-clients.md), qui traite des connexions de client de niveau inférieur.

9. Cliquez sur **OK**.


##<a id="subheading-3">Analyse des journaux et des rapports d’audit</a>

Les journaux d'audit sont agrégés dans une collection de tables de stockage avec un préfixe **SQLDBAuditLogs** au sein du compte de stockage Azure que vous avez choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l'aide d'un outil tel que l'<a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorateur de stockage Azure</a>.

Un modèle de rapport de tableau de bord préconfiguré est disponible sous forme de <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">feuille de calcul Excel téléchargeable</a> afin de vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d'audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable <a href="http://www.microsoft.com/download/details.aspx?id=39379">ici</a>.

Le modèle contient des données d'exemple fictives. Vous pouvez configurer Power Query de façon à ce qu'il importe votre journal d'audit directement à partir de votre compte de stockage Azure.

Pour des instructions plus détaillées sur la façon de travailler avec le modèle de rapport, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Procédure (téléchargement doc)</a>.

![][5]


##<a id="subheading-4">Pratiques d’utilisation dans un environnement de production</a>
La description fournie dans cette section fait référence aux captures d'écran précédentes. Vous pouvez utiliser le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou le <a href= "https://manage.windowsazure.com/" target="_bank">portail Azure Classic</a>.


##<a id="subheading-5"></a>Régénération des clés de stockage

Dans un environnement de production, vous êtes susceptible d'actualiser de temps en temps vos clés de stockage. Au moment d'actualiser vos clés, vous devez réenregistrer la stratégie. Pour ce faire, procédez comme suit :


1. Dans le volet de configuration de l'audit (décrit plus haut dans la section de configuration de l'audit), faites passer la **clé d'accès de stockage** de *Primaire* à *Secondaire*, puis choisissez **ENREGISTRER**. ![][4]
2. Revenez au volet de configuration du stockage, puis **régénérez** la *clé d'accès primaire*.

3. Revenez au volet de configuration de l'audit, faites passer la **clé d'accès de stockage** de *Secondaire* à *Primaire*, puis cliquez sur **ENREGISTRER**.

4. Retournez dans l'interface utilisateur de stockage, puis **régénérez** la *clé d'accès secondaire* (en vue du prochain cycle d'actualisation des clés).

##<a id="subheading-6"></a>Automatisation
Il existe plusieurs cmdlets PowerShell que vous pouvez utiliser pour configurer l’audit dans la base de données SQL Azure. Pour accéder aux cmdlets d’audit, vous devez exécuter PowerShell en mode Azure Resource Manager.

> [AZURE.NOTE] Le module [Azure Resource Manager](https://msdn.microsoft.com/library/dn654592.aspx) est actuellement disponible dans une version préliminaire. Il peut ne pas fournir les mêmes fonctionnalités de gestion que le module Azure.

Lorsque vous êtes en mode Azure Resource Manager, exécutez `Get-Command *AzureSql*` pour répertorier les cmdlets disponibles.


<!--Anchors-->
[Principes fondamentaux de l’audit de base de données]: #subheading-1
[Configuration de l'audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d’audit]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->

<!------HONumber=AcomDC_0309_2016-->