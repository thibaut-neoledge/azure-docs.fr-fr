<properties 
	pageTitle="Résolution de problèmes : création d'un espace de travail Machine Learning et connexion à celui-ci | Microsoft Azure" 
	description="Solutions aux problèmes courants liés à la création d'un espace de travail Azure Machine Learning et à la connexion à un tel espace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="garye"/>


#Guide de résolution des problèmes : création d'un espace de travail Machine Learning et connexion à celui-ci

Ce guide propose des solutions pour quelques-uns des défis qui se posent souvent quand vous configurez des espaces de travail Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Propriétaire de l'espace de travail

Quand vous créez un espace de travail Machine Learning, le code que vous entrez dans le champ PROPRIÉTAIRE DE L'ESPACE DE TRAVAIL doit être un compte Microsoft valide (anciennement Windows Live ID), par exemple, john-contoso@live.com ou john-contoso@hotmail.com. Il ne peut pas s'agir d'un compte autre que Microsoft, tel que votre compte de messagerie d'entreprise. Pour créer un compte Microsoft gratuit, accédez à [www.live.com](http://www.live.com).

Notez que le compte que vous utilisez pour vous connecter au portail Azure afin de créer l’espace de travail n’est pas automatiquement autorisé à *ouvrir* cet espace de travail, sauf si vous spécifiez ce compte comme propriétaire. Pour ouvrir un espace de travail de Machine Learning Studio, vous devez être connecté au compte Microsoft qui a été défini comme propriétaire de l'espace de travail ou recevoir une invitation du propriétaire à rejoindre l'espace de travail. À partir du portail Azure, cependant, vous pouvez *gérer* l'espace de travail, notamment modifier le propriétaire et configurer l'accès.

Pour plus d'informations sur la gestion d'un espace de travail, consultez [Gestion d'un espace de travail Azure Machine Learning].

[Gestion d'un espace de travail Azure Machine Learning]: machine-learning-manage-workspace.md

##Régions autorisées

Machine Learning est actuellement disponible dans la région Sud du centre des États-Unis. Si votre abonnement n'inclut pas le Sud du centre des États-Unis, le message d'erreur suivant risque de s'afficher : « Vous ne disposez d'aucun abonnement dans les régions autorisées. Régions autorisées : Sud du centre des États-Unis. »

Pour résoudre ce problème, comme illustré dans la capture d'écran suivante, sélectionnez **Contacter le support Microsoft** à partir du portail de gestion Azure et choisissez **Facturation** comme **TYPE DE SUPPORT** pour ajouter cette région à votre abonnement.

![Contact Microsoft support][screen1]

##Compte de stockage
 
Le service Machine Learning a besoin d'un compte de stockage pour stocker les données. Vous pouvez utiliser un compte de stockage existant dans la région Sud du centre des États-Unis ou vous pouvez en créer un quand vous créez l'espace de travail Machine Learning (si vous disposez d'un quota pour la création d'un compte de stockage). Pour savoir si vous pouvez créer un compte de stockage, dans le portail de gestion, accédez à **Paramètres**, puis cliquez sur **Utilisation**.

![Create workspace][screen2]

Une fois l'espace de travail Machine Learning créé, vous pouvez vous connecter à Machine Learning Studio au moyen du compte Microsoft que vous avez spécifié comme propriétaire de l'espace de travail. Si vous rencontrez le message d'erreur « Espace de travail introuvable » (comme dans la capture d'écran suivante), effectuez les étapes suivantes pour supprimer les cookies de votre navigateur.

![Workspace not found][screen3]

**Pour supprimer les cookies du navigateur**

	If you use Internet Explorer, click the **Tools** button in the upper-right corner and select **Internet options**.  

	![Internet options][screen4]

	Under the **General** tab, click **Delete…**

	![General tab][screen5]

	In the **Delete Browsing History** dialog box, make sure **Cookies and website data** is selected, and click **Delete**.

	![Delete cookies][screen6]

Une fois les cookies supprimés, redémarrez le navigateur, puis accédez à la page [Microsoft Azure Machine Learning](https://studio.azureml.net). Lorsque vous êtes invité à fournir un nom d'utilisateur et un mot de passe, entrez le même compte Microsoft que celui que vous avez spécifié en tant que propriétaire de l'espace de travail.

Notre objectif est de vous offrir une expérience de Machine Learning qui soit aussi transparente que possible. Publiez tous vos commentaires et problèmes sur le [forum Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour nous permettre de vous aider au mieux.

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
 

<!---HONumber=August15_HO6-->