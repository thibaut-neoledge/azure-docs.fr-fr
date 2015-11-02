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
	ms.date="10/15/2015"
	ms.author="garye"/>


# Guide de résolution des problèmes : création d'un espace de travail Machine Learning et connexion à celui-ci

Ce guide propose des solutions pour quelques-uns des défis qui se posent souvent quand vous configurez des espaces de travail Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Propriétaire de l'espace de travail

Quand vous créez un espace de travail Machine Learning, le code que vous entrez dans le champ PROPRIÉTAIRE DE L'ESPACE DE TRAVAIL doit être un compte Microsoft valide (anciennement Windows Live ID), par exemple, john-contoso@live.com ou john-contoso@hotmail.com. Il ne peut pas s'agir d'un compte autre que Microsoft, tel que votre compte de messagerie d'entreprise. Pour créer un compte Microsoft gratuit, accédez à [www.live.com](http://www.live.com).

Notez que le compte que vous utilisez pour vous connecter au portail Azure afin de créer l’espace de travail n’est pas automatiquement autorisé à *ouvrir* cet espace de travail, sauf si vous spécifiez ce compte comme propriétaire. Pour ouvrir un espace de travail de Machine Learning Studio, vous devez être connecté au compte Microsoft qui a été défini comme propriétaire de l'espace de travail ou recevoir une invitation du propriétaire à rejoindre l'espace de travail. À partir du portail Azure, cependant, vous pouvez *gérer* l'espace de travail, notamment modifier le propriétaire et configurer l'accès.

Pour plus d'informations sur la gestion d'un espace de travail, consultez [Gestion d'un espace de travail Azure Machine Learning].

[Gestion d'un espace de travail Azure Machine Learning]: machine-learning-manage-workspace.md

## Régions autorisées

Machine Learning est actuellement disponible dans un nombre limité de régions. Si votre abonnement n’inclut une de ces régions, le message d’erreur suivant risque de s’afficher : « Vous ne disposez d’aucun abonnement dans les régions autorisées ».

Pour demander qu’une région soit ajoutée à votre abonnement, sélectionnez **Contacter le support Microsoft** dans le portail de gestion Azure, choisissez **Facturation** comme type de problème, puis suivez les invites pour envoyer votre demande.

![Contact Microsoft support][screen1]

## Compte de stockage

Le service Machine Learning a besoin d'un compte de stockage pour stocker les données. Vous pouvez utiliser un compte de stockage existant ou vous pouvez en créer un quand vous créez l’espace de travail Machine Learning (si vous disposez d’un quota pour la création d’un compte de stockage).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the management portal, go to **Settings** and then click **Usage**.
-->

![Create workspace][screen2]

Une fois l'espace de travail Machine Learning créé, vous pouvez vous connecter à Machine Learning Studio au moyen du compte Microsoft que vous avez spécifié comme propriétaire de l'espace de travail. Si vous rencontrez le message d'erreur « Espace de travail introuvable » (comme dans la capture d'écran suivante), effectuez les étapes suivantes pour supprimer les cookies de votre navigateur.

![Workspace not found][screen3]

**Pour supprimer les cookies du navigateur**

Si vous utilisez Internet Explorer, cliquez sur le bouton **Outils** dans le coin supérieur droit et sélectionnez **Options Internet**.

![Internet options][screen4]

Sous l'onglet **Général**, cliquez sur **Supprimer…**

![General tab][screen5]

Dans la boîte de dialogue **Supprimer l’historique de navigation**, vérifiez que **Cookies et données de sites web** est sélectionné, puis cliquez sur **Supprimer**.

![Delete cookies][screen6]

Une fois les cookies supprimés, redémarrez le navigateur, puis accédez à la page [Microsoft Azure Machine Learning](https://studio.azureml.net). Lorsque vous êtes invité à fournir un nom d'utilisateur et un mot de passe, entrez le même compte Microsoft que celui que vous avez spécifié en tant que propriétaire de l'espace de travail.

Notre objectif est de vous offrir une expérience de Machine Learning qui soit aussi transparente que possible. Publiez tous vos commentaires et problèmes sur le [forum Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour nous permettre de vous aider au mieux.

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

<!---HONumber=Oct15_HO4-->