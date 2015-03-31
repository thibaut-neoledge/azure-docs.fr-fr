<properties 
	pageTitle="Prise en main de l'audit de base de données SQL | Azure" 
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
	ms.date="02/23/2015" 
	ms.author="jeffreyg"/>
 
# Prise en main de l'audit de base de données SQL 
<p> L'audit de base de données SQL Azure effectue le suivi des événements de base de données et consigne les événements audités dans un journal dans votre compte Azure Storage. L'audit est en disponibilité générale pour les services De base, Standard et Premium.

L'audit peut vous aider à respecter une conformité réglementaire, à comprendre l'activité de la base de données et à découvrir des discordances et anomalies susceptibles d'indiquer des problèmes pour l'entreprise ou des violations de la sécurité. 

Les outils d'audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d'informations sur les programmes Azure prenant en charge la conformité aux normes, consultez le <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centre de gestion de la confidentialité Azure</a>.

+ [Principes fondamentaux de l'audit de base de données SQL Azure] 
+ [Configuration de l'audit pour votre base de données]
+ [Analyse des journaux et des rapports d'audit]

## <a id="subheading-1"></a>Principes fondamentaux de l'audit de base de données SQL Azure

Vous configurez l'audit dans la version préliminaire du portail Azure, mais peu importe que vous ayez créé la base de données à partir du portail Azure ou de sa version préliminaire. Éléments rendus possibles par l'audit de bases de données :

- La **rétention** d'une piste d'audit d'événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à enregistrer.
- **La génération de rapports** sur les activités de la base de données. Utilisez des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
- L'**analyse** des rapports. Repérez les événements suspects, les activités inhabituelles et les tendances.

Avec l'audit, vous pouvez suivre les activités et événements suivants :

- **accès aux données ;**
- **modifications de schéma (DDL)** ;
- **modifications de données (DML)** ;
- **comptes, rôles et autorisations (DCL)** ;
- **exceptions de sécurité.**

Pour plus d'informations sur les activités et les événements journalisés, consultez le document de <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">référence sur les formats des journaux d'audit (fichier à télécharger)</a>. 

Vous devez également choisir le compte de stockage dans lequel enregistrer les journaux d'audit.

### Chaîne de connexion sécurisée
Lors de la configuration de l'audit, Azure vous fournit une chaîne de connexion sécurisée pour la base de données. Seules les applications clientes utilisant cette chaîne de connexion verront leurs événements et activités enregistrés. Vous devez donc mettre à jour les applications clientes existantes de façon à utiliser le nouveau format de chaîne.

Format de chaîne de connexion classique : <*nom du serveur*>.database.windows.net

Chaîne de connexion sécurisée : <*nom du serveur*>.database.**secure**.windows.net


## <a id="subheading-2"></a>Configuration de l'audit pour votre base de données

1. Lancez la <a href="https://portal.azure.com" target="_blank">version préliminaire du portail Azure</a> à l'adresse https://portal.azure.com. Vous pouvez aussi lancer <a href= "https://manage.windowsazure.com/" target="_bank">le portail Azure classique</a> à l'adresse https://manage.windowsazure.com/. Consultez les détails ci-dessous.
2. Accédez au volet de configuration de la base de données que voulez auditer. Faites défiler l'écran vers le bas jusqu'à la section **Opérations**, puis cliquez sur **Audit** pour activer l'audit et lancer le volet de configuration de l'audit.

	![][1]

3. Dans le volet de configuration de l'audit, sélectionnez le compte de stockage Azure dans lequel les journaux seront enregistrés. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport préconfigurés.

	![][2]

4. Sous **Options d'audit**, cliquez sur **Tous** pour journaliser tous les événements ou choisissez des types d'événement spécifiques.

	![][3]

5. Activez **Enregistrer ces paramètres par défaut pour le serveur** si vous voulez appliquer ces paramètres à toutes les futures bases de données du serveur et à toutes celles dont l'audit n'a pas encore été configuré. Il est possible de remplacer ces paramètres ultérieurement pour chaque base de données en suivant cette même procédure. 

6. Cliquez sur **Afficher les chaînes de connexion de la base de données**, puis copiez ou notez la chaîne de connexion sécurisée de votre application. Utilisez-la pour toute application cliente sur laquelle effectuer un audit.

	![][5]

7. Cliquez sur **OK**.



## <a id="subheading-3"></a>Analyse des journaux et des rapports d'audit

Les journaux d'audit sont agrégés dans une table de stockage Azure unique nommée **AuditLogs** au sein du compte de stockage Azure que vous avez choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l'aide d'un outil comme <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage Explorer</a>.

Un modèle de rapport de tableau de bord préconfiguré est disponible sous forme de <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">feuille de calcul Excel téléchargeable</a> pour vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d'audit, vous avez besoin d'Excel version 2013 ou ultérieure et de Power Query, que vous pouvez télécharger <a href="http://www.microsoft.com/download/details.aspx?id=39379">ici,</a>. 

Le modèle contient des données d'exemple fictives. Vous pouvez configurer Power Query de façon à ce qu'il importe votre journal d'audit directement à partir de votre compte de stockage Azure. 

Pour obtenir des instructions plus détaillées sur l'utilisation du modèle de rapport, consultez la <a href="http://go.microsoft.com/fwlink/?LinkId=506731">procédure (document à télécharger)</a>.

![][6]


## <a id="subheading-4"></a>Configuration de l'audit pour votre base de données à partir du portail Azure classique

1. Lancez le <a href= "https://manage.windowsazure.com/" target="_bank">portail Azure classique</a> à l'adresse https://manage.windowsazure.com/. 
2. Cliquez sur la base de données que vous voulez auditer, puis sur l'onglet **Audit et sécurité**.
3. Dans la section Audit, cliquez sur " Activé ".

![][7]

4. Modifiez le **TYPE D'ÉVÉNEMENT**, si nécessaire.

![][8]

5. Sélectionnez un **COMPTE DE STOCKAGE**.
6. Cliquez sur **ENREGISTRER**.
7. Cliquez sur **Afficher la chaîne de connexion sécurisée** pour la chaîne de connexion.


## <a id="subheading-3"></a>Pratiques d'utilisation dans un environnement de production
La description fournie dans cette section fait référence aux captures d'écran précédentes. Vous pouvez soit utiliser <a href="https://portal.azure.com" target="_blank">la version préliminaire du portail Azure,</a> soit <a href= "https://manage.windowsazure.com/" target="_bank">le portail Azure classique</a> .
 

## <a id="subheading-4"></a>Accès sécurisé

Dans un environnement de production, vous êtes susceptible d'imposer l'audit sur l'ensemble du trafic entre tous les outils et applications et la base de données. Pour cela, faites passer l'**Accès sécurisé** de *Optional* à *Required*, puis enregistrez la stratégie. Dès lors que *Required* est défini, il n'est plus possible d'accéder à la base de données par le biais de la chaîne de connexion d'origine. Seule la chaîne de connexion sécurisée le permet.


![][9]


## <a id="subheading-4"></a>Régénération des clés de stockage

Dans un environnement de production, vous êtes susceptible d'actualiser de temps en temps vos clés de stockage. Le service d'audit ne conserve pas les clés de compte de stockage. Au moment de l'enregistrement, une clé de signature d'accès partagé (SAP) en lecture seule est produite pour la table d'audit (le client est le seul à pouvoir lire les journaux d'audit). C'est pourquoi, au moment d'actualiser vos clés, vous devez réenregistrer la stratégie. Pour ce faire, procédez comme suit :


1. Dans le volet de configuration de l'audit (décrit plus haut dans la section de configuration de l'audit), faites passer la **clé d'accès de stockage** de *Primary* à *Secondary*, puis choisissez **ENREGISTRER**.
![][10]
2. Revenez au volet de configuration du stockage, puis **régénérez** la *Primary Access Key*.

3. Revenez au volet de configuration de l'audit, faites passer la **clé d'accès de stockage** de *Secondary* à *Primary*, puis cliquez sur **ENREGISTRER**.

4. Retournez dans l'interface utilisateur de stockage, puis **régénérez** la *Secondary Access Key* (en vue du prochain cycle d'actualisation des clés).
  
