<properties
	pageTitle="Prise en main de l’audit de base de données SQL | Microsoft Azure"
	description="Prise en main de l’audit de base de données SQL"
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
	ms.date="11/12/2015"
	ms.author="jeffreyg; ronitr"/>
 
# Prise en main de l’audit de base de données SQL
L’audit de base de données SQL Azure effectue le suivi des événements de base de données et consigne les événements audités dans un journal dans votre compte Azure Storage. L’audit est en disponibilité générale pour les services De base, Standard et Premium.

L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

Les outils d’audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).

+ [Principes fondamentaux de l’audit de base de données SQL Azure]
+ [Configuration de l’audit pour votre base de données]
+ [Analyse des journaux et des rapports d’audit]

##<a id="subheading-1"></a>Principes fondamentaux de l’audit de base de données SQL Azure

La section suivante décrit la configuration de l’audit à l’aide du portail Azure. Vous pouvez également [configurer l’audit pour votre base de données à partir du portail Azure Classic].

L’audit de bases de données SQL permet :

- **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
- **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
- **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

> [AZURE.NOTE] Vous pouvez désormais recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles à l’aide de la nouvelle fonctionnalité de **détection de menaces**, désormais en version préliminaire. La détection des menaces peut activée et configurée à partir du panneau de configuration d’audit. Pour en savoir plus, consultez [Prise en main de la détection des menaces](sql-database-threat-detection-get-started.md).

Vous pouvez configurer l’audit pour les catégories d’événements suivantes :

**SQL ordinaire** et **SQL paramétré** pour lesquels les journaux d'audit collectés sont classés comme

- **accès aux données ;**
- **modifications de schéma (DDL) ;**
- **modifications de données (DML) ;**
- **comptes, rôles et autorisations (DCL) ;**
- **Procédure stockée**, **connexion** et **Gestion des transactions**.

Pour chaque catégorie d’événement, les audits des opérations **Succès** et **Échec** sont configurés séparément.

Pour plus d’informations sur les activités et les événements audités, consultez la [référence sur les formats de journaux d’audit (fichier en anglais à télécharger)](http://go.microsoft.com/fwlink/?LinkId=506733).

Les journaux d’audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention du journal d’audit, après laquelle les anciens journaux seront supprimés.

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut. Une stratégie d’audit de serveur par défaut s’appliquera à toutes les bases de données d’un serveur qui ne dispose d’aucune stratégie d’audit de remplacement de base de données spécifique définie.

Avant de configurer l’audit, vérifiez que vous utilisez bien un [« Client de niveau inférieur »](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


##<a id="subheading-2"></a>Configuration de l’audit pour votre base de données

1. Accédez au [portail Azure](https://portal.azure.com) à l’adresse https://portal.azure.com. Vous pouvez également accéder au [portail Azure Classic](https://manage.windowsazure.com/) à l’adresse https://manage.windowsazure.com/. Voir les détails ci-dessous.

2. Accédez au panneau des paramètres de Base de données SQL/SQL Server que vous voulez auditer. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.

	![Volet de navigation][1]

3. Dans le panneau de configuration de l’audit, activez **ON**.

4. Sélectionnez **Détails du stockage** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte Azure Storage dans lequel enregistrer les journaux, ainsi que la période de rétention. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport d’audit.

	![Volet de navigation][2]

5. Cliquez sur **Événements audités (Audited Events)** pour personnaliser les événements à auditer. Dans le panneau **Journalisation par événement**, cliquez sur **Succès** et **Échec** pour journaliser tous les événements, ou choisissez des catégories d’événements.


6. Vous pouvez activer la case à cocher **Hériter des paramètres d’audit du serveur** pour indiquer que cette base de données sera analysée en fonction des paramètres de son serveur. Une fois que vous activez cette option, vous voyez un lien qui permet d’afficher ou de modifier les paramètres d’audit du serveur à partir de ce contexte.

	![Volet de navigation][3]

7. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer (**ON**) la détection de menaces et configurer les adresses électroniques qui recevront des alertes de sécurité. Pour en savoir plus, consultez la page sur la [prise en main de la détection des menaces](sql-database-threat-detection-get-started.md).

8. Cliquez sur **Enregistrer**.



##<a id="subheading-3"></a>Analyse des journaux et des rapports d’audit

Les journaux d’audit sont agrégés dans une collection de tables de stockage avec un préfixe **SQLDBAuditLogs** au sein du compte de stockage Azure que vous avez choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l’aide d’un outil tel que [l’Explorateur de stockage Azure](http://azurestorageexplorer.codeplex.com/).

Un modèle de rapport préconfiguré est disponible sous forme de [feuille de calcul Excel téléchargeable](http://go.microsoft.com/fwlink/?LinkId=403540) pour vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d’audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable [ici](http://www.microsoft.com/download/details.aspx?id=39379).

Vous pouvez importer vos journaux d’audit dans le modèle Excel directement à partir de votre compte de stockage Azure à l’aide de Power Query. Vous pouvez explorer ensuite vos enregistrements d’audit et créer des tableaux de bord et des rapports outre les données du journal.


![Volet de navigation][4]


##<a id="subheading-4"></a>Configurer l’audit pour votre base de données à partir du portail Azure Classic

1. Accédez au [portail Azure Classic](https://manage.windowsazure.com/) à l’adresse https://manage.windowsazure.com/.

2. Cliquez sur la base de données SQL/le SQL Server que vous voulez auditer, puis sur l’onglet **AUDIT ET SÉCURITÉ**

3. Définir l’audit sur **ACTIVÉ**.

	![Volet de navigation][5]

4. Modifiez la **JOURNALISATION PAR ÉVÉNEMENT** si nécessaire pour personnaliser les événements à auditer.

	![Volet de navigation][6]

5. Sélectionnez un **COMPTE DE STOCKAGE** et configurer **RÉTENTION**.

	![Volet de navigation][7]

6. Cliquez sur **ENREGISTRER**.




##<a id="subheading-5">Pratiques d’utilisation dans un environnement de production</a>
La description fournie dans cette section fait référence aux captures d’écran précédentes. Vous pouvez utiliser le [portail Azure](https://portal.azure.com) ou le [portail Azure Classic](https://manage.windowsazure.com/).


##<a id="subheading-6"></a>Régénération des clés de stockage

Dans un environnement de production, vous êtes susceptible d’actualiser de temps en temps vos clés de stockage. Lors de la mise à jour de vos clés, vous devez réenregistrer la stratégie d’audit. Pour ce faire, procédez comme suit :


1. Dans le panneau de configuration de l’audit, basculez la **clé d’accès de stockage** de *Primaire* à *Secondaire*, puis cliquez sur **ENREGISTRER**.

	![][8]

2. Revenez au volet de configuration du stockage, puis **régénérez** la *clé d’accès primaire*.

3. Revenez au panneau de configuration de l’audit, basculez la **clé d’accès de stockage** de *Secondaire* à *Primaire*, puis cliquez sur **ENREGISTRER**.

4. Revenez dans l’interface utilisateur de stockage, puis **régénérez** la *Clé d’accès secondaire* (en vue du prochain cycle d’actualisation des clés).
  
##<a id="subheading-7"></a>Automatisation
Vous pouvez utiliser différentes applets de commande PowerShell pour configurer l’audit dans Base de données SQL Azure :

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Principes fondamentaux de l’audit de base de données SQL Azure]: #subheading-1
[Configuration de l’audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d’audit]: #subheading-3
[configurer l’audit pour votre base de données à partir du portail Azure Classic]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 

<!---HONumber=AcomDC_0128_2016-->