<properties
	pageTitle="Activer Microsoft Passport for Work dans votre organisation | Microsoft Azure"
	description="Instructions de déploiement pour activer Microsoft Passport dans votre organisation."
	services="active-directory"
	documentationCenter=""
	keywords="configurer Microsoft Passport, déploiement de Microsoft Passport for Work"
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>


# Activer Microsoft Passport for Work dans votre organisation

Après avoir connecté les appareils du domaine Windows 10 à Azure Active Directory (Azure AD), procédez comme suit pour activer Microsoft Passport for Work dans votre organisation.

## Déployer la version 1509 de System Center Configuration Manager pour l’évaluation technique
Pour déployer des certificats utilisateur en fonction des clés de Microsoft Passport, vous avez besoin des ressources suivantes :

- **Version 1509 de System Center Configuration Manager pour l’évaluation technique** Pour plus d’informations, consultez [Évaluation technique de Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) et [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infrastructure à clé publique (PKI)** : pour activer Microsoft Passport for Work à l’aide de certificats utilisateur, vous devez disposer d’une infrastructure PKI. Si vous n’en avez pas, ou si vous ne souhaitez pas l’utiliser pour les certificats utilisateur, vous pouvez procéder comme suit :
 - **Déployer un contrôleur de domaine** : déployez un contrôleur de domaine qui dispose de Windows Server 2016 build 10551 (ou version ultérieure) et suivez les étapes pour [installer un contrôleur de domaine réplica dans un domaine existant](https://technet.microsoft.com/library/jj574134.aspx) ou [installer une nouvelle forêt Active Directory, si vous créez un nouvel environnement](https://technet.microsoft.com/library/jj574166). (Les fichiers ISO sont disponibles en téléchargement sur [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## Configurer Microsoft Passport for Work via la stratégie de groupe dans Active Directory

 Vous pouvez utiliser la stratégie de groupe Windows Server Active Directory pour configurer vos appareils joints à un domaine Windows 10 afin de fournir les données d’identification utilisateur de Microsoft Passport lors de la connexion à Windows :

1. 	Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2.	Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine dans lequel vous voulez activer Rejoindre Azure AD.
3.	Cliquez avec le bouton droit sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Donnez un nom à votre objet de stratégie de groupe, par exemple Activer Microsoft Passport. Cliquez sur **OK**.
4.	Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5.	Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Passport for Work**.
6.	Cliquez avec le bouton droit sur **Activer Passport for Work**, puis sélectionnez **Modifier**.
7.	Sélectionnez la case d’option **Activé**, puis cliquez sur **Appliquer**. Cliquez sur **OK**.
8.	Vous pouvez maintenant lier l’objet de stratégie de groupe à l’emplacement de votre choix. Pour activer cette stratégie pour tous les appareils joints à un domaine Windows 10 de votre organisation, liez la stratégie de groupe au domaine. Par exemple :
 - Une unité d’organisation spécifique dans Active Directory où les ordinateurs Windows 10 joints au domaine seront situés
 - Un groupe de sécurité spécifique contenant des ordinateurs Windows 10 joints au domaine qui sera inscrit automatiquement auprès d’Azure AD

## Configurer Microsoft Passport for Work avec PowerShell à partir de Configuration Manager

Exécutez la commande PowerShell suivante :

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configurer le profil de certificat pour utiliser le certificat « Passport for Work » dans Configuration Manager
Pour utiliser la connexion basée sur le certificat Password for Work/Microsoft Hello, configurez le profil de certificat (**Ressources et Conformité** -> **Paramètres de compatibilité** -> **Accès aux ressources de l’entreprise** -> **Profils de certificat**). Sélectionnez un modèle qui dispose d’une ouverture de session avec carte à puce EKU.

## Configurez une tâche planifiée de demande d’évaluation du certificat
Cette tâche planifiée est une solution à court terme. Les administrateurs doivent créer une tâche planifiée qui attend la création d’un conteneur Passport for Work, puis demande l’évaluation du certificat. La tâche planifiée est déclenchée lorsque le conteneur Passport for Work est activé. Cette tâche réduit le temps de configuration du conteneur et du code confidentiel et les met à disposition plus rapidement pour une utilisation lors de la prochaine connexion.

**Pour créer la tâche planifiée, vous pouvez utiliser l’interface utilisateur ou utiliser la commande suivante :**

    schtasks /create /xml %0\..<EnrollCertificate.xml> /tn <Task Name>

Voici l’exemple de code XML :

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

 Scope ID, ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd est l’identificateur du profil de certificat créé à l’étape Configurer le profil de certificat pour utiliser le certificat « Passport for Work » dans Configuration Manager. Le numéro de version, 8, suit cet ID.

## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0629_2016-->