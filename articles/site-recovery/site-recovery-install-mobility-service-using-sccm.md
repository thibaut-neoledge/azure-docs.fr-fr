---
title: "Automatisation de l’installation du service Mobilité pour Azure Site Recovery à l’aide d’outils de déploiement de logiciels | Microsoft Docs"
description: "Cet article vous aide à automatiser l’installation du service Mobilité à l’aide d’outils de déploiement de logiciels tels que System Center Configuration Manager."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ded45356e6dd22b485adfe7f85ddd0abb21280e5
ms.lasthandoff: 04/03/2017

---
# <a name="automate-mobility-service-installation-by-using-software-deployment-tools"></a>Automatisation de l’installation du service Mobilité à l’aide d’outils de déploiement de logiciels

Cet article fournit un exemple de l’utilisation de System Center Configuration Manager pour déployer le service Mobilité Azure Site Recovery dans votre centre de données. L’utilisation d’un outil de déploiement de logiciels tel que Configuration Manager vous offre les avantages suivants :
* Planification du déploiement (nouvelles installations et mises à niveau) pendant votre fenêtre de maintenance planifiée pour les mises à jour logicielles
* Mise à l’échelle du déploiement pour des centaines de serveurs simultanément


> [!NOTE]
> Cet article utilise System Center Configuration Manager 2012 R2 pour illustrer l’activité de déploiement. Vous pouvez également automatiser l’installation du service Mobilité à l’aide [d’Azure Automation et de la Configuration de l’état souhaité](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Composants requis
1. Un outil de déploiement de logiciels tel que Configuration Manager qui est déjà déployé dans votre environnement.
  Créez deux [regroupements d’appareils](https://technet.microsoft.com/library/gg682169.aspx), un pour tous les **serveurs Windows** et l’autre pour tous les **serveurs Linux** que vous souhaitez protéger à l’aide de Site Recovery.
3. Un serveur de configuration qui est déjà inscrit auprès Site Recovery.
4. Un partage de fichiers réseau sécurisé (partage SMB) qui est accessible par le serveur Configuration Manager.

## <a name="deploy-mobility-service-on-computers-running-windows"></a>Déploiement du service Mobilité sur des ordinateurs exécutant Windows
> [!NOTE]
> Cet article part du principe que l’adresse IP du serveur de configuration est 192.168.3.121, et que le partage de fichiers réseau sécurisé est \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Étape 1 : Préparation du déploiement
1. Créez un dossier sur le partage réseau et nommez-le **MobSvcWindows**.
2. Ouvrez une session sur votre serveur de configuration et ouvrez une invite de commandes d’administration.
3. Exécutez les commandes suivantes pour générer un fichier de phrase secrète :

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiez le fichier **MobSvc.passphrase** dans le dossier **MobSvcWindows** sur le partage réseau.
5. Naviguez jusqu’au référentiel du programme d’installation sur le serveur de configuration en exécutant la commande suivante :

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Copiez **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** dans le dossier **MobSvcWindows** sur votre partage réseau.
7. Copiez le code ci-dessous et enregistrez-le en tant que **install.bat** dans le dossier **MobSvcWindows**.

   > [!NOTE]
   > Remplacez les espaces réservés [CSIP] dans le script ci-dessous par les valeurs réelles de l’adresse IP de votre serveur de configuration.

```
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
REM ==================================================
REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================
REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================
REM ==== Extract the installer ======================
CD %Temp%\MobSvc\Extracted
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed run install command ========
REM ==== Else run upgrade command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\temp\logfile.log
REM SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1} >> C:\Temp\logfile.log 2>&1
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
REM IF NOT %ERRORLEVEL% EQU 0 (GOTO :INSTALL) ELSE GOTO :UPDATE
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UPDATE
:INSTALL
    echo "Install" >> c:\Temp\logfile.log
     UnifiedAgent.exe /Role "Agent" /CSEndpoint "10.10.20.168" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
GOTO :ENDSCRIPT
:UPDATE
    echo "Update" >> C:\Temp\logfile.log
    UnifiedAgent.exe /upgrade
:ENDSCRIPT

```

### <a name="step-2-create-a-package"></a>Étape 2 : Création d’un package

1. Connectez-vous à votre console Configuration Manager.
2. Accédez à **Bibliothèque de logiciels** > **Gestion des applications** > **Packages**.
3. Cliquez avec le bouton droit sur **Packages** et sélectionnez **Créer un package**.
4. Fournissez des valeurs pour le Nom, la Description, le Fabricant, la Langue et la Version.
5. Cochez la case **Ce package contient des fichiers sources**.
6. Cliquez sur **Parcourir** et sélectionnez le partage réseau où se trouve le programme d’installation (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. Dans la page **Choisissez le type de programme que vous voulez créer**, sélectionnez **Programme standard**, puis cliquez sur **Suivant**.

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. Dans la page **Spécifier les informations concernant ce programme standard**, renseignez les entrées suivantes, puis cliquez sur **Suivant**. (Les autres entrées peuvent utiliser leurs valeurs par défaut.)
 
  | **Nom du paramètre** | **Valeur** |
  |--|--|
  | Nom | Installer le service Mobilité de Microsoft Azure (Windows) |
  | Ligne de commande | install.bat |
  | Le programme peut s’exécuter | Qu’un utilisateur ait ouvert une session ou non |

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)

9. Dans la page suivante, sélectionnez les systèmes d’exploitation cible. Le service Mobilité ne peut être installé que sur Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2.

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png) 

10. Cliquez deux fois sur **Suivant** pour terminer l’Assistant.


> [!NOTE]
> Le script prend en charge les nouvelles installations des Agents du service Mobilité et les mises à jour sur les agents déjà installés.

### <a name="step-3-deploy-the-package"></a>Étape 3 : Déploiement du package
1. Dans la console Configuration Manager, cliquez avec le bouton droit sur votre package et sélectionnez **Distribuer du contenu**.
  ![Capture d’écran de la console Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Sélectionnez les **[Points de distribution](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** vers lesquels les packages doivent être copiés.
3. Terminez l’Assistant. Le package démarre ensuite la réplication sur les points de distribution spécifiés.
4. Une fois la distribution du package terminée, cliquez avec le bouton droit sur le package et sélectionnez **Déployer**.
  ![Capture d’écran de la console Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Sélectionnez le regroupement d’appareils Windows Server que vous avez créé dans la section des composants requis en tant que regroupement cible du déploiement.

  ![Capture d’écran de l’assistant Déploiement du logiciel](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)

6. Dans la page **Spécifier la destination du contenu**, sélectionnez vos **Points de distribution**.
7. Dans la page **Spécifier un paramètre pour contrôler la manière dont ce logiciel est déployé**, vérifiez que l’objectif est **Requis**.

  ![Capture d’écran de l’assistant Déploiement du logiciel](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. Spécifiez une planification sur la page **Spécifier la planification de ce déploiement**. Pour plus d’informations, consultez [Planification de packages](https://technet.microsoft.com/library/gg682178.aspx).
9. Sur la page **Points de distribution**, configurez les propriétés selon les besoins de votre centre de données. Terminez ensuite l’Assistant.

> [!TIP]
> Pour éviter les redémarrages inutiles, planifiez l’installation du package pendant votre fenêtre de maintenance mensuelle ou votre fenêtre de mises à jour logicielles.

Vous pouvez surveiller la progression du déploiement à l’aide de la console Configuration Manager. Accédez à **Surveillance** > **Déploiements** > *[nom de votre package]*.

  ![Capture d’écran de l’option de Configuration Manager pour contrôler les déploiements](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux"></a>Déploiement du service Mobilité sur des ordinateurs exécutant Linux
> [!NOTE]
> Cet article part du principe que l’adresse IP du serveur de configuration est 192.168.3.121, et que le partage de fichiers réseau sécurisé est \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Étape 1 : Préparation du déploiement
1. Créez un dossier sur le partage réseau et nommez-le **MobSvcLinux**.
2. Ouvrez une session sur votre serveur de configuration et ouvrez une invite de commandes d’administration.
3. Exécutez les commandes suivantes pour générer un fichier de phrase secrète :

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiez le fichier **MobSvc.passphrase** dans le dossier **MobSvcLinux** sur le partage réseau.
5. Naviguez jusqu’au référentiel du programme d’installation sur le serveur de configuration en exécutant la commande :

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Copiez les fichiers suivants dans le dossier **MobSvcLinux** sur le partage réseau :
   * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. Copiez le code suivant et enregistrez-le en tant que **install_linux.sh** dans le dossier **MobSvcLinux**.
   > [!NOTE]
   > Remplacez les espaces réservés [CSIP] dans le script ci-dessous par les valeurs réelles de l’adresse IP de votre serveur de configuration.

```
#!/bin/sh

rm -rf /tmp/MobSvc

mkdir -p /tmp/MobSvc

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
        cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            cp *RHEL7*.tar.gz /tmp/MobSvc
    fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
        echo $OS >> /tmp/MobSvc/sccm.log
        cp *SLES11*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
       cp *UBUNTU*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi
if [ "${OS}" ==  "" ]; then
    exit 1
fi
cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz


if [ -e /usr/local/.vx_version ];
then
    ./install -A u
    echo "Errorcode:$?"
    Error=$?

else
    ./install -t both -a host -R Agent -d /usr/local/ASR -i [CS IP] -p 443 -s y -c https -P MobSvc.passphrase >> /tmp/MobSvc/sccm.log 2>&1 && echo "Install Progress"
    Error=$?
fi
cd /tmp
rm -rf /tm/MobSvc
exit ${Error}
```

### <a name="step-2-create-a-package"></a>Étape 2 : Création d’un package

1. Connectez-vous à votre console Configuration Manager.
2. Accédez à **Bibliothèque de logiciels** > **Gestion des applications** > **Packages**.
3. Cliquez avec le bouton droit sur **Packages** et sélectionnez **Créer un package**.
4. Fournissez des valeurs pour le Nom, la Description, le Fabricant, la Langue et la Version.
5. Cochez la case **Ce package contient des fichiers sources**.
6. Cliquez sur **Parcourir** et sélectionnez le partage réseau où se trouve le programme d’installation (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. Dans la page **Choisissez le type de programme que vous voulez créer**, sélectionnez **Programme standard**, puis cliquez sur **Suivant**.

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. Dans la page **Spécifier les informations concernant ce programme standard**, renseignez les entrées suivantes, puis cliquez sur **Suivant**. (Les autres entrées peuvent utiliser leurs valeurs par défaut.)

    | **Nom du paramètre** | **Valeur** |
  |--|--|
  | Nom | Installer le service Mobilité de Microsoft Azure (Linux) |
  | Ligne de commande | ./install_linux.sh |
  | Le programme peut s’exécuter | Qu’un utilisateur ait ouvert une session ou non |

  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)

9. Sur la page suivante, sélectionnez **Ce programme peut être exécuté sur n'importe quelle plateforme**.
  ![Capture d’écran de l’assistant Création d’un package et d’un programme](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)

10. Cliquez deux fois sur **Suivant** pour terminer l’Assistant. 
 
> [!NOTE]
> Le script prend en charge les nouvelles installations des Agents du service Mobilité et les mises à jour sur les agents déjà installés.

### <a name="step-3-deploy-the-package"></a>Étape 3 : Déploiement du package
1. Dans la console Configuration Manager, cliquez avec le bouton droit sur votre package et sélectionnez **Distribuer du contenu**.
  ![Capture d’écran de la console Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Sélectionnez les **[Points de distribution](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** vers lesquels les packages doivent être copiés.
3. Terminez l’Assistant. Le package démarre ensuite la réplication sur les points de distribution spécifiés.
4. Une fois la distribution du package terminée, cliquez avec le bouton droit sur le package et sélectionnez **Déployer**.
  ![Capture d’écran de la console Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Sélectionnez le regroupement d’appareils Linux Server que vous avez créé dans la section des composants requis en tant que regroupement cible du déploiement.

  ![Capture d’écran de l’assistant Déploiement du logiciel](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)

6. Dans la page **Spécifier la destination du contenu**, sélectionnez vos **Points de distribution**.
7. Dans la page **Spécifier un paramètre pour contrôler la manière dont ce logiciel est déployé**, vérifiez que l’objectif est **Requis**.

  ![Capture d’écran de l’assistant Déploiement du logiciel](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. Spécifiez une planification sur la page **Spécifier la planification de ce déploiement**. Pour plus d’informations, consultez [Planification de packages](https://technet.microsoft.com/library/gg682178.aspx).
9. Sur la page **Points de distribution**, configurez les propriétés selon les besoins de votre centre de données. Terminez ensuite l’Assistant.

Le service Mobilité est installé sur le regroupement d’appareils Linux Server conformément à la planification configurée.

## <a name="other-methods-to-install-mobility-service"></a>Autres méthodes d’installation des services Mobilité
Voici d’autres options pour l’installation du service Mobilité :
* [Installation manuelle à l’aide de l’interface utilisateur graphique](http://aka.ms/mobsvcmanualinstall)
* [Installation manuelle à l’aide de la ligne de commande](http://aka.ms/mobsvcmanualinstallcli)
* [Installation Push à l’aide du serveur de configuration](http://aka.ms/pushinstall)
* [Installation automatisée à l’aide d’Azure Automation et de la Configuration de l’état souhaité](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>Désinstaller le service Mobilité
Vous pouvez créer des packages Configuration Manager pour désinstaller le service Mobilité. Pour ce faire, utilisez le script suivant :

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Étapes suivantes
Vous êtes désormais prêt à [activer la protection](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) de vos machines virtuelles.

