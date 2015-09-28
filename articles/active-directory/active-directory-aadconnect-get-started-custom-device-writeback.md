<properties 
	pageTitle="Activation de l’écriture différée des appareils dans Azure AD Connect" 
	description="Ce document explique comment activer l’écriture différée des appareils à l’aide d’Azure AD Connect" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="msStevenPo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Activation de l’écriture différée des appareils dans Azure AD Connect

La documentation suivante fournit des informations sur l’activation de la fonctionnalité d’écriture différée des appareils dans Azure AD Connect. L’écriture différée des appareils est utilisée dans les scénarios suivants :

Activer l’accès conditionnel basé sur les appareils pour les applications protégées ADFS (2012 R2 ou version ultérieure) (approbations de la partie de confiance).

Cela fournit une sécurité supplémentaire et l’assurance que l’accès aux applications est accordé uniquement aux appareils de confiance. Pour plus d’informations sur l’accès conditionnel, consultez [Gestion des risques avec accès conditionnel](active-directory-conditional-access.md) et [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.Note]Un abonnement à Office 365 ou à Azure AD Premium est nécessaire pour utiliser des appareils enregistrés dans les stratégies d’accès conditionnel du service d’inscription d’appareils Azure Active Directory. Ces stratégies incluent les stratégies appliquées par les services AD FS (Active Directory Federation Services) pour les ressources locales.

## Partie 1 : Préparer Azure AD Connect
Utilisez les étapes suivantes pour préparer l’utilisation de l’écriture différée des appareils.

1.	À partir de l’ordinateur sur lequel est installé Azure AD Connect, lancez PowerShell en mode élevé.

2.	Si le module Active Directory PowerShell n’est PAS installé. Installez-le en utilisant la commande suivante :

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	Avec les informations d’identification d’administrateur d’entreprise, exécutez les commandes suivantes, puis quittez PowerShell.

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Description :



- En cas de non-existence, des conteneurs et objets sont créés et configurés sous CN=Device Registration Configuration,CN=Services,CN=Configuration,<forest-dn>.



- En cas de non-existence, des conteneurs et objets sont créés et configurés sous CN=RegisteredDevices,<domain-dn>. Les objets d’appareil seront créés dans ce conteneur.



- Définit les autorisations nécessaires sur le compte Azure AD Connector pour gérer des appareils sur votre Active Directory.



- Ne doit s’exécuter que sur une seule forêt, même si Azure AD Connect est installé sur plusieurs forêts.

Paramètres :


- DomainName : domaine Active Directory où les objets d’appareil seront créés. Remarque : tous les appareils pour une forêt Active Directory donnée seront créés dans un domaine unique. 


- AdConnectorAccount : compte Active Directory qui servira à Azure AD Connect pour gérer des objets dans le répertoire.

## Partie 2 : Activer l’écriture différée des appareils
Utilisez la procédure suivante pour activer l’écriture différée des appareils dans Azure AD Connect.

1.	Exécutez l’Assistant AAD Connect. S’il s’agit de la première fois que vous utilisez l’Assistant, effectuez une installation personnalisée en sélectionnant Personnaliser dans l’écran Configuration rapide. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	Si ce n’est pas la première fois, sélectionnez Personnalisation des options de synchronisation à partir de la page Tâches supplémentaires et cliquez sur Suivant. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	Sur la page Fonctionnalités facultatives, l’écriture différée des appareils ne sera plus grisée. Notez que si les étapes de préparation d’Azure AD Connect ne sont pas terminées, l’écriture différée des appareils sera grisée sur la page Fonctionnalités facultatives. Cochez la case pour l’écriture différée des appareils et cliquez sur Suivant. ![Écriture différée des appareils](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	Sur la page de l’écriture différée, vous verrez le domaine fourni en tant que forêt d’écriture différée d’appareil par défaut. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	Terminez l’installation de l’Assistant sans autre modification de la configuration. Si nécessaire, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)



## Activer l’accès conditionnel
Des instructions détaillées pour activer ce scénario sont disponibles dans [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Vérifier la synchronisation des appareils avec Active Directory
L’écriture différée des appareils doit désormais fonctionner correctement. Sachez que la réécriture des objets d’appareil dans AD peut prendre jusqu’à 3 heures. Pour vérifier que vos appareils sont correctement synchronisés, procédez comme suit après la fin des règles de synchronisation :
 
1.	Lancez le Centre d’administration Active Directory. 
2.	Développez RegisteredDevices au sein du domaine en cours de fédération. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Les appareils enregistrés actuels sont répertoriés à cet emplacement. 

![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Informations supplémentaires 


- [Gestion des risques avec accès conditionnel](active-directory-conditional-access.md)
- [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

<!---HONumber=Sept15_HO3-->