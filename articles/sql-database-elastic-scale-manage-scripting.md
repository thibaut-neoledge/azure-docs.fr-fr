<properties 
	pageTitle="Gestion de l'infrastructure élastique avec des scripts" 
	description="Écrivez les scripts des tâches de mise à l'échelle flexible avec PowerShell et les Runbook du service d'automatisation Azure." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Gestion de l'infrastructure élastique avec des scripts


## Service Azure Automation 

[Azure Automation](http://azure.microsoft.com/documentation/services/automation/) offre un service d'exécution de workflow PowerShell puissant et essentiel à la plateforme Azure. Désormais, vous pouvez automatiser les tâches de maintenance difficiles depuis le portail Azure commun.  Créez simplement un workflow PowerShell (appelé **Runbook** dans Azure Automation), téléchargez-le dans le cloud et planifiez son exécution. Ce document décrit l'installation de bout en bout d'Azure Automation pour des exemples d'élasticité des partitions. Pour plus d'informations, consultez la page [d'annonce de la version préliminaire](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx). Ou souscrivez à un [abonnement](https://account.windowsazure.com/PreviewFeatures?fid=automation) Azure.

Dans cet exemple, Azure Automation est utilisé comme infrastructure de planification et d'exécution de la charge de travail. Considérez Azure Automation comme votre [agent SQL dans le cloud](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/). 

En plus de ce document, vous trouvez ci-dessous d'autres ressources :

* [Prise en main d'Azure Automation](http://azure.microsoft.com/documentation/articles/automation-create-runbook-from-samples/)
* [Étape par étape : prise en main de la NOUVELLE fonctionnalité en version préliminaire de Microsoft Azure Automation](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Microsoft Azure Automation](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* Posez des questions spécifiques à Azure Automation sur le [forum Automation](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).  


## Conditions préalables

[Inscrivez-vous](http://azure.microsoft.com/services/preview/) et [familiarisez-vous](http://azure.microsoft.com/documentation/articles/automation-create-runbook-from-samples/) avec le service en version préliminaire Microsoft Azure Automation. 


## Fichiers PowerShell d'élasticité des partitions

L'ensemble de fichiers PowerShell suivant comporte les commandes de base permettant d'accomplir les scénarios de mises à l'échelle verticale et horizontale à l'aide d'Azure Automation. 

Ces exemples illustrent comment utiliser les exemples de modules PowerShell pour effectuer des tâches de base d'élasticité des partitions. En association avec le service Microsoft Azure Automation et les Runbook correspondants d'Azure Automation, vous pouvez créer des tâches automatisées et planifiées qui approvisionnent une nouvelle partition et/ou modifient le niveau de performances de partitions spécifiques en fonction d'un ensemble de règles. 

**SetupShardedEnvironment.ps1** : ce Runbook PowerShell effectue une installation unique d'un environnement partitionné complet comportant un gestionnaire des cartes de partitions et une carte de partitions de plages. 

**ProvisionByDate.ps1** : approvisionne une nouvelle base de données avant la charge de travail du jour à venir. La base de données est créée et nommée selon un horodatage (AAAAMMJJ) et est enregistrée avec le gestionnaire des cartes de partitions sous la forme d'une plage [AAAAMMJJ, AAAAMMJJ + 1J). 

**ProvisionBySize.ps1** : approvisionne une nouvelle base de données lorsque la base de données actuelle manque de capacité. 

**ReduceServiceTier.ps1** : effectue une itération dans les partitions d'une carte de partitions fournie et détermine si chaque partition est un candidat à la réduction du niveau de performances. Deux critères déterminent si la partition est un candidat : 1) le niveau de service en cours de la partition et 2) l'âge de la base de données.  

**ShardManagement.psm1** : fournit un ensemble de méthodes permettant d'interagir avec le gestionnaire des cartes de partitions. 

**SqlDatabaseHelpers.psm1** : fournit un ensemble de méthodes permettant d'interagir avec les bases de données SQL Azure. 

**ShardElasticity.psm1** : fournit un ensemble de méthodes permettant d'effectuer des opérations de mise à l'échelle horizontale ou verticale. 

**ShardElasticityModule.psd1** : fournit un ensemble de méthodes permettant d'interagir avec l'infrastructure élastique et la base de données SQL Azure. 

## Coûts

Notez que l'exécution des scripts d'exemple PowerShell entraîne la création de bases de données qui occasionnent des frais réels pour le propriétaire de l'abonnement. Les bases de données SQL Azure sous-jacentes seront [facturées à un tarif](http://azure.microsoft.com/pricing/details/sql-database/) similaire à celui des autres bases de données SQL Azure.  Les taux à partir du 1er novembre sont les suivants : 

* Le Runbook SetupShardedEnvironment crée le gestionnaire des cartes de partitions sur une base de données De base (0,0069$/heure) et approvisionne également la première partition d'une base de données De base (0,0069$/heure). 

* Les Runbook ProvisionBySize et ProvisionByDate approvisionnent une base de données Standard S0 (0,0208$/heure).  Pour contrebalancer ces coûts, si vous exécutez conjointement le Runbook ReduceServiceTier, le niveau de service de la base de données nouvellement approvisionnée sera réduit d'un niveau Standard S0 (0,0208$/heure) à un service De base (0,0069$/heure) après une journée. 

Enfin, dans le cadre des exemples fournis, l'utilisation d'[Azure Automation](http://azure.microsoft.com/pricing/details/automation/) ne génère actuellement pas de frais pour le propriétaire de l'abonnement.  Pour plus d'informations, consultez la [page de tarification Azure Automation](http://azure.microsoft.com/pricing/details/automation/). 

## Pour charger les Runbook 

1. Téléchargez le fichier **ShardElasticity.zip** et extrayez son contenu.
2. [Ajout de références aux fichiers binaires de l'infrastructure élastique à l'aide de NuGet](sql-database-elastic-scale-add-references-visual-studio.md)
3. Recherchez le fichier binaire du client de l'infrastructure élastique (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**).
4. Placez le DLL dans le dossier ShardElasticityModule et compressez le dossier. 
3. Dans votre compte Azure Automation, téléchargez le fichier ShardElasticityModule.zip en tant que **Ressource**. 
4. Dans Azure Automation, créez des **Informations d'identification de ressources** appelées  *ElasticScaleCredential* qui comportent le nom d'utilisateur et le mot de passe de votre serveur de base de données SQL Azure. 
5. Créez une **Variable de ressource** appelée  *SqlServerName* pour votre nom complet de serveur de base de données SQL Azure. 
5. Téléchargez **SetupShardedEnvironment.ps1**, **ProvisionBySize.ps1**, **ProvisionByDate.ps1** et **ProvisionByDate.ps1** sous la forme de runbooks. 
6. Sous la forme d'une opération unique, testez le Runbook **SetupShardedEnvironment.ps1** pour approvisionner l'environnement partitionné. 
7. Publiez un ou plusieurs des Runbook restants et liez-les à une planification. 
8. Observez la sortie du Runbook via l'onglet **TÂCHES**. 

Si les instructions de l'exemple rapide n'ont pas réussi, consultez les instructions de l'exemple détaillé ci-dessous.  

## Pour utiliser des Runbook

1. Création et empaquetage d'un module PowerShell 
2. Création d'un compte Microsoft Azure Automation 
3. Téléchargement du module PowerShell vers Azure Automation en tant que ressource 
4. Création des ressources d'informations d'identification et de variables Azure Automation 
5. Téléchargement des Runbook PowerShell vers Azure Automation 
6. Installation d'un environnement partitionné 
7. Test des Runbook Automation 
8. Publication des Runbook 
9. Planification des Runbook 


## Création et empaquetage d'un module PowerShell 

La première étape consiste à créer un module PowerShell qui référence les assemblys de l'infrastructure élastique et à empaqueter ce module afin qu'il soit prêt à être téléchargé vers le service Azure Automation en tant que ressource. 

1. Téléchargez le fichier " ShardElasticity.zip ".
2. Extrayez tout le contenu.

    ![Extract all][1]
3. Obtenez la DLL du client de l'infrastructure élastique (c'est-à-dire Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll) et copiez les fichiers suivants dans votre dossier local " ShardElasticityModule " téléchargé à l'étape 1.  Cette opération peut être réalisée de deux manières : 1) téléchargez le DLL via le package NuGet de l'infrastructure élastique [link] ou 2) à partir de votre projet Starter Kit de l'infrastructure élastique [link] (doit être généré), accédez à \bin\Debug\ pour obtenir le DLL.

    ![Obtain Dll][2]

4. Compressez le dossier ShardElasticityModule. 

    Remarque : Azure Automation nécessite plusieurs conventions de nom : si le nom du module est ShardElasticityModule.psm1, le nom du fichier .zip doit correspondre exactement (ShardElasticityModule.zip). Le fichier .zip contient le dossier ShardElasticityModule (nom correspondant au nom du module), qui contient le fichier .psm1. Si cette structure n'est pas suivie, Azure Automation ne peut pas décompresser le module.

5.    Une fois que vous avez vérifié que le contenu et la structure du dossier compressé correspondent aux conditions requises, passez à l'étape suivante. Cela doit ressembler à ceci :

    ![dll][3]


## Inscription à la version préliminaire d'Azure Automation

1. Accédez aux [fonctionnalités de la version préliminaire d'Azure](http://azure.microsoft.com/services/preview/).
    
2. Cliquez sur **Essayer**.

    ![Click Try It][8]

2. Accédez au[ portail Microsoft Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com).
3. Cliquez sur **Automation**.

    ![Automation][4]
4. En bas de l'écran, cliquez sur **Créer**. 
5. Dans l'invite ci-dessous, entrez un nom de compte valide et cliquez sur la coche dans le coin inférieur droit de la zone.

    ![Prompt][5] 
5. Passez à l'étape suivante. L'illustration ci-dessous représente une opération réussie.

    ![success][6]

## Téléchargement du module PowerShell vers Azure Automation en tant que ressource 

Téléchargez le module PowerShell ci-dessus vers votre compte Azure Automation. Par exemple, le module contient un ensemble de fonctions d'élasticité des partitions et des DLL de l'infrastructure élastique qui peuvent être référencés à partir des Runbook. 

1. Cliquez sur **RESSOURCES** dans le ruban en haut de l'écran.
2. Cliquez sur **MODULE D'IMPORTATION** en bas de la page. 
3. Cliquez sur **RECHERCHER LE FICHIER...**, puis recherchez le fichier **ShardElasticityModule.zip** ci-dessus. 
4. Une fois que le fichier correct a été choisi, cliquez sur la coche dans le coin inférieur droit de la zone pour l'importer. Le service Azure Automation importe le module. 
5. Passez à l'étape suivante. Ce graphique illustre une opération réussie. Si le module n'a pas été importé avec succès, vérifiez que le fichier .zip correspond aux conventions décrites ci-dessus.

    ![Assets][10] 
      
## Création des ressources d'informations d'identification et de variables Azure Automation 

Au lieu de coder en dur les informations d'identification et les variables utilisées dans les Runbook, Azure Automation peut créer des ressources d'informations d'identification et de variables, respectivement, qui peuvent être référencées dans de nombreux Runbook. Par exemple, la modification d'un mot de passe qui ne se produit ensuite qu'à un seul emplacement. 

1. Sélectionnez le compte Azure Automation que vous venez de créer.
2. Sous le compte **ShardElasticityExamples**, cliquez sur **RESSOURCES** dans le ruban.
3. Cliquez sur **AJOUTER UN PARAMÈTRE** au bas de l'écran.  
4. Cliquez sur **AJOUTER LES INFORMATIONS D'IDENTIFICATION**. 

    ![Add credential][9]
4. Sélectionnez **Informations d'identification de Windows PowerShell** comme **TYPE D'INFORMATIONS D'IDENTIFICATION** et **ElasticScaleCredential** comme nom. La description est facultative.  
5. Cliquez sur la flèche dans le coin inférieur droit de la zone. 

    Remarque : pour utiliser les Runbook sans modification, utilisez les noms de variables textuels, comme indiqué dans les instructions. Les noms de variables sont référencés par les Runbook. 
5. Insérez le nom d'utilisateur et le mot de passe (deux fois) pour le serveur de base de données SQL Azure sur lequel vous souhaitez exécuter les exemples d'élasticité des partitions. 
 
6. Pour créer la ressource de variable, cliquez sur **AJOUTER UN PARAMÈTRE**, puis sélectionnez **AJOUTER UNE VARIABLE**. 

    ![Add variable][7]
7. Pour ce didacticiel, créez une variable pour le nom du serveur de base de données SQL Azure complet sous lequel résident le gestionnaire des cartes de partitions et les bases de données partitionnées. Sélectionnez **Chaîne** comme **TYPE DE VARIABLE** et entrez **SqlServerName**. Cliquez sur la flèche pour continuer. 
8. Entrez le nom du serveur de base de données SQL Azure complet en tant que VALEUR et cliquez sur la coche. 
9. Vous venez de créer une ressource d'informations d'identification et de variables qui sera utilisée dans les Runbook de l'élasticité des partitions.  Passez à l'étape suivante. Une opération réussie ressemble à ceci : 
    

## Téléchargement des Runbook PowerShell vers Azure Automation 

Téléchargez les quatre exemples de Runbook PowerShell fournis. 

1. Pour télécharger un nouveau Runbook Azure Automation, cliquez sur **RUNBOOKS** dans le ruban. 
2. En bas de l'écran, cliquez sur **IMPORTER**. 
3. Accédez au dossier contenant le fichier, sélectionnez **SetupShardedEnvironment.ps1** et cliquez sur la coche. 
4. Répétez les étapes 2 et 3 pour les trois Runbook PowerShell restants (**ProvisionByDate.ps1**, **ProvisionBySize.ps1** et **ReduceServiceTier.ps1**). 
5. Passez à l'étape suivante. 

## Installation d'un environnement partitionné 

L'étape suivante consiste à exécuter le Runbook **SetupShardedEnvironment** qui approvisionne un environnement partitionné comportant une base de données de gestionnaire des cartes de partitions, une carte de partitions de plages et une partition pour le jour actuel.   

1. Sélectionnez le Runbook **SetupShardedEnvironment** en cliquant sur son nom. 
2. Cliquez sur **AUTEUR** dans le ruban. 
3. À partir de cet écran, vous verrez le code qui compose le Runbook (et vous serez en mesure de le modifier, le cas échéant). Pour installer l'environnement partitionné, cliquez sur le bouton **TESTER** au bas de l'écran. Confirmez que vous souhaitez enregistrer et tester le Runbook. 
4. Vous pouvez surveiller l'état de la tâche dans le volet de sortie. Lorsque vous avez terminé, le serveur de base de données SQL Azure que vous avez spécifié est alimenté par une base de données de gestionnaire des cartes de partitions, ainsi que par une base de données de partition. Le Runbook **SetupShardedEnvironment** est uniquement destiné à approvisionner l'environnement partitionné et n'est pas censé s'exécuter selon une planification récurrente. Passez à l'étape suivante.  

## Test des Runbook d'Automation 

Testez l'exécution de chaque Runbook avant sa publication et sa planification. 

1. Cliquez sur **RUNBOOK** dans le ruban en haut de la page. 
2. Cliquez sur le Runbook **ProvisionByDate**.
3. Cliquez sur **AUTEUR** dans le ruban en haut de la page. 
4. Cliquez sur **ENREGISTRER**, puis sur **TESTER**.
5. Répétez cette opération pour le Runbook **ReduceServiceTier**. 

    Notez que, **ProvisionBySize** et **ProvisionByDate** approvisionnant de nouvelles partitions (en utilisant des algorithmes différents), il n'est pas nécessaire d'exécuter **ProvisionByDate** à ce stade. 

## Publication des Runbook 
L'étape suivante consiste à publier le Runbook afin qu'il puisse être planifié pour s'exécuter sur une base périodique. 

1. En bas de la page, cliquez sur **PUBLIER**. 
2. Cliquez sur **Publié**. 
3. Passez à l'étape suivante.
 
## Planification du Runbook 

L'étape finale consiste à créer et lier une planification au Runbook publié ci-dessus. 

1. Cliquez sur **PLANIFIER** en haut de la page. 
2. Cliquez sur **LIER À UNE NOUVELLE PLANIFICATION**.
3. Nommez la planification de manière appropriée, puis cliquez sur la flèche vers la droite.
4. Configurez la planification.
5. Lorsque vous avez terminé, cliquez sur la coche en bas de la zone.
6. Une fois que la tâche a été exécutée selon la planification établie précédemment, cliquez sur l'option **TÂCHES** dans le ruban en haut de la page, puis sélectionnez la tâche planifiée. 

## Conclusion 

Vous avez maintenant correctement créé et empaqueté un module PowerShell, téléchargé le module PowerShell vers Azure Automation en tant que ressource et créé, testé, publié et planifié un Runbook.  Pour surveiller l'intégrité et l'état du Runbook, utilisez les onglets du tableau de bord et des tâches. 

Les exemples présentés ne font que gratter la surface de ce qui est possible lors de la combinaison de l'infrastructure élastique, de la base de données SQL Azure et d'Azure Automation. Testez et créez à partir de ces exemples pour permettre à votre application d'infrastructure élastique de base de données SQL Azure de mettre à l'échelle horizontalement et/ou verticalement. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-scripting/assets.png

<!--HONumber=47-->
