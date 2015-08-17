<properties
	pageTitle="Machine virtuelle pour la science des données dans Azure | Microsoft Azure"
	description="Configurer une machine virtuelle pour la science des données"
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="mohabib;xibingao;bradsev" />

# Machine virtuelle pour la science des données dans Azure

Vous pouvez approvisionner et configurer plusieurs types de machines virtuelles Azure en vue de les utiliser au sein d’un environnement de science des données dans le cloud. Le choix du type de machine virtuelle à utiliser dépend du type et de la quantité des données à modéliser avec l’apprentissage automatique, ainsi que de la destination cible de ces données dans le cloud. Pour plus d’informations sur les questions à prendre en compte lors de cette prise de décision, consultez la page [Planifier votre environnement de science des données Azure Machine Learning](machine-learning-data-science-plan-your-environment.md). Pour accéder à un catalogue de scénarios que vous pouvez rencontrer lorsque vous effectuez des analyses avancées, consultez la page [Processus de science des données cloud dans Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md)

Les instructions fournies décrivent la procédure de configuration d’une machine virtuelle Azure et d’une machine virtuelle Azure équipée du service SQL en tant que serveurs Notebook IPython. La machine virtuelle Windows est configurée avec des outils connexes, tels que Notebook IPython, l’Explorateur de stockage Azure et AzCopy, ainsi que d’autres utilitaires utiles pour les projets de science des données. Par exemple, l’Explorateur de stockage Azure et AzCopy facilitent le chargement de données dans le stockage Azure depuis votre ordinateur local ou le téléchargement de ces données vers votre ordinateur local à partir du stockage. Deux ensembles d’instructions vous sont fournis :

* L’article [Configurer une machine virtuelle Azure comme serveur IPython Notebook pour des analyses avancées](machine-learning-data-science-setup-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure avec Notebook IPython et d’autres outils utilisés pour des projets de science des données permettant de recourir à une forme de stockage Azure autre que SQL pour le stockage des données.

* L’article [Configurer une machine virtuelle Azure SQL Server comme serveur IPython Notebook pour des analyses avancées](machine-learning-data-science-setup-sql-server-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure SQL Server avec Notebook IPython et d’autres outils utilisés pour des projets de science des données permettant de recourir à une base de données SQL pour le stockage des données.

Une fois approvisionnées et configurées, ces machines virtuelles sont utilisables sous forme de serveurs Notebook IPython pour l’exploration et le traitement des données, ainsi que pour l’exécution d’autres tâches avec Azure Machine Learning et le processus de science des données dans le cloud. Les étapes suivantes du traitement de science de données sont présentées dans le [Guide d’apprentissage : traitement des données avancé dans Microsoft Azure](machine-learning-data-science-advanced-data-processing.md) et peuvent inclure des étapes de déplacement de données dans SQL Server ou HDInsight, de traitement et d’échantillonnage des données en vue d’en extraire de l’information pertinente avec Azure Machine Learning.


> [AZURE.NOTE]Le service Azure Virtual Machines est facturé au tarif du **paiement à l’utilisation**. Pour vous assurer que vous n’êtes pas facturé lorsque vous n’utilisez pas votre machine virtuelle, cette dernière doit être définie sur l’état **Arrêté (désalloué)** à partir du [Portail de gestion Azure](http://manage.windowsazure.com/). Pour obtenir des instructions détaillées sur la libération de votre machine virtuelle, consultez la page [Arrêter et libérer une machine virtuelle inutilisée](machine-learning-data-science-setup-virtual-machine.md#shutdown).
 

<!---HONumber=August15_HO6-->