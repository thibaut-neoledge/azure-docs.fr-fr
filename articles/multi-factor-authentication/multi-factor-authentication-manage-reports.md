<properties 
	pageTitle="Rapports Azure Multi-Factor Authentication" 
	description="Cette section décrit comment utiliser la fonctionnalité de rapports d’Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Rapports dans Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation. Ces rapports sont accessibles via le portail de gestion de Multi-Factor Authentication. Voici la liste des rapports disponibles.

Vous pouvez accéder à ces rapports via le portail de gestion d’Azure.

Nom| Description
:------------- | :------------- | 
Utilisation | Les rapports d'utilisation affichent des informations sur l'utilisation générale, des récapitulatifs par utilisateurs et des détails sur les utilisateurs.
État du serveur|Ce rapport affiche l'état des serveurs Multi-Factor Authentication associés à votre compte.
Historique de l'utilisateur bloqué|Ces rapports présentent l'historique des demandes de blocage ou déblocage des utilisateurs.
Historique de l'utilisateur contourné|Affiche l'historique des demandes de contournement de Multi-Factor Authentication pour le numéro de téléphone d'un utilisateur.
Alerte de fraude|Affiche un historique des alertes de fraude soumises au cours de la plage de dates spécifiée.
Mis en file d'attente.|Répertorie les rapports en file d'attente de traitement et leur état. Un lien pour télécharger ou afficher le rapport est fourni lorsque ce dernier est terminé.

## Pour afficher des rapports

1. Connectez-vous sur [http://azure.microsoft.com](http://azure.microsoft.com)
2. Sélectionnez Active Directory à gauche.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui pour lequel vous souhaitez afficher le rapport d’alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Multi-Factor Authentication.
5. Dans le portail de gestion Multi-Factor Authentication, sur la gauche, le message Afficher un rapport apparaît. Ici, vous pouvez sélectionner les rapports décrits ci-dessus.


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Ressources supplémentaires**

* [Pour les utilisateurs](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication sur MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=August15_HO6-->