<properties 
	pageTitle="Opérations Azure AD Connect Health" 
	description="Ceci est la page dédiée à Azure AD Connect Health qui décrit les opérations supplémentaires pouvant être effectuées après le déploiement d’Azure AD Connect Health." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>

# Opérations Azure AD Connect Health

La rubrique suivante décrit les différentes opérations pouvant être effectuées à l’aide d’Azure AD Connect Health.

## Notifications par courrier électronique
Vous pouvez configurer le service Azure AD Connect Health pour qu’il envoie des notifications par courrier électronique lorsque des alertes sont générées pour indiquer que l’infrastructure du service de fédération présente un défaut d’intégrité. Cela se produit lorsqu’une alerte est générée, et quand elle est marquée comme résolue. Pour configurer les notifications par courrier électronique, suivez les instructions ci-dessous. Notez que les notifications par courrier électronique sont désactivées par défaut.


### Pour activer les notifications par courrier électronique Azure AD Connect Health

1. Ouvrez le panneau Alertes pour la batterie pour laquelle vous souhaitez recevoir des notifications par courrier électronique.
1. Dans la barre d’actions, cliquez sur le bouton Paramètres de Notification.
1. Activez les notifications par courrier électronique.
1. Sélectionnez la case à cocher pour configurer les notifications par courrier électronique pour l’ensemble des administrateurs généraux des locataires.
1. Si vous souhaitez recevoir des notifications par courrier électronique sur d’autres adresses, indiquez-les dans les zones Destinataires d’e-mail supplémentaires. Pour supprimer une adresse de messagerie de cette liste, cliquez avec le bouton droit sur l’entrée, puis sélectionnez Supprimer.
1. Pour valider les modifications, cliquez sur Enregistrer. Les modifications prendront effet une fois que vous avez cliqué sur Enregistrer.






## Supprimer un serveur du service Azure AD Connect Health

Dans certains cas, vous pouvez envisager de retirer un serveur de la surveillance. Pour supprimer un serveur du service Azure AD Connect Health, suivez les instructions ci-dessous.

Lorsque vous supprimez un serveur, tenez compte des points suivants :

- Cette action INTERROMPT la collecte des données de ce serveur. Ce serveur sera retiré du service de surveillance. Après l’exécution de cette action, vous n’aurez plus accès aux nouvelles alertes,ni aux données de surveillance et d’analyse de l’utilisation pour ce serveur.
- Cette action NE SUPPRIMERA NI NE DÉSINSTALLERA l’agent Health du serveur. Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent.
- Cette action ne supprimera PAS les données déjà collectées à partir de ce serveur. Ces données seront supprimées conformément à la politique de conservation des données Microsoft Azure. 
- Après avoir effectué cette opération, si vous souhaitez commencer à surveiller de nouveau le même serveur, il vous faudra désinstaller puis réinstaller l’agent Health sur ce serveur. 


	### Pour supprimer un serveur du service Azure AD Connect Health
<ol>
1. Sélectionnez le nom du serveur à supprimer afin d’ouvrir le panneau Serveur du panneau Liste des services. 
1. Sur le panneau Serveur, cliquez sur le bouton Supprimer de la barre d’actions.
1. Pour valider la suppression, saisissez le nom du serveur dans la zone de confirmation.
1. Cliquez sur le bouton Supprimer.







## Supprimer une instance du service Azure AD Connect Health

Dans certains cas, vous pouvez envisager de supprimer une instance de service. Pour supprimer une instance du service Azure AD Connect Health, suivez les instructions ci-dessous.

Lorsque vous supprimez une instance de service, tenez compte des éléments suivants :

- Cette action va supprimer l’instance de service actuelle du service de surveillance. 
- Cette action NE SUPPRIMERA NI NE DÉSINSTALLERA l’agent Health des serveurs surveillés avec cette instance de service. Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent. 
- Toutes les données de cette instance de service seront supprimées, conformément à la stratégie de conservation des données Microsoft Azure. 
- Après avoir effectué cette opération, si vous souhaitez commencer à surveiller le service, désinstallez puis réinstallez l’agent Health de l’ensemble des serveurs à surveiller. Après avoir effectué cette opération, si vous souhaitez commencer à surveiller de nouveau le même serveur, il vous faudra désinstaller puis réinstaller l’agent Health sur ce serveur.







	### Pour supprimer une instance du service Azure AD Connect Health
<ol>
1. À partir du panneau Liste des services, ouvrez le panneau Service en sélectionnant l’identificateur de service (nom de batterie) que vous souhaitez supprimer. 
1. Sur le panneau Serveur, cliquez sur le bouton Supprimer de la barre d’actions.
1. Pour valider le nom du service, saisissez-le dans la zone de confirmation (par exemple : sts.contoso.com). 
1. Cliquez sur le bouton Supprimer.

<!---HONumber=July15_HO5-->