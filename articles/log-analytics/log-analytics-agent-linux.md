---
title: "Connecter des ordinateurs Linux à Operations Management Suite (OMS) | Microsoft Docs"
description: "Cet article explique comment connecter des ordinateurs Linux hébergés dans Azure, d’autres cloud ou locaux à OMS à l’aide de l’agent OMS pour Linux."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/05/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 17b451b1fc91cf9fdc895ad28f2c455af5d28b07
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---

# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>Connecter des ordinateurs Linux à Operations Management Suite (OMS) 

Avec Microsoft Operations Management Suite (OMS), vous pouvez collecter et agir sur les données générées à partir d’ordinateurs Linux et de solutions de conteneur telles que Docker, résidant dans votre centre de données local en tant que serveurs physiques ou virtuels, de machines virtuelles dans un service hébergé dans le cloud comme Amazon Web Services (AWS), ou de Microsoft Azure. Vous pouvez également utiliser des solutions de gestion disponibles dans OMS telles que Change Tracking pour identifier les modifications de configuration, et Update Management pour administrer les mises à jour logicielles afin de gérer de manière proactive le cycle de vie de vos machines virtuelles Linux. 

L’agent OMS pour Linux communique en sortie avec le service OMS via le port TCP 443 ; si l’ordinateur se connecte à un pare-feu ou à un serveur proxy pour communiquer sur Internet, consultez la page [Configurer l’agent en vue d’une utilisation avec un serveur proxy HTTP ou une passerelle OMS](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway) pour connaître les modifications de configuration à appliquer.  Si vous surveillez l’ordinateur avec System Center 2016 - Operations Manager ou Operations Manager 2012 R2, il peut être multihébergé avec le service OMS pour collecter des données et les transférer au service, et toujours être surveillé par Operations Manager.  Les ordinateurs Linux analysés par un groupe d’administration Operations Manager intégré à OMS ne reçoivent pas de configuration des sources de données et transmettent les données collectées via le groupe d’administration.  L’agent OMS ne peut pas être configuré pour envoyer des rapports à plus d’un espace de travail.  

Si vos stratégies de sécurité n’autorisent pas les ordinateurs sur votre réseau à se connecter à Internet, l’agent peut être configuré pour se connecter à la passerelle OMS afin de recevoir des informations de configuration et d’envoyer les données collectées en fonction de la solution que vous avez activée. Pour plus d’informations et pour savoir comment configurer votre agent Linux OMS pour communiquer via une passerelle OMS avec le service OMS, consultez [Connecter des ordinateurs à OMS en utilisant la passerelle OMS](log-analytics-oms-gateway.md).  

Le diagramme suivant représente la connexion entre les ordinateurs Linux gérés par agent et OMS, notamment la direction et les ports.