## <a id="subheading-4"></a>Automation
Il existe plusieurs cmdlets PowerShell que vous pouvez utiliser pour configurer l'audit dans la base de données SQL Azure. Pour accéder aux cmdlets d'audit, vous devez exécuter PowerShell en mode Azure Resource Manager.

> [AZURE.NOTE] Le module AzureResourceManager est actuellement en version préliminaire. Il peut ne pas fournir les mêmes fonctionnalités de gestion que le module Azure.

 Le mode [Azure Resource Manager](https://msdn.microsoft.com/library/dn654592.aspx) est accessible en exécutant la cmdlet Switch-AzureMode (`Switch-AzureMode AzureResourceManager`). Lorsque vous êtes en mode Azure Resource Manager, exécutez `Get-Command *AzureSql*` pour répertorier les cmdlets disponibles.







<!--Anchors-->
[Principes fondamentaux de l'audit de base de données SQL Azure]: #subheading-1
[Configuration de l'audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d'audit]: #subheading-3
[Configuration de l'audit pour votre base de données à partir du portail Azure classique]: #subheading-4


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






<!--Link references-->
[Lien 1 vers une autre rubrique de documentation azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Lien 2 vers une autre rubrique de documentation azure.microsoft.com]: ../web-sites-custom-domain-name/
[Lien 3 vers une autre rubrique de documentation azure.microsoft.com]: ../storage-whatis-account/


<!--HONumber=47-->
