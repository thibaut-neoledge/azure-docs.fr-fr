<properties 
	pageTitle="Activer Microsoft Passport for Work dans l’organisation | Microsoft Azure" 
	description="Instructions de déploiement pour activer Microsoft Passport dans votre organisation." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Activer Microsoft Passport for Work dans l’organisation

Après avoir connecté les appareils du domaine Windows 10 à Azure AD, procédez comme suit pour activer Microsoft Passport for Work dans l’organisation.

## Déployer la version 1509 de System Center Configuration Manager pour l’évaluation technique
Pour déployer des certificats utilisateur en fonction des clés de Microsoft Passport, vous avez besoin des ressources suivantes :

- **Version 1509 de System Center Configuration Manager pour l’évaluation technique**. Pour plus d’informations, consultez [Évaluation technique de Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) et [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infrastructure à clé publique** : pour activer Microsoft Passport for Work à l’aide de certificats utilisateur, vous devez disposer d’une infrastructure à clé publique. Si vous n’en avez pas ou si vous ne souhaitez pas l’utiliser pour les certificats utilisateur, vous pouvez déployer des contrôleurs de domaine de la nouvelle version de Windows Server :
 - **Déployez un contrôleur de domaine de la nouvelle version de Windows Server** : sur un serveur Windows Server version 10551 ou ultérieure (les fichiers ISO sont disponibles en téléchargement sur [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)), suivez les étapes pour [installer un contrôleur de domaine réplica dans un domaine existant](https://technet.microsoft.com/fr-FR/library/jj574134.aspx) ou [installer une nouvelle forêt Active Directory en cas de création d’un nouvel environnement à partir de zéro](https://technet.microsoft.com/fr-FR/library/jj574134.aspx).

## Configurer Microsoft Passport for Work via la stratégie de groupe dans Active Directory

 Vous pouvez utiliser une stratégie de groupe Active Directory pour configurer vos appareils joints à un domaine Windows 10 afin de fournir des données d’identification lors de la connexion à Windows :

1. 	Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2.	Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine où vous voulez activer Rejoindre Azure AD.
3.	Cliquez avec le bouton droit sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Nommez votre objet de stratégie de groupe, par exemple **Activer Microsoft Passport**. Cliquez sur **OK**.
4.	Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5.	Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Passport for Work**.
6.	Cliquez avec le bouton droit sur **Activer Passport for Work**, puis sélectionnez **Modifier**.
7.	Sélectionnez la case d’option **Activé**, puis cliquez sur **Appliquer**. Cliquez sur **OK**.
8.	Vous pouvez maintenant lier l’objet de stratégie de groupe à un emplacement de votre choix. Pour activer cette stratégie pour tous les appareils Windows 10 joints au domaine de votre organisation, liez la stratégie de groupe au domaine. Par exemple :
 - Une unité d’organisation spécifique (UO) dans AD où les ordinateurs Windows 10 joints au domaine seront situés.
 - Un groupe de sécurité spécifique contenant des ordinateurs Windows 10 joints au domaine, qui sera enregistré automatiquement avec Azure AD.

## Configurer Microsoft Passport for Work en déployant PowerShell à partir de Configuration Manager 

Exécutez la commande PowerShell suivante :

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configurer le profil de certificat pour inscrire le certificat « Passport for Work » dans Configuration Manager
Pour utiliser la connexion basée sur le certificat Password for Work/Microsoft Hello, configurez le profil de certificat (Ressources et Conformité -> Paramètres de compatibilité -> Accès aux ressources de l’entreprise -> Profils de certificat) en sélectionnant un modèle d’ouverture de session de carte à puce.

##Configurer une tâche planifiée, qui est déclenchée lorsque le conteneur Passport for Work est activé et demande une évaluation de certificat
Il s’agit d’une solution à court terme dans laquelle les administrateurs doivent créer une tâche planifiée qui attend la création d’un conteneur Passport for Work et qui demande l’évaluation du certificat. Cela réduit le délai de configuration du conteneur et du code confidentiel et sa capacité à être utilisé lors de la prochaine ouverture de session.

**Pour créer la tâche planifiée, utilisez la commande ci-dessous ou utilisez l’IU** schtasks /create /xml %0..<EnrollCertificate.xml> /tn <Task Name>

Voici un exemple de code XML :

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

Dans lequel ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd est l’identificateur du profil de certificat créé à l’étape de configuration du profil de certificat pour inscrire Passport for Work comme certificat dans Configuration manager et dans lequel <8> est la version.

## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->