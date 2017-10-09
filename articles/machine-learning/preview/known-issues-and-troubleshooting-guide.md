---
title: "Problèmes connus et guide de dépannage | Microsoft Docs"
description: "Liste des problèmes connus et guide de dépannage"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - Problèmes connus et guide de dépannage 
Cet article vous permet de rechercher et corriger les erreurs ou défaillances rencontrées dans le cadre de l’utilisation de l’application Azure Machine Learning Workbench. 

> [!NOTE]
> Dans vos échanges par e-mail ou via des posts sur les forums avec l’équipe de support, il s’avère utile de connaître le numéro de build. Pour trouver le numéro de build de l’application, cliquez le menu **Aide**. Cliquez sur le numéro de build pour le copier dans le Presse-papiers. Vous pouvez le coller dans vos e-mails ou sur des forums d’aide pour signaler des problèmes.

![vérifier le numéro de version](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows et Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>Instance Data Science Virtual Machine (DSVM) Azure basée sur CentOS 
* L’envoi de tâches à une instance Data Science Virtual Machine (DSVM) Azure basée sur CentOS n’est pas pris en charge. Vous pouvez cibler une [instance DSVM basée sur Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

### <a name="docker-error"></a>Erreur Docker 
* Pour une exécution dans un conteneur Docker local, vous pouvez corriger l’erreur ci-après en remplaçant la valeur du serveur DNS Docker Automatique par la valeur 8.8.8.8 fixe. 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![Image](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Mot de passe Linux VM 
* Si vous modifiez le mot de passe à partir du portail Azure sur une machine virtuelle Ubuntu Linux, vous risquez d’obtenir l’erreur suivante quand vous exécutez un travail dessus :
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  La solution consiste à ajouter un fichier (par exemple _myDockerUsers_) dans _/etc/sudoers.d_ avec le contenu suivant :
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>Clés SSH 
* Les clés SSH ne sont pas prises en charge lors de la connexion à un ordinateur distant ou un cluster Spark via SSH. Seul le mode nom d’utilisateur/mot de passe est pris en charge.

## <a name="windows-only"></a>Windows uniquement 
### <a name="sharing-c-drive-in-docker"></a>Partage du lecteur C dans Docker 
* Vérifier le partage sur le lecteur C à l’aide de l’Explorateur de fichiers
* Ouvrir les paramètres de carte réseau et désinstaller/réinstaller « Partage de fichiers et d’imprimantes pour les réseaux Microsoft » pour vEthernet
* Ouvrir les paramètres Docker et partager le lecteur C à partir des paramètres Docker
* Les modifications apportées au mot de passe Windows affectent le partage. Ouvrez l’Explorateur de fichiers, repartagez le lecteur C et entrez le nouveau mot de passe.
* Vous pouvez également rencontrer un problème de pare-feu lorsque vous tentez de partager votre lecteur C avec Docker. Ce [post Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) peut s’avérer utile.
* Lorsque vous partagez le lecteur C à l’aide d’informations d’identification de domaine, le partage peut cesser de fonctionner sur les réseaux où le contrôleur de domaine n’est pas accessible (par exemple, réseau domestique, Wi-Fi public, etc.). Pour plus d’informations, consultez [ce post](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).



## <a name="mac-only"></a>Mac uniquement 
* Les transformations de clustering de texte ne sont pas prises en charge sur Mac.
* La bibliothèque RevoScalePy n’est pas prise en charge sur Mac.

## <a name="azure-machine-learning-service-limits"></a>Limites du service Azure Machine Learning

- Taille maximale autorisée pour le dossier du projet : 25 Mo
    >[!Note]
    >Cette limite ne s’applique pas aux dossiers `.git`, `docs` et `outputs`. Ces noms de dossier respectent la casse.

- Temps d’exécution maximal autorisé pour les expérimentations : 7 jours
- Taille maximale du fichier suivi dans le dossier `outputs` après une exécution : 512 Mo 

>[!NOTE]
>Si vous travaillez avec des fichiers volumineux, reportez-vous à [Persistance des modifications et gestion des fichiers volumineux](how-to-read-write-files.md).

## <a name="other-issues"></a>Autres problèmes
Si vous avez connaissance d’autres problèmes non décrits, envoyez-nous des commentaires via _Envoyer un sourire/un smiley mécontent_. 




