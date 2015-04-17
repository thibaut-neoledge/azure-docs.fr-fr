<properties 
	pageTitle="Configurer une machine virtuelle ou une machine virtuelle SQL Server dans Azure pour la science des données" 
	description="Configurer une machine virtuelle pour la science des données" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev"/>

# Configurer une machine virtuelle ou une machine virtuelle SQL Server dans Azure pour la science des données

Vous pouvez approvisionner et configurer plusieurs types de machines virtuelles Azure en vue de les utiliser au sein d'un environnement de science des données dans le cloud. Le choix du type de machine virtuelle à utiliser dépend du type et de la quantité des données à modéliser avec l'apprentissage automatique, ainsi que de la destination cible de ces données dans le cloud. Pour plus d'informations sur les questions à prendre en compte lors de cette prise de décision, voir [Planifier votre environnement de science des données Azure Machine Learning](machine-learning-data-science-plan-your-environment.md). Les deux scénarios abordés ici comprennent

Les instructions fournies décrivent la procédure de configuration d'une machine virtuelle Azure et d'une machine virtuelle Azure équipée du service SQL en tant que serveurs Notebook IPython. La machine virtuelle s'exécute sur Windows et est configurée avec des outils connexes comme l'Explorateur de stockage Azure et AzCopy, ainsi qu'avec d'autres outils utiles pour les projets de science des données. Par exemple, l'Explorateur de stockage Azure et AzCopy facilitent le chargement de données dans le stockage Azure depuis votre ordinateur local ou le téléchargement de ces données vers votre ordinateur local à partir du stockage. Deux ensembles d'instructions vous sont fournis :

* L'article [Configurer une machine virtuelle Azure pour la science des données](machine-learning-data-science-setup-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure avec Notebook IPython et d'autres outils utilisés pour des projets de science des données permettant de recourir à une forme de stockage Azure autre que SQL pour le stockage des données. 

* L'article [Configurer une machine virtuelle Azure SQL Server pour la science des données](machine-learning-data-science-setup-sql-server-virtual-machine.md) explique comment approvisionner une machine virtuelle Azure SQL Server avec Notebook IPython et d'autres outils utilisés pour des projets de science des données impliquant le stockage des données dans une base de données SQL.

Une fois approvisionnées et configurées, ces machines virtuelles sont utilisables sous forme de serveurs Notebook IPython pour l'exploration et le traitement des données, ainsi que pour l'exécution d'autres tâches avec Azure Machine Learning et le processus de science des données dans le cloud. Ce processus peut inclure des étapes de déplacement, de traitement et d'échantillonnage des données dans HDInsight en vue d'en extraire de l'information pertinente avec Azure Machine Learning.

* Pour plus d'informations sur le déplacement des données dans HDInsight à partir du stockage d'objets blob Azure, voir [Créer et charger des données dans des tables Hive à partir du stockage d'objets blob Azure](machine-learning-data-science-hive-tables.md).
* Pour plus d'informations sur le traitement de données dans HDInsight avec des requêtes Hive, voir [Soumettre des requêtes Hive aux clusters Hadoop HDInsight dans le processus de science des données dans le cloud](machine-learning-data-science-hive-queries.md).
* Pour plus d'informations sur l'échantillonnage de données dans HDInsight, voir [Échantillonner des données dans des tables Hive Azure HDInsight](machine-learning-data-science-sample-data-hive.md).


> [AZURE.NOTE] Le service Azure Virtual Machines est facturé au tarif du **paiement à l'utilisation**. Pour vous assurer que vous n'êtes pas facturé lorsque vous n'utilisez pas votre machine virtuelle, cette dernière doit être définie sur l'état **Arrêté (désalloué)** à partir du [Portail de gestion Azure](http://manage.windowsazure.com/). Pour obtenir des instructions détaillées sur la libération de votre machine virtuelle, voir [Arrêter et libérer une machine virtuelle inutilisée](machine-learning-data-science-setup-virtual-machine.md#shutdown). 




<!--HONumber=49-->