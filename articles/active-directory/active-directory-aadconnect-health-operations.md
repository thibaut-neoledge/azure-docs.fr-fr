<properties
	pageTitle="Opérations Azure AD Connect Health"
	description="Cet article décrit les opérations supplémentaires pouvant être effectuées après le déploiement d’Azure AD Connect Health."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="billmath"/>

# Opérations Azure AD Connect Health

La rubrique suivante décrit les différentes opérations pouvant être effectuées à l’aide d’Azure AD Connect Health.

## Activer les notifications par courrier électronique
Vous pouvez configurer le service Azure AD Connect Health pour qu’il envoie des notifications par courrier électronique quand des alertes sont générées pour indiquer que votre infrastructure d’identité présente un défaut d’intégrité. Cela se produit lorsqu’une alerte est générée, et quand elle est marquée comme résolue. Pour configurer les notifications par courrier électronique, suivez les instructions ci-dessous.
>[AZURE.NOTE] Les notifications par courrier électronique sont désactivées par défaut.


### Pour activer les notifications par courrier électronique Azure AD Connect Health

1. Ouvrez le panneau Alertes pour le service pour lequel vous souhaitez recevoir des notifications par courrier électronique.
2. Dans la barre d’actions, cliquez sur le bouton Paramètres de Notification.
3. Activez les notifications par courrier électronique.
4. Cochez la case pour configurer les notifications par courrier électronique pour l’ensemble des administrateurs généraux.
5. Si vous souhaitez recevoir des notifications par courrier électronique sur d’autres adresses, indiquez-les dans la zone Destinataire d’e-mail supplémentaire. Pour supprimer une adresse de messagerie de cette liste, cliquez avec le bouton droit sur l’entrée, puis sélectionnez Supprimer.
6. Pour valider les modifications, cliquez sur Enregistrer. Les modifications prendront effet une fois que vous avez cliqué sur Enregistrer.

## Supprimer une instance de serveur ou de service

### Supprimer un serveur du service Azure AD Connect Health
Dans certains cas, vous pouvez envisager de retirer un serveur de la surveillance. Pour supprimer un serveur du service Azure AD Connect Health, suivez les instructions ci-dessous.

Lorsque vous supprimez un serveur, tenez compte des points suivants :

- Cette action INTERROMPT la collecte des données de ce serveur. Ce serveur sera retiré du service de surveillance. Après l’exécution de cette action, vous n’aurez plus accès aux nouvelles alertes,ni aux données de surveillance et d’analyse de l’utilisation pour ce serveur.
- Cette action NE SUPPRIMERA NI NE DÉSINSTALLERA l’agent Health du serveur. Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent.
- Cette action ne supprimera PAS les données déjà collectées à partir de ce serveur. Ces données seront supprimées conformément à la politique de conservation des données Microsoft Azure.
- Après avoir effectué cette opération, si vous souhaitez commencer à surveiller de nouveau le même serveur, il vous faudra désinstaller puis réinstaller l’agent Health sur ce serveur.


#### Pour supprimer un serveur du service Azure AD Connect Health

1. Sélectionnez le nom du serveur à supprimer afin d’ouvrir le panneau Serveur du panneau Liste des services.
2. Sur le panneau Serveur, cliquez sur le bouton Supprimer de la barre d’actions.
3. Pour valider la suppression, saisissez le nom du serveur dans la zone de confirmation.
4. Cliquez sur le bouton Supprimer.


### Supprimer une instance du service Azure AD Connect Health

Dans certains cas, vous pouvez envisager de supprimer une instance de service. Pour supprimer une instance du service Azure AD Connect Health, suivez les instructions ci-dessous.

Lorsque vous supprimez une instance de service, tenez compte des éléments suivants :

- Cette action va supprimer l’instance de service actuelle du service de surveillance.
- Cette action NE SUPPRIMERA NI NE DÉSINSTALLERA l’agent Health des serveurs surveillés avec cette instance de service. Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent.
- Toutes les données de cette instance de service seront supprimées, conformément à la stratégie de conservation des données Microsoft Azure.
- Après avoir effectué cette opération, si vous souhaitez commencer à surveiller le service, désinstallez puis réinstallez l’agent Health de l’ensemble des serveurs à surveiller. Après avoir effectué cette opération, si vous souhaitez commencer à surveiller de nouveau le même serveur, il vous faudra désinstaller puis réinstaller l’agent Health sur ce serveur.


#### Pour supprimer une instance du service Azure AD Connect Health

1. À partir du panneau Liste des services, ouvrez le panneau Service en sélectionnant l’identificateur de service (nom de batterie) que vous souhaitez supprimer.
2. Sur le panneau Serveur, cliquez sur le bouton Supprimer de la barre d’actions.
3. Pour valider le nom du service, saisissez-le dans la zone de confirmation (par exemple : sts.contoso.com).
4. Cliquez sur le bouton « Supprimer ». <br><br>


