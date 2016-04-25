<properties 
	pageTitle="FAQ sur la gestion des API Azure | Microsoft Azure" 
	description="Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour la gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="sdanie"/>

# FAQ sur la gestion des API Azure

Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour la gestion des API Azure.

## Forum Aux Questions

-	[Comment puis-je poser une question à l’équipe de gestion des API ?](#how-can-i-ask-a-question-to-the-api-management-team)
-	[Qu’est-ce que cela signifie lorsqu’une fonctionnalité est disponible en version préliminaire ?](#what-does-it-mean-if-a-feature-is-in-preview)
-	[Quelles sont les options prises en charge pour sécuriser la connexion entre la passerelle de gestion des API et mes services principaux ?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[Comment puis-je copier une instance de la gestion des API vers une nouvelle instance ?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[Puis-je gérer mon instance de gestion des API par programme ?](#can-i-manage-my-api-management-instance-programmatically)
-	[Comment puis-je ajouter un utilisateur au groupe d’administrateurs ?](#how-can-i-add-a-user-to-the-administrators-group)
-	[Pourquoi la stratégie que je veux ajouter n’est-elle pas activée dans l’éditeur de stratégie ?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)

### Comment puis-je poser une question à l’équipe de gestion des API ?

-	Vous pouvez publier vos questions sur notre [forum MSDN de gestion des API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	Vous pouvez également nous envoyer un courrier électronique à l’adresse : `apimgmt@microsoft.com`.

### Qu’est-ce que cela signifie lorsqu’une fonctionnalité est disponible en version préliminaire ?

Une fonctionnalité en version préliminaire est complète et opérationnelle, mais elle est en version préliminaire pour nous permettre de collecter activement les commentaires client à son sujet. En fonction des commentaires client reçus, nous serons peut-être amenés à apporter des modifications avec rupture. Nous vous recommandons donc de ne pas utiliser cette fonctionnalité comme composant essentiel dans votre environnement de production. Si vous avez des commentaires sur les fonctionnalités en version préliminaire, n’hésitez pas à nous le faire savoir de l’une des façons décrites dans [Comment puis-je poser une question à l’équipe de gestion des API ?](#how-can-i-ask-a-question-to-the-api-management-team).

### Quelles sont les options prises en charge pour sécuriser la connexion entre la passerelle de gestion des API et mes services principaux ?

Il existe plusieurs options prises en charge.

1. Utilisez l’authentification HTTP de base. Pour plus d’informations, consultez [Configuration des paramètres de l’API](api-management-howto-create-apis.md#configure-api-settings).
2. Utilisez l’authentification mutuelle SSL telle que décrite dans [Comment sécuriser des services principaux à l’aide d’une authentification par certificat client dans la Gestion des API Azure](api-management-howto-mutual-certificates.md).
3. Utilisez une liste blanche des adresses IP sur votre service principal. Si vous avez une instance de gestion des API de niveau Standard ou Premium, l’adresse IP de la passerelle reste constante et vous pouvez configurer votre liste blanche pour autoriser cette adresse IP. Vous pouvez récupérer l’adresse IP de votre instance de gestion des API sur le **Tableau de bord** dans le portail Azure Classic.
4. Vous pouvez connecter votre instance de gestion des API à un réseau virtuel Azure (classique). Pour plus d’informations, consultez [Comment configurer des connexions VPN dans Gestion des API Azure](api-management-howto-setup-vpn.md).

### Comment puis-je copier une instance de la gestion des API vers une nouvelle instance ?

Il existe plusieurs options que vous pouvez utiliser pour copier une instance de service de gestion des API vers une nouvelle instance.

-	Utilisez la fonctionnalité de sauvegarde et restauration de la gestion des API. Pour plus d’informations, consultez [Comment implémenter une récupération d’urgence à l’aide d’une sauvegarde de service et la récupérer dans Gestion des API Azure](api-management-howto-disaster-recovery-backup-restore.md).
-	Créez votre propre fonctionnalité de sauvegarde et restauration à l’aide de l’[API REST de gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx) pour enregistrer et restaurer les entités requises à partir de votre instance de service.
-	Téléchargez la configuration du service à l’aide de Git et chargez-la vers une nouvelle instance. Pour plus d’informations, consultez [Découvrez comment enregistrer et configurer votre configuration du service Gestion des API à l’aide de Git](api-management-configuration-repository-git.md).

### Puis-je gérer mon instance de gestion des API par programme ?

Oui, vous pouvez la gérer à l’aide de l’[API REST de gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx) et des applets de commande PowerShell de [déploiement du service](https://msdn.microsoft.com/library/mt619282.aspx) et de [gestion du service](https://msdn.microsoft.com/library/mt613507.aspx).

### Comment puis-je ajouter un utilisateur au groupe d’administrateurs ?

À ce stade, les administrateurs sont limités aux utilisateurs qui se connectent via le portail Azure Classic en tant qu’administrateurs ou co-administrateurs de l’abonnement Azure qui contient l’instance de gestion des API. Les utilisateurs créés dans le portail des éditeurs ne peuvent pas être désignés comme administrateurs ou ajoutés au groupe Administrateurs.


### Pourquoi la stratégie que je veux ajouter n’est-elle pas activée dans l’éditeur de stratégie ?

Si la stratégie que vous souhaitez ajouter n’est pas activée, vérifiez que vous êtes dans l’étendue correcte pour cette stratégie. Chaque instruction de stratégie est conçue pour une utilisation dans certaines étendues et sections de la stratégie. Pour consulter les sections de la stratégie et les étendues pour une stratégie, consultez la section **Utilisation** de cette stratégie dans la [Référence de la stratégie](https://msdn.microsoft.com/library/azure/dn894080.aspx).

<!---HONumber=AcomDC_0413_2016-->