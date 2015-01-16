<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Guide de résolution des problèmes : création et connexion à un espace de travail Azure Machine Learning | Azure" description="Solutions aux problèmes courants liés à la création d'un espace de travail Azure Machine Learning et à la connexion à un tel espace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/25/2014" ms.author="garye" />


#Guide de résolution des problèmes : création et connexion à un espace de travail Azure Machine Learning

Ce guide propose des solutions pour quelques-uns des défis qui se posent souvent lors de la configuration d'espaces de travail Azure ML.

##Propriétaire de l'espace de travail

Lorsque vous créez un nouvel espace de travail Azure ML, l'identifiant que vous entrez dans le champ PROPRIÉTAIRE DE L'ESPACE DE TRAVAIL doit être un compte Microsoft valide (anciennement Windows Live ID), tel que john-contoso@live.com ou john-contoso@hotmail.com. Vous ne pouvez pas saisir l'adresse d'un compte non Microsoft, comme votre compte de messagerie d'entreprise. Pour créer un compte Microsoft gratuit, accédez à [www.live.com](http://www.live.com). 

##Régions autorisées

Une version préliminaire publique d'Azure ML est actuellement disponible dans la région sud du centre des États-Unis. Si votre abonnement n'inclut pas le Sud du centre des États-Unis, l'erreur suivante risque de s'afficher : " Vous ne disposez d'aucun abonnement dans les régions autorisées. Régions autorisées : Sud du centre des États-Unis. " 

Pour résoudre ce problème, sélectionnez " Contacter le support Microsoft " (illustré ci-dessous) à partir du portail Azure et choisissez **Facturation** comme **TYPE DE SUPPORT** pour ajouter cette région à votre abonnement. Azure ML ajoutera progressivement de nombreuses autres régions.

![Contact Microsoft support][screen1]

##Compte de stockage
 
Le service Azure ML a besoin d'un compte de stockage pour stocker les données. Vous pouvez utiliser un compte de stockage existant dans le Sud du centre des États-Unis ou vous pouvez en créer un lorsque vous créez l'espace de travail ML (si vous disposez d'un quota pour la création d'un compte de stockage). Pour savoir si vous pouvez créer un nouveau compte de stockage, dans le portail Azure, accédez à **Paramètres**, puis à **Utilisation**.

![Create workspace][screen2]

Une fois l'espace de travail ML créé, vous pouvez vous connecter à ML Studio au moyen du compte Microsoft que vous avez spécifié comme propriétaire de l'espace de travail. Si l'erreur " Espace de travail introuvable " apparaît, comme dans la capture d'écran ci-dessous, procédez comme suit pour corriger le problème.

![Workspace not found][screen3]

1. Supprimez les cookies du navigateur.

	Si vous utilisez Internet Explorer, cliquez sur le bouton **Outils** dans le coin supérieur droit et sélectionnez **Options Internet**.  

	![Internet options][screen4]

	Sous l'onglet **Général**, cliquez sur **Supprimer...**

	![General tab][screen5]

	Dans la boîte de dialogue **Supprimer l'historique de navigation**, vérifiez que **Cookies et données de sites Web** est sélectionné et cliquez sur **Supprimer**.

	![Delete cookies][screen6]

2. Une fois les cookies supprimés, redémarrez le navigateur et accédez à [https://studio.azureml.net](https://studio.azureml.net). Lorsque vous êtes invité à fournir un nom d'utilisateur et un mot de passe, entrez le même compte Microsoft que celui que vous avez spécifié en tant que propriétaire de l'espace de travail.

Notre objectif est de vous offrir une expérience d'Azure ML qui soit aussi fluide et transparente que possible. Publiez tous vos commentaires et problèmes ci-dessous ou sur le [forum Azure ML](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning) pour nous permettre de vous aider au mieux. 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
