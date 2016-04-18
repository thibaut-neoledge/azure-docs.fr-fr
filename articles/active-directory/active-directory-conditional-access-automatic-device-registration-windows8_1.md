<properties
	pageTitle="Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1| Microsoft Azure"
	description="Étapes pour configurer une stratégie de groupe pour que les appareils joints à un domaine Windows 8.1 s’inscrivent automatiquement auprès d’Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="femila"/>

# Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1.

Vous pouvez utiliser une stratégie de groupe Active Directory pour configurer vos appareils joints à un domaine Windows 8.1 pour qu’ils s’inscrivent automatiquement auprès d’Azure AD. Pour configurer la stratégie de groupe, vous devez disposer d'au moins un ordinateur Windows Server 2012 R2 ou Windows 8.1 joint à un domaine et doté de la fonctionnalité de gestion des stratégies de groupe. Une fois la stratégie de groupe activée pour votre domaine, tout utilisateur du domaine qui se connecte à l’ordinateur est automatiquement inscrit en mode silencieux auprès d’un objet d’appareil dans Azure AD. Il y a un objet d’appareil dans Azure AD pour chaque utilisateur inscrit de l’appareil physique. Lisez et veillez à satisfaire les conditions requises pour l’inscription automatique des appareils auprès d’Azure Active Directory pour les appareils joints à un domaine Windows.

## Configurer la stratégie de groupe pour les appareils joints à un domaine Windows 8.1

1. Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2. Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine où vous voulez activer **Jonction automatique de l’espace de travail**.
3. Cliquez avec le bouton droit sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Nommez votre objet de stratégie de groupe, par exemple **Jonction automatique de l’espace de travail**. Cliquez sur **OK**.
4. Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5. Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Jonction d’espace de travail**.
6. Cliquez avec le bouton droit sur Joindre automatiquement les ordinateurs clients à l’espace de travail et sélectionnez **Modifier**.
7. Sélectionnez la case d’option Activé, puis cliquez sur Appliquer. Cliquez sur **OK**.
8. Vous pouvez maintenant lier l’objet de stratégie de groupe à un emplacement de votre choix. Pour activer cette stratégie pour tous les appareils joints à un domaine Windows 8.1 de votre organisation, liez la stratégie de groupe au domaine.

## Annuler l’inscription d’appareils joints à votre domaine Windows 8.1

Vous pouvez choisir d’annuler l’inscription des appareils joints à votre domaine Windows 8.1 de la façon suivante : modifiez les paramètres de stratégie de groupe de jonction d’espace de travail créés dans la section précédente. Définissez la stratégie Joindre automatiquement les ordinateurs clients à l’espace de travail sur Désactivée. Ceci empêchera de nouveaux appareils de joindre automatiquement l’espace de travail.

Annulez l’inscription des ordinateurs existants joints à un domaine Windows 8.1 selon une des deux options suivantes :

* Option 1 : **Désinscrire un appareil joint à un domaine Windows 8.1 via les paramètres du PC**
  1. Sur l’appareil Windows 8.1, accédez à **Paramètres du PC** > **Réseau** > **Espace de travail**
  2. Sélectionnez **Quitter**. Ce processus doit être répété pour chaque utilisateur du domaine qui s’est connecté à l’ordinateur et a été joint automatiquement à l’espace de travail.

* Option 2 : annuler l’inscription d’un appareil joint à un domaine Windows 8.1 à l’aide d’un script
  	1. Ouvrez une invite de commande sur l’ordinateur Windows 8.1 et exécutez la commande suivante : ` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Cette commande doit être exécutée dans le contexte de chaque utilisateur du domaine qui s’est connecté à l’ordinateur.

##Observateur d’événements et erreurs pour les appareils joints à un domaine Windows 8.1

Le journal des événements Windows sur un ordinateur Windows 8.1 affiche des messages liés à l’inscription de l’appareil. Vous pouvez y trouver des messages pour les événements qui ont réussi et pour ceux qui ont échoué.

Le journal des événements se trouve dans l’Observateur d’événements, sous Services et Applications **Journaux** > **Microsoft** > **Windows > Jonction d’espace de travail**.

##Informations supplémentaires

La stratégie de groupe autorise une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur et qui est déclenchée à la connexion de l’utilisateur. La tâche inscrit l’utilisateur et l’appareil auprès d’Azure AD en mode silencieux une fois que le processus de connexion est terminé. La tâche planifiée se trouve sur les appareils Windows 8.1 dans la bibliothèque du Planificateur de tâches, sous **Microsoft** > **Windows** > **Jonction d’espace de travail**. La tâche s’exécute et inscrit tous les utilisateurs Active Directory qui s’y connectent.

## Rubriques supplémentaires
- [Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)
- [Inscription automatique auprès d’Azure Active Directory d’appareils Windows 10 joints à un domaine](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!---HONumber=AcomDC_0406_2016-->