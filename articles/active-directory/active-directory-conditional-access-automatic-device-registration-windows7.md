<properties pageTitle="# Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7 | Microsoft Azure" description="Procédures pour configurer vos appareils joints à un domaine Windows 7 pour qu’ils s’inscrivent automatiquement auprès d’Azure AD et procédures de déploiement du package logiciel d’inscription d’appareil sur vos appareils joints à un domaine Windows 7 à l’aide d’un système de distribution de logiciels comme System Center Configuration Manager." services="active-directory" documentationCenter="" authors="femila" manager="swadhwa editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7

En tant qu’administrateur, vous pouvez configurer vos appareils joints à un domaine Windows 7 pour qu’ils s’inscrivent automatiquement auprès d’Azure AD. Pour cela, vous devez déployer le package logiciel d’inscription d’appareils sur les appareils joints à un domaine Windows 7 à l’aide d’un système de distribution de logiciels comme System Center Configuration Manager. Veillez à lire et à satisfaire aux conditions requises répertoriées dans l’inscription automatique des appareils auprès d’Azure Active Directory pour les appareils joints à un domaine Windows.

##Installation du package logiciel d’inscription des appareils sur des appareils joints à un domaine Windows 7

L’inscription des appareils pour Windows 7 est disponible sous la forme d’un [package MSI téléchargeable](https://connect.microsoft.com/site1164). Le package doit être installé sur les ordinateurs Windows 7 joints à un domaine Active Directory. Vous devez déployer le package à l’aide d’un système de distribution de logiciels comme System Center Configuration Manager. Le package MSI prend en charge les options d’installation en mode silencieux standard avec le paramètre /quiet. Le package logiciel est disponible en téléchargement sur le [site web Microsoft Connect](https://connect.microsoft.com/site1164). Vous pouvez sélectionner puis téléchargez ici Jonction d’espace de travail pour Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## Jonction d’espace de travail avec Azure Active Directory
L’inscription d’appareils pour les appareils joints à un domaine Windows 7 ne requiert pas et n’inclut pas d’interface utilisateur. Une fois installé sur l’ordinateur, tout utilisateur du domaine qui se connecte à l’ordinateur est automatiquement inscrit en mode silencieux auprès d’un objet d’appareil dans Azure AD. Il y a un objet d’appareil dans Azure AD pour chaque utilisateur de l’appareil physique inscrit.

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur et qui est déclenchée à la connexion de l’utilisateur. La tâche inscrit l’utilisateur et l’appareil auprès d’Azure AD en mode silencieux une fois que le processus de connexion de l’utilisateur est terminé. La tâche planifiée se trouve dans la bibliothèque du Planificateur de tâches, sous **Microsoft** > **Jonction d’espace de travail**. La tâche s’exécute et inscrit tous les utilisateurs Active Directory qui se connectent à l’ordinateur. L’illustration suivante montre le processus pas à pas pour l’inscription automatique des appareils.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un utilisateur (travailleur de l’information) ouvre une session sur un ordinateur client Windows 7 à l’aide des informations d’identification du domaine Active Directory.
1. La tâche planifiée Jonction d’espace de travail est exécutée.
1. L’utilisateur est authentifié en mode silencieux avec AD FS à l’aide de l’authentification Windows intégrée.
1. Le PC Windows 7 est inscrit pour l’utilisateur dans Azure AD.
1. Un objet et un certificat d’appareil sont créés dans Azure AD. L’objet représente le user@device.
1. Le certificat de jonction d’espace de travail est stocké sur l’ordinateur.

## Annuler l’inscription d’appareils joints à votre domaine Windows 7

Vous pouvez choisir d’annuler l’inscription des appareils joints à votre domaine Windows 7 de la façon suivante : désinstallez le package logiciel Jonction d’espace de travail sur les appareils joints à votre domaine Windows 7 en utilisant un système de distribution de logiciels comme System Center Configuration Manager.

Ouvrez ensuite une invite de commandes sur l’ordinateur Windows 7 et exécutez la commande suivante pour désinscrire l’appareil :

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
Cette commande doit être exécutée dans le contexte de chaque utilisateur du domaine qui s’est connecté à l’ordinateur. Observateur d’événements et erreurs pour les appareils joints à un domaine Windows 7

Le journal des événements Windows sur l’ordinateur Windows 7 affiche des messages liés à la jonction d’espace de travail. Vous pouvez y trouver des messages pour les événements de jonction d’espace de travail qui ont réussi et pour ceux qui ont échoué. Le journal des événements se trouve dans l’Observateur d’événements, sous Journaux des services et Applications > Microsoft > Jonction d’espace de travail.

## Rubriques supplémentaires

- [Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)
- [Inscription automatique d’appareils auprès d’Azure Active Directory pour les appareils joints à un domaine Windows](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1.](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Inscription automatique auprès d’Azure Active Directory d’appareils Windows 10 joints à un domaine](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0629_2016-->