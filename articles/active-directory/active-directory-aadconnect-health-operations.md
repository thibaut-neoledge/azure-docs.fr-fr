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
	ms.date="08/14/2015"
	ms.author="billmath"/>

# Opérations Azure AD Connect Health

La rubrique suivante décrit les différentes opérations pouvant être effectuées à l’aide d’Azure AD Connect Health.

## Activer les notifications par courrier électronique
Vous pouvez configurer le service Azure AD Connect Health pour qu’il envoie des notifications par courrier électronique quand des alertes sont générées pour indiquer que votre infrastructure d’identité présente un défaut d’intégrité. Cela se produit lorsqu’une alerte est générée, et quand elle est marquée comme résolue. Pour configurer les notifications par courrier électronique, suivez les instructions ci-dessous. Notez que les notifications par courrier électronique sont désactivées par défaut.


### Pour activer les notifications par courrier électronique Azure AD Connect Health

1. Ouvrez le panneau Alertes pour le service pour lequel vous souhaitez recevoir des notifications par courrier électronique.
2. Dans la barre d’actions, cliquez sur le bouton Paramètres de Notification.
3. Activez les notifications par courrier électronique.
4. Cochez la case pour configurer les notifications par courrier électronique pour l’ensemble des administrateurs généraux.
5. Si vous souhaitez recevoir des notifications par courrier électronique sur d’autres adresses, indiquez-les dans la zone Destinataire d’e-mail supplémentaire. Pour supprimer une adresse de messagerie de cette liste, cliquez avec le bouton droit sur l’entrée, puis sélectionnez Supprimer.
6. Pour valider les modifications, cliquez sur Enregistrer. Les modifications prendront effet une fois que vous avez cliqué sur Enregistrer.






## Supprimer un serveur du service Azure AD Connect Health

Dans certains cas, vous pouvez envisager de retirer un serveur de la surveillance. Pour supprimer un serveur du service Azure AD Connect Health, suivez les instructions ci-dessous.

Lorsque vous supprimez un serveur, tenez compte des points suivants :

- Cette action INTERROMPT la collecte des données de ce serveur. Ce serveur sera retiré du service de surveillance. Après l’exécution de cette action, vous n’aurez plus accès aux nouvelles alertes,ni aux données de surveillance et d’analyse de l’utilisation pour ce serveur.
- Cette action NE SUPPRIMERA NI NE DÉSINSTALLERA l’agent Health du serveur. Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent.
- Cette action ne supprimera PAS les données déjà collectées à partir de ce serveur. Ces données seront supprimées conformément à la politique de conservation des données Microsoft Azure.
- Après avoir effectué cette opération, si vous souhaitez commencer à surveiller de nouveau le même serveur, il vous faudra désinstaller puis réinstaller l’agent Health sur ce serveur.


### Pour supprimer un serveur du service Azure AD Connect Health

1. Sélectionnez le nom du serveur à supprimer afin d’ouvrir le panneau Serveur du panneau Liste des services.
2. Sur le panneau Serveur, cliquez sur le bouton Supprimer de la barre d’actions.
3. Pour valider la suppression, saisissez le nom du serveur dans la zone de confirmation.
4. Cliquez sur le bouton Supprimer.







## Supprimer une instance du service Azure AD Connect Health

Dans certains cas, vous pouvez envisager de supprimer une instance de service. Pour supprimer une instance du service Azure AD Connect Health, suivez les instructions ci-dessous.

Lorsque vous supprimez une instance de service, tenez compte des éléments suivants :

- Cette action va supprimer l’instance de service actuelle du service de surveillance.
- Cette action NE SUPPRIMERA NI NE DÉSINSTALLERA l’agent Health des serveurs surveillés avec cette instance de service. Si vous n’avez pas désinstallé l’agent Health avant d’exécuter cette étape, il est possible que des événements d’erreur s’affichent sur le(s) serveur(s) associé(s) à l’agent.
- Toutes les données de cette instance de service seront supprimées, conformément à la stratégie de conservation des données Microsoft Azure.
- Après avoir effectué cette opération, si vous souhaitez commencer à surveiller le service, désinstallez puis réinstallez l’agent Health de l’ensemble des serveurs à surveiller. Après avoir effectué cette opération, si vous souhaitez commencer à surveiller de nouveau le même serveur, il vous faudra désinstaller puis réinstaller l’agent Health sur ce serveur.


### Pour supprimer une instance du service Azure AD Connect Health