![diagramme de communication directe entre l’agent et OMS](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Conditions requises pour le système
Avant de commencer, prenez connaissance des informations suivantes pour vérifier que les conditions préalables sont remplies.

### <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge
Les distributions Linux suivantes sont officiellement prises en charge.  Toutefois, l’Agent OMS pour Linux peut également s’exécuter dans d’autres distributions non répertoriées.

* Amazon Linux 2012.09 à 2015.09 (x86/x64)
* CentOS Linux 5, 6 et 7 (x86/x64)
* Oracle Linux 5, 6 et 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 et 7 (x86/x64)
* Debian GNU/Linux 6, 7 et 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 et 12 (x86/x64)

### <a name="network"></a>Réseau
Voici la liste des informations de configuration du proxy et du pare-feu requises pour permettre à l’agent Linux de communiquer avec OMS. Le trafic va de votre réseau vers le service OMS. 

|Ressource de l'agent| Ports |  
|------|---------|  
|*.ods.opinsights.azure.com | Port 443|   
|*.oms.opinsights.azure.com | Port 443|   
|*.blob.core.windows.net/ | Port 443|   
|*.azure-automation.net | Port 443|  

### <a name="package-requirements"></a>Packages requis

 **Package requis**   | **Description**   | **Version minimum**
--------------------- | --------------------- | -------------------
Glibc | Bibliothèque C de GNU   | 2.5-12 
Openssl | Bibliothèques OpenSSL | 0.9.8e ou 1.0
Curl | Client web cURL | 7.15.5
Python-ctypes | | 
PAM | Pluggable Authentication Modules | 

> [!NOTE]
>  rsyslog ou syslog-ng est requis pour collecter les messages syslog. Le démon syslog par défaut sur la version 5 de Red Hat Enterprise Linux, CentOS et Oracle Linux (sysklog) ne prend pas en charge la collecte des événements syslog. Pour collecter les données syslog avec cette version de ces distributions, le démon rsyslog doit être installé et configuré à la place de sysklog, 

L’agent inclut plusieurs packages. Les packages suivants sont disponibles en exécutant l’application shell avec `--extract` :

**Package** | **Version** | **Description**
----------- | ----------- | --------------
omsagent | 1.4.0 | Agent Operations Management Suite pour Linux
omsconfig | 1.1.1 | Agent de configuration de l’Agent OMS
omi | 1.2.0 | Open Management Infrastructure (OMI) - Serveur CIM léger
scx | 1.6.3 | Fournisseurs CIM OMI pour les mesures de performances des systèmes d’exploitation
apache-cimprov | 1.0.1 | Fournisseur de surveillance des performances d’Apache HTTP Server pour OMI. Installé si Apache HTTP Server est détecté.
mysql-cimprov | 1.0.1 | Fournisseur de surveillance des performances de MySQL Server pour OMI. Installé si MySQL/MariaDB Server est détecté.
docker-cimprov | 1.0.0 | Fournisseur de Docker pour OMI. Installé si Docker est détecté.

### <a name="compatibility-with-system-center-operations-manager"></a>Compatibilité avec System Center Operations Manager
L’Agent OMS pour Linux partage ses fichiers binaires avec l’agent System Center Operations Manager. L’installation de l’Agent OMS pour Linux sur un système géré par Operations Manager met à niveau les packages OMI et SCX présents sur l’ordinateur. Dans cette version, les agents OMS et System Center 2016 - Operations Manager/Operations Manager 2012 R2 pour Linux sont compatibles. 

> [!NOTE]
> System Center 2012 SP1 et ses versions antérieures ne sont actuellement pas compatibles avec l’Agent OMS pour Linux.<br>
> Si l’Agent OMS pour Linux est installé sur un ordinateur non surveillé par Operations Manager, et que vous décidez ensuite de surveiller l’ordinateur avec Operations Manager, vous devez modifier la [configuration d’OMI](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) avant de détecter l’ordinateur. **Cette étape n’est *pas* nécessaire si l’agent Operations Manager est installé avant l’Agent OMS pour Linux.**

### <a name="system-configuration-changes"></a>Modifications de configuration système
Après avoir installé les packages de l’Agent OMS pour Linux, les modifications de configuration système suivantes sont appliquées. Ces artefacts sont supprimés lorsque le package omsagent est désinstallé.

* Un utilisateur sans privilège nommé `omsagent` est créé. Il s’agit du compte utilisé par le démon omsagent pour s’exécuter.
* Un fichier « include » de sudoers est créé à l’emplacement /etc/sudoers.d/omsagent. Cela autorise omsagent à redémarrer les démons syslog et omsagent. Si les directives « include » sudo ne sont pas prises en charge dans la version installée de sudo, ces entrées sont inscrites dans /etc/sudoers.
* La configuration de syslog est modifiée pour transférer un sous-ensemble d’événements à l’agent. Pour plus d’informations, consultez la section **Configuration de la collecte des données** ci-dessous.

### <a name="upgrade-from-a-previous-release"></a>Mettre à niveau à partir d’une version antérieure
La mise à niveau à partir de versions antérieures à 1.0.0-47 est prise en charge dans cette version. Effectuer l’installation avec la commande `--upgrade` met à niveau tous les composants de l’agent vers la dernière version.

## <a name="installing-the-agent"></a>Installation de l’agent

Cette section décrit comment installer l’Agent OMS pour Linux à l’aide d’un groupe, qui contient des packages Debian et RPM pour chacun des composants de l’agent.  Il peut être installé directement ou extrait pour récupérer les packages individuels.  

Tout d’abord, vous avez besoin de l’ID et de la clé de votre espace de travail OMS, qui se trouvent dans le [portail classique OMS](https://mms.microsoft.com).  Dans la page **Vue d’ensemble**, dans le menu supérieur, sélectionnez **Paramètres**, puis accédez à **Sources connectées\Serveurs Linux**.  Les valeurs sont situées à droite de **Clé primaire** et **ID de l’espace de travail**.  Copiez-collez ces deux valeurs dans votre éditeur favori.    

1. Téléchargez la dernière version [Agent OMS pour Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) ou [Agent OMS pour Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) à partir de GitHub.  
2. Transférez le groupe approprié (x86 ou x64) sur votre ordinateur Linux à l’aide de scp/sftp.
3. Installez le groupe à l’aide de l’argument `--install` ou `--upgrade`. 

    > [!NOTE]
    > Si des packages existants sont installés, par exemple lorsque l’agent System Center Operations Manager pour Linux est déjà installé, utilisez l’argument `--upgrade`. Pour vous connecter à Operations Management Suite pendant l’installation, vous devez fournir les paramètres `-w <WorkspaceID>` et `-s <Shared Key>`.


#### <a name="to-install-and-onboard-directly"></a>Pour installer et intégrer directement
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Pour mettre à niveau le package de l’agent
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Pour installer et intégrer dans un espace de travail du cloud du gouvernement des États-Unis
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway"></a>Configuration de l’agent pour une utilisation avec un serveur proxy HTTP ou une passerelle OMS
L’agent OMS pour Linux prend en charge la communication via un serveur proxy HTTP ou HTTPS ou la passerelle OMS vers le service OMS.  L’authentification anonyme et l’authentification de base (nom d’utilisateur/mot de passe) sont prises en charge.  

### <a name="proxy-configuration"></a>Configuration du proxy
La valeur de configuration de proxy a la syntaxe suivante :

`[protocol://][user:password@]proxyhost[:port]`

Propriété|Description
-|-
Protocole|http ou https
user|Nom d’utilisateur facultatif pour l’authentification du proxy
password|Mot de passe facultatif pour l’authentification du proxy
proxyhost|Adresse ou nom de domaine complet du serveur proxy/de la passerelle OMS
port|Numéro de port facultatif pour le serveur proxy/la passerelle OMS

Par exemple : `http://user01:password@proxy01.contoso.com:8080`

Le serveur proxy peut être spécifié lors de l’installation ou en modifiant le fichier de configuration proxy.conf après l’installation.   

### <a name="specify-proxy-configuration-during-installation"></a>Spécifier la configuration du serveur proxy lors de l’installation
L’argument `-p` ou `--proxy` pour le groupe d’installation omsagent spécifie la configuration de proxy à utiliser. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p http://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Définition de la configuration du proxy dans un fichier
La configuration du proxy peut être définie dans les fichiers `/etc/opt/microsoft/omsagent/proxy.conf` et `/etc/opt/microsoft/omsagent/conf/proxy.conf `. Ces fichiers peuvent être créés ou modifiés directement, mais leurs autorisations doivent être mises à jour pour accorder à l’utilisateur omiuser une autorisation de lecture sur les fichiers. Par exemple :
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:8080"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Suppression de la configuration de proxy
Pour supprimer une configuration de proxy précédemment définie et revenir à une connexion directe, supprimez le fichier proxy.conf :
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-operations-management-suite"></a>Intégration avec Operations Management Suite
Si un ID d’espace de travail et une clé n’ont pas été fournis pendant l’installation du groupe, l’agent doit être enregistré dans Operations Management Suite.

### <a name="onboarding-using-the-command-line"></a>Intégration avec la ligne de commande
Exécutez la commande omsadmin.sh en fournissant l’ID de l’espace de travail et la clé pour votre espace de travail. Cette commande doit être exécutée en tant que root (avec élévation des droits sudo) :
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>Intégration à l’aide d’un fichier
1.  Créez le fichier `/etc/omsagent-onboard.conf`. Le fichier doit être accessible en lecture et en écriture pour root.
`sudo vi /etc/omsagent-onboard.conf`
2.  Insérez les lignes suivantes dans le fichier avec votre ID d’espace de travail et la clé partagée :

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Exécutez la commande suivante pour intégrer OMS : `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  Le fichier est supprimé lors de la réussite de l’intégration.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Permettre à l’Agent OMS pour Linux d’envoyer des rapports à System Center Operations Manager
Procédez comme suit pour configurer l’Agent OMS pour Linux pour envoyer des rapports à un groupe d’administration System Center Operations Manager.  

1. Modifiez le fichier `/etc/opt/omi/conf/omiserver.conf`
2. Vérifiez que la ligne commençant par **httpsport =** spécifie le port 1270. Par exemple : `httpsport=1270`
3. Redémarrez le serveur OMI : `sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Journaux de l’agent
Les journaux pour l’agent OMS pour Linux sont accessibles ici : `/var/opt/microsoft/omsagent/<workspace id>/log/` Les journaux du programme omsconfig (configuration de l’agent) sont accessibles ici : `/var/opt/microsoft/omsconfig/log/` Les journaux pour les composants SCX et OMI (qui fournissent des données de mesure des performances) sont accessibles ici :`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Configuration de rotation des journaux##
La configuration de la rotation pour omsagent est accessible ici :`/etc/logrotate.d/omsagent-<workspace id>`

Les paramètres par défaut sont : 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Désinstallation de l’Agent OMS pour Linux
Les packages de l’agent peuvent être désinstallés en exécutant le fichier .sh du groupe avec l’argument `--purge`, ce qui supprime complètement l’agent et sa configuration de l’ordinateur.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>Problème : Impossible de se connecter via le proxy pour OMS

#### <a name="probable-causes"></a>Causes probables
* Le proxy spécifié pendant l’intégration est incorrect
* Les points de terminaison du Service OMS ne sont pas approuvés dans votre centre de données 

#### <a name="resolutions"></a>Résolutions
1. Réintégrez le service OMS à l’Agent OMS pour Linux à l’aide de la commande suivante avec l’option `-v` activée. Cela autorise une sortie détaillée de l’agent qui se connecte via le proxy au Service OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Passez en revue la section [Configuration de l’agent pour une utilisation avec un serveur proxy HTTP(#configuring the-agent-for-use-with-a-http-proxy-server) pour vérifier que vous avez correctement configuré l’agent pour communiquer via un serveur proxy.    
* Vérifiez que les points de terminaison du Service OMS suivants sont approuvés :

    |Ressource de l'agent| Ports |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Port 443|   
    |*.oms.opinsights.azure.com | Port 443|   
    |ods.systemcenteradvisor.com | Port 443|   
    |*.blob.core.windows.net/ | Port 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problème : Vous recevez une erreur 403 lorsque vous tentez d’effectuer l’intégration

#### <a name="probable-causes"></a>Causes probables
* La date et l’heure sont incorrectes sur le serveur Linux 
* L’ID et la clé d’espace de travail utilisés sont incorrects

#### <a name="resolution"></a>Résolution :

1. Vérifiez l’heure sur votre serveur Linux avec la commande date. Si l’heure est à +/-15 minutes de l’heure actuelle, l’intégration échoue. Pour corriger ce problème, mettez à jour de la date et/ou le fuseau horaire de votre serveur Linux. 
2. Vérifiez que vous avez installé la dernière version de l’agent OMS pour Linux.  Celle-ci vous avertit si le décalage horaire est à l’origine de l’échec d’intégration.
3. Relancez l’intégration avec la bonne clé et le bon identifiant d’espace de travail en suivant les instructions d’installation fournies précédemment dans cette rubrique.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problème : Vous voyez une erreur 500 et 404 dans le fichier journal juste après l’intégration
Il s’agit d’un problème connu qui se produit lors du premier chargement de données Linux dans un espace de travail OMS. Cela n’affecte pas les données envoyées ni l’expérience du service.

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>Problème : Vous ne voyez pas toutes les données dans le portail OMS

#### <a name="probable-causes"></a>Causes probables

- L’intégration au Service OMS a échoué.
- La connexion au Service OMS est bloquée.
- Les données de l’Agent OMS pour Linux sont sauvegardées

#### <a name="resolutions"></a>Résolutions
1. Vérifiez si l’intégration du service OMS a réussi en vous assurant que le fichier suivant existe : `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Relancer l’intégration à l’aide des instructions de la ligne de commande `omsadmin.sh`
3. Si vous utilisez un proxy, reportez-vous à la procédure de résolution de proxy fournie précédemment.
4. Dans certains cas, lorsque l’Agent OMS pour Linux ne peut pas communiquer avec le Service OMS, les données de l’Agent sont mises en file d’attente dans la taille de mémoire tampon saturée de 50 Mo. L’Agent OMS pour Linux doit être redémarré en exécutant la commande suivante : `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ce problème est résolu dans l’agent version 1.1.0-28 et versions ultérieures.


