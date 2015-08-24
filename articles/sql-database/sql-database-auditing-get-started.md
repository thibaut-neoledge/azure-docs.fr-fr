<properties 
	pageTitle="Prise en main de l'audit de base de données SQL | Microsoft Azure" 
	description="Prise en main de l'audit de base de données SQL" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="jeffreyg"/>
 
# Prise en main de l'audit de base de données SQL 
<p> L'audit de base de données SQL Azure effectue le suivi des événements de base de données et consigne les événements audités dans un journal dans votre compte Azure Storage. L'audit est en disponibilité générale pour les services De base, Standard et Premium.

L'audit peut vous aider à respecter une conformité réglementaire, à comprendre l'activité de la base de données et à découvrir des discordances et anomalies susceptibles d'indiquer des problèmes pour l'entreprise ou des violations de la sécurité.

Les outils d'audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d'informations sur les programmes Azure prenant en charge la conformité aux normes, consultez le <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centre de gestion de la confidentialité Azure</a>.

+ [Principes fondamentaux de l'audit de base de données SQL Azure] 
+ [Configuration de l'audit pour votre base de données]
+ [Analyse des journaux et des rapports d’audit]

##<a id="subheading-1"></a>Principes fondamentaux de l'audit de base de données SQL Azure

Les sections suivantes décrivent la configuration de l'audit à l'aide de la version préliminaire du portail Azure. Vous pouvez également [configurer l'audit pour votre base de données à partir du portail Azure classique].

Éléments rendus possibles par l'audit de bases de données :

- **La rétention** d'une piste d'audit d'événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
- **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
- **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Vous pouvez configurer l'audit pour les catégories d'événements suivantes :

**SQL ordinaire** et **SQL paramétré** pour lesquels les journaux d'audit collectés sont classés comme

- **accès aux données** ;
- **modifications de schéma (DDL)** ;
- **modifications de données (DML)** ;
- **comptes, rôles et autorisations (DCL)** ;

**Procédure stockée**, **connexion** et **Gestion des transactions**.

Pour chaque catégorie d'événements, les audits des opérations **Succès** et **Échec** sont configurées séparément.

Pour d'autres informations sur les activités et événements audités, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Référence sur le format du journal d'audit (téléchargement d'un fichier doc)</a>.

Les journaux d'audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention des journaux d'audit.

Une stratégie d'audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut. Une stratégie d'audit de serveur par défaut s'appliquera à toutes les bases de données d’un serveur qui ne dispose d’aucune stratégie d'audit de remplacement de base de données spécifique définie.

Avant de configurer l'audit, assurez-vous que vous utilisez bien un [« Client de bas niveau »](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


##<a id="subheading-2"></a>Configuration de l'audit pour votre base de données

1. Lancez la <a href="https://portal.azure.com" target="_blank">version préliminaire du portail Azure</a> à l’adresse https://portal.azure.com. Vous pouvez également lancer la <a href= "https://manage.windowsazure.com/" target="_bank">version classique du portail Azure</a> à l'adresse https://manage.windowsazure.com/. Consultez les détails ci-dessous.

2. Accédez au volet de configuration de la base de données SQL/du SQL Server que vous voulez auditer. Cliquez sur le bouton **Paramètres** situé en haut de l’écran, puis, dans le volet de configuration, sélectionnez **audit**.

	![][1]

3. Dans le volet de configuration de l’audit, sélectionnez DÉTAILS DE STOCKAGE pour ouvrir le volet de stockage de journaux d'audit. Sélectionnez le compte de stockage Azure dans lequel les journaux seront enregistrés, ainsi que la période de rétention. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport préconfigurés.

	![][2]

4. Sous **ENREGISTREMENT PAR ÉVÈNEMENT**, cliquez sur **SUCCÈS** et **ÉCHEC**pour enregistrer tous les événements, ou choisissez des catégories d’événements individuelles.


5. Si vous configurez l’audit pour une base de données SQL, cliquez sur **Pour appliquer l’audit, cliquez ici...**, puis sur **ACCÈS DE SÉCURITÉ ACTIVÉ**, sélectionnez **OBLIGATOIRE**. Si vous configurez l'audit pour un serveur SQL, vous avez deux options : (a) après l'étape \#6, accédez pour chaque base de données SQL au serveur et appliquez cette étape, ou (2) [modifiez Server FDQN dans la chaîne de connexion](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


	![][5]

6. Cliquez sur **OK**.



##<a id="subheading-3">Analyse des journaux et des rapports d’audit</a>

Les journaux d'audit sont agrégés dans une collection de tables de stockage avec un préfixe **SQLDBAuditLogs** au sein du compte de stockage Azure que vous avez choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l'aide d'un outil tel que l'<a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorateur de stockage Azure</a>.

Un modèle de rapport de tableau de bord préconfiguré est disponible sous forme de <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">feuille de calcul Excel téléchargeable</a> afin de vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d'audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable <a href="http://www.microsoft.com/download/details.aspx?id=39379">ici</a>.

Le modèle contient des données d'exemple fictives. Vous pouvez configurer Power Query de façon à ce qu'il importe votre journal d'audit directement à partir de votre compte de stockage Azure.

Pour des instructions plus détaillées sur la façon de travailler avec le modèle de rapport, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Procédure (téléchargement doc)</a>.

![][6]


##<a id="subheading-4"></a>Configuration de l'audit pour votre base de données à partir du portail Azure classique

1. Lancez le <a href= "https://manage.windowsazure.com/" target="_bank">portail Azure classique</a> à l’adresse https://manage.windowsazure.com/.
 
2.   Cliquez sur la base de données SQL/le SQL Server que vous voulez auditer, puis sur l'onglet **AUDIT ET SÉCURITÉ**

3.   Si vous configurez l’audit pour une base de données SQL, cliquez sur **ACCÈS DE SÉCURITÉ ACTIVÉ**, sélectionnez **OBLIGATOIRE**. Si vous configurez l'audit pour un SQL Server, vous avez deux options : (a) après l'étape \#7, accédez pour chaque base de données SQL au serveur et appliquez cette étape, ou (2) [modifiez Server FDQN dans la chaîne de connexion](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

4. Dans la section Audit, cliquez sur **ACTIVÉ**.


	![][7]

5. Modifiez l’**ENREGISTREMENT PAR ÉVÈNEMENT** en fonction de vos besoins.

	![][8]

6. Sélectionnez un **COMPTE DE STOCKAGE** et configurer **RÉTENTION**.

	![][11]

7. Cliquez sur **ENREGISTRER**.




##<a id="subheading-3">Pratiques d’utilisation dans un environnement de production</a>
La description fournie dans cette section fait référence aux captures d'écran précédentes. La <a href="https://portal.azure.com" target="_blank">version préliminaire du portail Azure</a> ou la <a href= "https://manage.windowsazure.com/" target="_bank">version classique du portail Azure</a> peuvent être utilisées.
 

##<a id="subheading-4"></a>Régénération des clés de stockage

Dans un environnement de production, vous êtes susceptible d'actualiser de temps en temps vos clés de stockage. Au moment d'actualiser vos clés, vous devez réenregistrer la stratégie. Pour ce faire, procédez comme suit :


1. Dans le volet de configuration de l'audit (décrit plus haut dans la section de configuration de l'audit), faites passer la **clé d'accès de stockage** de *Primaire* à *Secondaire*, puis choisissez **ENREGISTRER**. ![][10]
2. Revenez au volet de configuration du stockage, puis **régénérez** la *clé d'accès primaire*.

3. Revenez au volet de configuration de l'audit, faites passer la **clé d'accès de stockage** de *Secondaire* à *Primaire*, puis cliquez sur **ENREGISTRER**.

4. Retournez dans l'interface utilisateur de stockage, puis **régénérez** la *clé d'accès secondaire* (en vue du prochain cycle d'actualisation des clés).
  
##<a id="subheading-4"></a>Automatisation
Il existe plusieurs cmdlets PowerShell que vous pouvez utiliser pour configurer l’audit dans la base de données SQL Azure. Pour accéder aux cmdlets d’audit, vous devez exécuter PowerShell en mode Azure Resource Manager.

> [AZURE.NOTE]Le module [Azure Resource Manager](https://msdn.microsoft.com/library/dn654592.aspx) est actuellement disponible dans une version préliminaire. Il peut ne pas fournir les mêmes fonctionnalités de gestion que le module Azure.

Lorsque vous êtes en mode Azure Resource Manager, exécutez `Get-Command *AzureSql*` pour répertorier les cmdlets disponibles.







<!--Anchors-->
[Principes fondamentaux de l'audit de base de données SQL Azure]: #subheading-1
[Configuration de l'audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d’audit]: #subheading-3
[configurer l'audit pour votre base de données à partir du portail Azure classique]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png
[11]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure-storage.png






<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

 

<!---HONumber=August15_HO7-->