1. À partir du panneau Liste des services, ouvrez le panneau Service en sélectionnant l’identificateur de service (nom de batterie) que vous souhaitez supprimer.
2. Sur le panneau Serveur, cliquez sur le bouton Supprimer de la barre d’actions.
3. Pour valider le nom du service, saisissez-le dans la zone de confirmation (par exemple : sts.contoso.com).
4. Cliquez sur le bouton Supprimer.

## Activer l’audit pour AD FS

Pour que la fonctionnalité d’analyse de l’utilisation puisse collecter les données et les analyser, l’agent Azure AD Connect Health doit avoir les informations à disposition dans les journaux d’audit AD FS. Ces journaux ne sont pas activés par défaut. Cette règle s’applique uniquement aux serveurs de fédération AD FS. Il est inutile d’activer l’audit sur les serveurs proxy AD FS ou sur les serveurs proxy d’application web. Utilisez les procédures suivantes pour activer l’audit AD FS et localiser les journaux d’audit AD FS.

#### Pour activer l’audit pour AD FS 2.0

1. Cliquez sur **Démarrer**, pointez sur **Programmes**, pointez sur **Outils d’administration**, puis cliquez sur **Stratégie de sécurité locale**.
2. Accédez au dossier **Security Settings\\Local Policies\\User Rights Management**, puis double-cliquez sur Générer des audits de sécurité.
3. Sous l’onglet **Paramètre de sécurité locale**, vérifiez que le compte de service AD FS 2.0 est répertorié. S’il n’est pas présent, cliquez sur **Ajouter un utilisateur ou un groupe** et ajoutez-le à la liste, puis cliquez sur **OK**.
4. Ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante pour activer l’audit.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Fermez Stratégie de sécurité locale, puis ouvrez le composant logiciel enfichable Gestion. Pour ouvrir le composant logiciel enfichable Gestion, cliquez sur **Démarrer**, pointez sur **Programmes**, pointez sur **Outils d’administration**, puis cliquez sur Gestion AD FS 2.0.
6. Dans le volet Actions, cliquez sur Modifier les propriétés du service FS (Federation Service).
7. Dans la boîte de dialogue **Propriétés du service de fédération**, cliquez sur l’onglet **Événements**.
8. Cochez les cases **Audits des succès** et **Audits des échecs**.
9. Cliquez sur **OK**.

#### Pour activer l’audit pour AD FS sur Windows Server 2012 R2

1. Ouvrez **Stratégie de sécurité locale** en ouvrant **Gestionnaire de serveur** sur l’écran d’accueil, ou Gestionnaire de serveur dans la barre des tâches sur le bureau, puis cliquez sur **Outils/Stratégie de sécurité locale**.
2. Accédez au dossier **Security Settings\\Local Policies\\User Rights Assignment**, puis double-cliquez sur **Générer des audits de sécurité**.
3. Sous l’onglet **Paramètre de sécurité locale**, vérifiez que le compte de service AD FS est répertorié. S’il n’est pas présent, cliquez sur **Ajouter un utilisateur ou un groupe** et ajoutez-le à la liste, puis cliquez sur **OK**.
4. Ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante pour activer l’audit : <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Fermez **Stratégie de sécurité locale**, puis ouvrez le composant logiciel enfichable **Gestion AD FS** (dans Gestionnaire de serveur, cliquez sur Outils, puis sélectionnez Gestion AD FS).
6. Dans le volet Actions, cliquez sur **Modifier les propriétés du service de fédération**.
7. Dans la boîte de dialogue Propriétés du service de fédération, cliquez sur l’onglet **Événements**.
8. Cochez les cases **Audits des succès et Audits des échecs**, puis cliquez sur **OK**.






#### Pour localiser les journaux d’audit AD FS


1. Ouvrez l’**Observateur d’événements**.
2. Accédez à Journaux Windows et sélectionnez **Sécurité**.
3. Sur la droite, cliquez sur **Filtrer les journaux actuels**.
4. Dans Source de l’événement, sélectionnez **Audit AD FS**.

![Journaux d’audit AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Si vous disposez d’une stratégie de groupe qui désactive l’audit AD FS, l’agent Azure AD Connect Health ne pourra pas collecter les informations. Assurez-vous de ne pas disposer de stratégie de groupe susceptible de désactiver l’audit.


## Liens connexes

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation de l’agent Azure AD Connect Health pour AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=Oct15_HO2-->