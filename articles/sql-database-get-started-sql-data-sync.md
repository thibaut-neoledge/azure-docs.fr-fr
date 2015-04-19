<properties 
	pageTitle="Prise en main de la synchronisation des données de bases de données SQL" 
	description="Ce didacticiel présente la synchronisation des données SQL Azure (aperçu)." 
	services="sql-database" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="3/2/2015" 
	ms.author="spelluru"/>


# Prise en main de la synchronisation des données SQL Azure (version préliminaire)
Dans ce didacticiel, vous allez vous familiariser avec les concepts de base de la synchronisation des données SQL Azure à l'aide de la version préliminaire du portail Azure. 

Ce didacticiel ne présuppose aucune expérience préalable de SQL Server et de la base de données SQL Azure. Au cours de ce didacticiel, vous allez créer un groupe de synchronisation hybride (instances SQL Server et base de données SQL) entièrement configuré et synchronisé avec la planification que vous avez définie.


## Étape 1 : connexion à la base de données SQL Azure

1. Connectez-vous au [Portail de gestion](http://manage.windowsazure.com).

2. Dans le volet gauche, cliquez sur **BASES DE DONNÉES SQL**.

3. Cliquez sur **SYNC** en bas de la page. Lorsque vous cliquez sur SYNC, la liste qui apparaît affiche les éléments que vous pouvez ajouter, à savoir un **groupe de synchronisation** et un **agent de synchronisation**.

4. Pour lancer l'Assistant New SQL Data Sync Agent, cliquez sur **Nouvel agent de synchronisation**.

5. Si vous n'avez pas encore ajouté d'agent, cliquez sur **télécharger ici**.

	![Image1](./media/sql-database-get-started-data-sync/SQLDatabaseScreen-Figure1.PNG)


## Étape 2 : ajout d'un agent client
Cette étape n'est nécessaire que si vous envisagez d'inclure une base de données SQL Server locale dans le groupe de synchronisation. Vous pouvez passer à l'étape 4 : créez un groupe de synchronisation si celui-ci n'inclut que des instances de base de données SQL.

<h3><a id="InstallRequiredSoftware"></a>Étape 2a : installation des logiciels requis</h3>
Veillez à installer les composants suivants sur l'ordinateur sur lequel vous installez l'agent client.

- **.NET Framework 4.0** 

 Vous pouvez télécharger .NET Framework 4.0 [ici](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 System CLR Types (x86)**

 Vous pouvez télécharger Microsoft SQL Server 2008 R2 SP1 System CLR Types (x86) [ici](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86)**

 Vous pouvez télécharger Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86) [ici](http://www.microsoft.com/download/en/details.aspx?id=26728)


<h3><a id="InstallClient"></a>Étape 2b : installation d'un nouvel agent client</h3>

Pour installer l'agent, suivez les instructions de la page [Installer un Agent client pour l'Aperçu de synchronisation des données SQL](http://msdn.microsoft.com/library/jj823137.aspx). 


<h3><a id="RegisterSSDb"></a>Étape 2c : finalisation de l'Assistant New SQL Data Sync Agent</h3> 

1. 	Revenez à l'Assistant New SQL Data Sync Agent.
2.	Attribuez un nom explicite à l'agent.
3.	Dans la liste déroulante, sélectionnez la **RÉGION** (centre de données) hébergeant l'agent.
4.	Dans la liste déroulante, sélectionnez l'**ABONNEMENT** (centre de données) hébergeant l'agent.
5.	Cliquez sur la flèche droite.



## Étape 3 : inscription d'une base de données SQL Server auprès d'un agent client

Une fois l'agent client installé, inscrivez chaque base de données SQL Server locale que vous souhaitez inclure dans un groupe de synchronisation auprès de l'agent.
Pour ce faire, suivez les instructions de la page [Inscrire une base de données SQL Server auprès d'un agent client](http://msdn.microsoft.com/library/jj823138.aspx).



## Étape 4 : création d'un groupe de synchronisation

<h3><a id="StartNewSGWizard"></a>Étape 4a : lancement de l'Assistant New Sync Group</h3>
1.	Revenez au [portail de gestion](http://manage.windowsazure.com).
2.	Cliquez sur **BASES DE DONNÉES SQL**.
3.	Cliquez sur **AJOUTER SYNCHRONISATION** en bas de la page, puis sélectionnez Nouveau groupe de synchronisation dans le menu déroulant.

	![Image2](./media/sql-database-get-started-data-sync/NewSyncGroup-Figure2.png)


<h3><a id=""></a>Étape 4b : saisie des paramètres de base</h3>	
1.	Attribuez un nom explicite au groupe de synchronisation.
2.	Dans la liste déroulante, sélectionnez la **RÉGION** (centre de données) hébergeant le groupe de synchronisation.
3. Cliquez sur la flèche droite.

	![Image3](./media/sql-database-get-started-data-sync/NewSyncGroupName-Figure3.PNG)
 
<h3><a id="DefineHubDB"></a>Étape 4c : définition du hub de synchronisation</h3>
1. Dans la liste déroulante, sélectionnez l'instance de base de données SQL qui servira de hub de groupe de synchronisation.
2. Entrez les informations d'identification de l'instance de base de données SQL (**NOM D'UTILISATEUR DU CONCENTRATEUR** et **MOT DE PASSE DU CONCENTRATEUR**).
3. Patientez jusqu'à ce que la synchronisation des données confirme le nom d'utilisateur et le mot de passe. Une coche verte apparaît à droite du mot de passe lorsque les informations d'identification sont confirmées.
4. Dans la liste déroulante, sélectionnez la stratégie **RÉSOLUTION DES CONFLITS**.

 **Hub Wins** : les modifications écrites dans la base de données hub sont écrites dans les bases de données de référence et écrasent les modifications apportées dans le même enregistrement de base de données de référence. En pratique, cela signifie que la première modification écrite dans le hub est propagée vers les autres bases de données.


 **Client Wins** : les modifications apportées au concentrateur sont écrasées par celles apportées aux bases de données de référence. En pratique, cela signifie que la dernière modification écrite dans le hub est conservée et propagée vers les autres bases de données.

5.	Cliquez sur la flèche droite.

	![Image4](./media/sql-database-get-started-data-sync/NewSyncGroupHub-Figure4.PNG)

<h3><a id="AddRefDB"></a>Étape 4d : ajout d'une base de données de référence</h3>

Répétez cette procédure pour chaque base de données supplémentaire que vous souhaitez ajouter au groupe de synchronisation.

1. Dans la liste déroulante, sélectionnez la base de données à ajouter.

	Les bases de données figurant dans la liste déroulante incluent aussi bien les bases de données SQL Server inscrites auprès de l'agent que les instances de base de données SQL.
2.	Entrez les informations d'identification de cette base de données, à savoir le **NOM D'UTILISATEUR** et le **MOT DE PASSE**.
3.	Dans la liste déroulante, sélectionnez le **SENS DE LA SYNCHRONISATION** de cette base de données.

	**Bi-directionnel** : les modifications apportées à la base de données de référence sont écrites dans la base de données hub et inversement.

	**Synchronisation depuis le concentrateur** : la base de données reçoit les mises à jour du hub. Elle ne lui envoie pas de modifications.

	**Synchronisation vers le concentrateur** : la base de données envoie les mises à jour au hub. Les modifications apportées au hub ne sont pas écrites dans cette base de données.

4.	Pour finaliser la création de ce groupe de synchronisation, cliquez sur la coche située en bas à droite de l'Assistant. Patientez jusqu'à ce que la synchronisation des données SQL confirme les informations d'identification, ce qui sera indiqué par une coche verte.

5.	Cliquez une nouvelle fois sur la coche. Vous revenez alors à la page **SYNC** sous Bases de données SQL. Ce groupe de synchronisation est désormais répertorié avec les autres groupes de synchronisation et agents.

	![Image5](./media/sql-database-get-started-data-sync/NewSyncGroupReference-Figure5.PNG)


## Étape 5 : définition des données à synchroniser

La synchronisation des données SQL Azure vous permet de sélectionner les tables et colonnes à synchroniser. Si vous souhaitez filtrer une colonne afin que seules les lignes contenant des valeurs spécifiques soient synchronisées (par exemple, un âge supérieur ou égal à 65), utilisez le portail de la synchronisation des données SQL Azure et reportez-vous à la section de la documentation traitant de la sélection des tables, colonnes et lignes à synchroniser pour définir les données à synchroniser.

1.	Revenez au [portail de gestion](http://manage.windowsazure.com).
2.	Cliquez sur **BASES DE DONNÉES SQL**.
3.	Cliquez sur l'onglet **SYNC**.
4.	Cliquez sur le nom du groupe de synchronisation.
5.	Cliquez sur l'onglet **RÈGLES DE SYNCHRONISATION**.
6.	Sélectionnez la base de données à laquelle vous voulez fournir le schéma de groupe de synchronisation.
7.	Cliquez sur la flèche droite.
8.	Cliquez sur **ACTUALISER LE SCHÉMA**.
9.	Pour chaque table de la base de données, sélectionnez les colonnes à inclure dans les synchronisations. 
	- Les colonnes qui contiennent des types de données non pris en charge ne peuvent pas être sélectionnées. 
	- Si aucune colonne de la table n'est sélectionnée, la table n'est pas incluse dans le groupe de synchronisation. 
	- Pour sélectionner/désélectionner toutes les tables, cliquez sur Sélectionner en bas de l'écran.
10.	Cliquez sur **ENREGISTRER**, puis patientez pendant que la configuration du groupe de synchronisation se termine.
11.	Pour revenir à la page d'accueil de la synchronisation des données, cliquez sur la flèche Précédent située en haut à gauche de 'écran (au-dessus du nom du groupe de synchronisation).

	![Image6](./media/sql-database-get-started-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## Étape 7 : configuration de votre groupe de synchronisation

Vous pouvez toujours synchroniser un groupe de synchronisation en cliquant sur le bouton Synchroniser situé en bas de la page d'accueil de la synchronisation des données.
Si vous souhaitez que le groupe de synchronisation se synchronise sur une planification, configurez-le dans ce sens.

1.	Revenez au [portail de gestion](http://manage.windowsazure.com).
2.	Cliquez sur **BASES DE DONNÉES SQL**.
3.	Cliquez sur l'onglet **SYNC**.
4.	Cliquez sur le nom du groupe de synchronisation.
5.	Cliquez sur l'onglet **CONFIGURER**.
6.	**SYNCHRONISATION AUTOMATIQUE**
	- Pour configurer le groupe de synchronisation pour qu'il soit synchronisé en fonction de la fréquence définie, cliquez sur **ON**. Vous pouvez effectuer une synchronisation à la demande en cliquant sur le bouton Synchroniser.
	- Cliquez sur **OFF** pour configurer le groupe de synchronisation de manière à ce qu'il soit synchronisé uniquement lorsque vous cliquez sur SYNCHRONISER.
7.	**FRÉQUENCE DE SYNCHRONISATION**
	- Si la synchronisation automatique est activée, définissez sa fréquence. Celle-ci doit être comprise entre 5 minutes et 1 mois.
8.	Cliquez sur **ENREGISTRER**.

![Image7](./media/sql-database-get-started-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Félicitations ! Vous avez créé un groupe de synchronisation incluant une instance de base de données SQL et une base de données SQL Server.

## Étapes suivantes
Pour plus d'informations sur la base de données SQL et la synchronisation des données SQL, consultez les pages suivantes :

* [Inscrivez-vous à l'offre Premium pour la base de données SQL] (sign-up-for-sql-database-premium.md)
* [Synchronisation des données SQL (portail WA)](http://msdn.microsoft.com/library/windowsazure/jj856263.aspx)
* [Prise en main de la base de données SQL Azure](getting-started-w-sql-databases.md)
* [Cycle de vie des bases de données SQL Server](http://go.microsoft.com/fwlink/?LinkId=275193)










<!--HONumber=47-->
