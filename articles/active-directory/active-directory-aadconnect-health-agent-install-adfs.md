<properties 
	pageTitle="Installation de l’agent Azure AD Connect Health pour AD FS | Microsoft Azure"
	description="Ceci est la page d’Azure AD Connect Health qui décrit l’installation de l’agent Active Directory Federation Services (AD FS)."
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






# Installation de l’agent Azure AD Connect Health pour AD FS

Ce document vous guidera dans l’installation et la configuration de l’agent Azure AD Connect Health pour AD FS sur vos serveurs.

>[AZURE.NOTE]Avant de pouvoir consulter des données sur votre instance d’Azure AD Connect Health, il vous faudra installer l’agent Azure AD Connect Health sur vos serveurs cibles. Assurez-vous de disposer de la configuration requise [ici](active-directory-aadconnect-health.md#requirements) avant d’installer l’agent. Vous pouvez télécharger l’agent [ici](http://go.microsoft.com/fwlink/?LinkID=518973).


## Installation de l’agent
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Sur le premier écran, cliquez sur Installer.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Cette action lance une invite de commandes suivie par certains éléments PowerShell qui exécuteront Register-AzureADConnectHealthADFSAgent. Vous serez invité à vous connecter à Azure. Connectez-vous.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Une fois que vous êtes connecté, PowerShell continue. À l’issue du processus, vous pouvez fermer PowerShell ; la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement. L’agent surveille et collecte désormais les données. Gardez à l’esprit que vous verrez des avertissements s’afficher dans la fenêtre PowerShell si vous n’avez pas satisfait la configuration requise décrite dans les sections précédentes. Assurez-vous de disposer de la configuration requise [ici](active-directory-aadconnect-health.md#requirements) avant d’installer l’agent. La capture d’écran suivante est un exemple de ces erreurs.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Pour vérifier que l’agent a été installé, ouvrez les services et procédez aux vérifications suivantes. Si vous avez terminé la configuration, ces services doivent s’exécuter. Dans le cas contraire, vous devez impérativement terminer la configuration pour exécuter les services.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


## Installation de l’agent sur les serveurs Windows Server 2008 R2

Pour les serveurs Windows Server 2008 R2, procédez comme suit :

1. Assurez-vous que le serveur est exécuté au Service Pack 1 ou une version supérieure.
1. Désactivez la Configuration de sécurité renforcée pour l’installation de l’agent :
1. Installez Windows PowerShell 4.0 sur chacun des serveurs avant d’installer l’agent AD Health. Pour installer Windows PowerShell 4.0 :
 - Installez [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) en cliquant sur le lien suivant pour télécharger le programme d’installation hors connexion.
 - Installer PowerShell ISE (à partir des fonctionnalités de Windows)
 - Installez [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installez Internet Explorer version 10 ou supérieure sur le serveur. Cette installation est requise par le service Health, afin de vous authentifier avec vos informations d’identification d’administrateur Azure.
1. Pour plus d’informations sur l’installation de Windows PowerShell 4.0 sur Windows Server 2008 R2, consultez l’article wiki [ici](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

## Liens connexes

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->