[//]: # "Début de la section RBAC"
## Gérer l’accès avec le contrôle d’accès en fonction du rôle (RBAC)
### Vue d'ensemble
Le [contrôle d’accès en fonction du rôle (RBAC)](role-based-access-control-configure.md) pour Azure AD Connect Health fournit l’accès au service Azure AD Connect Health aux utilisateurs et/ou groupes en dehors des administrateurs généraux. Pour ce faire, des rôles sont affectés aux groupes et/ou utilisateurs concernés et un mécanisme permet de limiter les administrateurs généraux dans votre annuaire.

#### Rôles
Azure AD Connect Health prend en charge les rôles prédéfinis suivants.

| Rôle | Autorisations |
| ----------- | ---------- |
| Propriétaire | Les propriétaires peuvent ***gérer l’accès*** (par exemple, affecter un rôle à un utilisateur/groupe), ***afficher toutes les informations*** (par exemple, afficher des alertes) à partir du portail et ***modifier les paramètres*** (par exemple, envoyer des notifications par courrier électronique) au sein d’Azure AD Connect Health. <br>Par défaut, ce rôle est affecté de manière immuable aux administrateurs généraux Azure AD. |
|Collaborateur| Les collaborateurs peuvent ***afficher toutes les informations*** (par exemple, afficher des alertes) à partir du portail et ***modifier les paramètres*** (par exemple, envoyer des notifications par courrier électronique) au sein d’Azure AD Connect Health.|
|Lecteur| Les lecteurs peuvent ***afficher toutes les informations*** (par exemple, afficher des alertes) à partir du portail au sein d’Azure AD Connect Health.|

Tous les autres rôles (comme « Administrateurs de l’accès utilisateur » ou « Utilisateurs DevTest Lab ») n’ont aucun impact sur l’accès au sein d’Azure AD Connect Health, même s’ils sont disponibles au cours de l’utilisation du portail.

#### Étendue de l’accès

Azure AD Connect prend en charge la gestion des accès à deux niveaux :

- ***Toutes les instances de service***: ceci est le chemin d’accès recommandé pour la plupart des clients, et contrôle l’accès pour toutes les instances de service (par exemple, une batterie de serveurs ADFS) dans tous les types de rôles surveillés par Azure AD Connect Health.

- ***Instance de service***: dans certains cas, vous pouvez être amené à paramétrer l’accès en fonction d’un type de rôle ou d’une instance de service. Dans ce cas, vous pouvez gérer l’accès au niveau de l’instance de service.

L’autorisation est accordée si un utilisateur final a accès au niveau Annuaire ou Instance de service.


### Comment autoriser l’accès des utilisateurs ou des groupes à Azure AD Connect Health
#### Étape 1 : sélectionner l’étendue d’accès appropriée
Pour autoriser un accès utilisateur au niveau *toutes les instances de service* dans Azure AD Connect Health, ouvrez le panneau principal dans Azure AD Connect Health.<br>
#### Étape 2 : ajouter des utilisateurs, des groupes et affecter des rôles
1. Cliquez sur la partie « Utilisateurs » de la section Configurer.<br> ![Panneau principal (section RBAC d’Azure AD Connect Health)](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Sélectionnez « Ajouter ».
3. Sélectionnez le « Rôle », par exemple « Propriétaire ».<br> ![Ajouter un utilisateur (section RBAC d’Azure AD Connect Health)](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Tapez le nom ou l’identificateur du groupe ou de l’utilisateur cible. Vous pouvez sélectionner un ou plusieurs utilisateurs ou groupes en même temps. Cliquez sur « Sélectionner» .
![Sélectionner un utilisateur (section RBAC d’Azure AD Connect Health)](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Sélectionnez « OK ».<br>

6. Une fois l’affectation de rôle terminée, les utilisateurs et/ou les groupes apparaissent dans la liste.<br> ![Liste des utilisateurs (section RBAC d’Azure AD Connect Health)](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Au terme de ces étapes, les utilisateurs et groupes répertoriés bénéficient d’un accès en fonction des rôles qui leur ont été affectés.
>[AZURE.NOTE]
-Les administrateurs généraux disposent toujours d’un accès complet à toutes les opérations, mais les comptes d’administrateurs généraux ne sont pas présents dans la liste ci-dessus. - La fonctionnalité « Inviter des utilisateurs » N’EST PAS prise en charge dans Azure AD Connect Health.

#### Étape 3 : partager l’emplacement du panneau avec des utilisateurs ou des groupes
1. Une fois les autorisations affectées, un utilisateur peut accéder à Azure AD Connect Health à l’adresse [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Une fois sur le panneau, l’utilisateur peut épingler ce dernier ou différentes parties au tableau de bord en cliquant simplement sur « Épingler au tableau de bord ».<br> ![Épingler le panneau (section RBAC d’Azure AD Connect Health)](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un utilisateur qui détient le rôle « Lecteur » ne peut pas effectuer l’opération de création pour obtenir l’extension Azure AD Connect Health à partir d’Azure Marketplace. Cet utilisateur peut toujours accéder au panneau en suivant le lien ci-dessus. Pour une utilisation ultérieure, il peut épingler le panneau au tableau de bord.

### Supprimer des utilisateurs et/ou des groupes
Vous pouvez supprimer un utilisateur ou un groupe de la partie du contrôle d’accès en fonction du rôle d’Azure AD Connect Health en cliquant avec le bouton droit et en sélectionnant Supprimer.<br> ![Supprimer un utilisateur (section RBAC d’Azure AD Connect Health)](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # "Fin de la section RBAC"

## Liens connexes

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l'agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historique de publication des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0224_2016-->