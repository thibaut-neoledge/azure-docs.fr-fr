<properties
	pageTitle="Activation de l’écriture différée des appareils dans Azure AD Connect | Microsoft Azure"
	description="Ce document explique comment activer l’écriture différée des appareils à l’aide d’Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/12/2015"
	ms.author="billmath;andkjell"/>

# Activation de l’écriture différée des appareils dans Azure AD Connect

La documentation suivante fournit des informations sur l’activation de la fonctionnalité d’écriture différée des appareils dans Azure AD Connect. L’écriture différée des appareils est utilisée dans les scénarios suivants :

Activer l’accès conditionnel basé sur les appareils pour les applications protégées ADFS (2012 R2 ou version ultérieure) (approbations de la partie de confiance).

Cela fournit une sécurité supplémentaire et l’assurance que l’accès aux applications est accordé uniquement aux appareils de confiance. Pour plus d’informations sur l’accès conditionnel, consultez [Gestion des risques avec accès conditionnel](active-directory-conditional-access.md) et [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.NOTE]Un abonnement à Office 365 ou à Azure AD Premium est nécessaire pour utiliser des appareils enregistrés dans les stratégies d’accès conditionnel du service d’inscription d’appareils Azure Active Directory. Ces stratégies incluent les stratégies appliquées par les services AD FS (Active Directory Federation Services) pour les ressources locales.

## 1ère partie : Installer Azure AD Connect
1. Installer Azure AD Connect à l'aide de paramètres personnalisés ou Express. Il est recommandé de commencer par synchroniser correctement tous les utilisateurs et groupes avant d'activer l'écriture différée des appareils.

## Partie 2 : Préparer Active Directory
Utilisez les étapes suivantes pour préparer l’utilisation de l’écriture différée des appareils.

1.	À partir de l’ordinateur sur lequel est installé Azure AD Connect, lancez PowerShell en mode élevé.

2.	Si le module Active Directory PowerShell n’est PAS installé. Installez-le en utilisant la commande suivante :

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	Avec les informations d’identification d’administrateur d’entreprise, exécutez les commandes suivantes, puis quittez PowerShell.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![PowerShell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

Description :

- En cas de non-existence, des conteneurs et objets sont créés et configurés sous CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
- En cas de non-existence, des conteneurs et objets sont créés et configurés sous CN=RegisteredDevices,[domain-dn]. Les objets d’appareil seront créés dans ce conteneur.
- Définit les autorisations nécessaires sur le compte Azure AD Connector pour gérer des appareils sur votre Active Directory.
- Ne doit s’exécuter que sur une seule forêt, même si Azure AD Connect est installé sur plusieurs forêts.

Paramètres :

- DomainName : domaine Active Directory où les objets d’appareil seront créés. Remarque : tous les appareils pour une forêt Active Directory donnée seront créés dans un domaine unique.
- AdConnectorAccount : compte Active Directory qui servira à Azure AD Connect pour gérer des objets dans le répertoire. C'est le compte utilisé par la synchronisation Azure AD Connect pour se connecter à AD. Si vous avez procédé à l'installation à l'aide de paramètres Express, c'est le compte précédé de MSOL\_.

## 3ème partie : Activer l'écriture différée des appareils dans Azure AD Connect
Utilisez la procédure suivante pour activer l’écriture différée des appareils dans Azure AD Connect.

1.	Réexécutez l’Assistant d’installation. Sélectionnez **Personnalisation des options de synchronisation** à partir de la page Tâches supplémentaires et cliquez sur **Suivant**. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	Sur la page Fonctionnalités facultatives, l’écriture différée des appareils ne sera plus grisée. Notez que si les étapes de préparation d’Azure AD Connect ne sont pas terminées, l’écriture différée des appareils sera grisée sur la page Fonctionnalités facultatives. Cochez la case pour l’écriture différée des appareils et cliquez sur **Suivant**. Si la case à cocher est toujours désactivée, consultez la [section Dépannage](#the-writeback-checkbox-is-still-disabled). ![Écriture différée des appareils](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	Sur la page de l’écriture différée, vous verrez le domaine fourni en tant que forêt d’écriture différée d’appareil par défaut. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	Terminez l’installation de l’Assistant sans autre modification de la configuration. Si nécessaire, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)


## Activer l’accès conditionnel
Des instructions détaillées pour activer ce scénario sont disponibles dans [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Vérifier la synchronisation des appareils avec Active Directory
L’écriture différée des appareils doit désormais fonctionner correctement. Sachez que la réécriture des objets d’appareil dans AD peut prendre jusqu’à 3 heures. Pour vérifier que vos appareils sont correctement synchronisés, procédez comme suit après la fin des règles de synchronisation :

1.	Lancez le Centre d’administration Active Directory.
2.	Développez RegisteredDevices au sein du domaine en cours de fédération. ![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Les appareils enregistrés actuels sont répertoriés à cet emplacement.

![Installation personnalisée](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Résolution de problèmes

### La case à cocher de l'écriture différée est toujours désactivée.
Si la case à cocher pour l'écriture différée des appareils n'est pas activée alors que vous avez suivi les étapes ci-dessus, la procédure suivante vous guidera dans ce que l'Assistant d'installation vérifie avant l'activation de la case.

Commençons par le début :

- Assurez-vous qu'au moins une forêt a Windows Server 2012R2. Le type d'objet d'appareil doit être présent.
- Si l'Assistant d'installation est déjà en cours d'exécution, les modifications ne seront pas détectées. Dans ce cas, terminez l'Assistant installation et exécutez-le à nouveau.
- Assurez-vous que le compte que vous fournissez dans le script d'initialisation est bien l'utilisateur utilisé par le connecteur Active Directory. Pour ce faire, procédez comme suit :
	- Dans le menu Démarrer, ouvrez **Service de synchronisation**.
	- Ouvrez l'onglet **Connecteurs** onglet.
	- Trouvez le connecteur de type services de domaine Active Directory et sélectionnez-le.
	- Sous **Actions**, sélectionnez **Propriétés**.
	- Accédez à **se connecter à la forêt Active Directory**. Vérifiez que le nom de domaine et d'utilisateur spécifié sur cet écran correspond au compte fourni pour le script. ![Compte de connecteur](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

Vérifier la configuration dans Active Directory : - Vérifiez que le Service d'inscription de l'appareil se trouve à l'emplacement ci-dessous (CN = DeviceRegistrationService, CN = Services d'inscription de périphérique, CN = Inscription de l'appareil, CN = Services, CN = Configuration) dans le contexte d'appellation de configuration.

![Troubleshoot1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- Vérifiez qu'il n'y a qu'un seul objet de configuration en recherchant l'espace de noms de configuration. S'il en existe plusieurs, supprimez le doublon.

![Troubleshoot2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- Sur l'objet Service d'inscription de l'appareil, assurez-vous que l'attribut msDS-DeviceLocation est présent et a une valeur. Recherchez cet emplacement et assurez-vous qu'il est présent avec l'attribut objectType msDS-DeviceContainer.

![Troubleshoot3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Troubleshoot4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- Vérifiez que le compte utilisé par le connecteur Active Directory dispose des autorisations requise sur le conteneur d’appareils inscrits trouvé à l’étape précédente. Voici les autorisations attendues sur ce conteneur :

![Troubleshoot5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- Vérifiez que le compte Active Directory dispose des autorisations sur CN = Inscription de l’appareil, CN = Services, CN = Objet de Configuration.

![Troubleshoot6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## Informations supplémentaires
- [Gestion des risques avec accès conditionnel](active-directory-conditional-access.md)
- [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO3-->