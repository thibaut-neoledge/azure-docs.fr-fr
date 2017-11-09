---
title: "Résoudre les problèmes de déploiement d’OpenShift dans Azure | Microsoft Docs"
description: "Résoudre les problèmes de déploiement d’OpenShift dans Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Résoudre les problèmes de déploiement d’OpenShift dans Azure

Si le cluster OpenShift ne se déploie pas correctement, certaines tâches de dépannage peuvent être effectuées pour circonscrire le problème. Affichez l’état du déploiement et comparez-le à la liste suivante de codes de sortie.

- Code de sortie 3 : vos nom/mot de passe d’utilisateur ou ID d’organisation/clé d’activation d’abonnement Red Hat sont incorrects
- Code de sortie 4 : votre ID de pool Red Hat est incorrect ou aucun droit n’est disponible
- Code de sortie 5 : impossible d’approvisionner le volume de pool dynamique Docker
- Code de sortie 6 : l’installation du cluster OpenShift a échoué
- Code de sortie 7 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de Cloud Azure a échoué ; problème de fichier de configuration maître sur le nœud master
- Code de sortie 8 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de Cloud Azure a échoué ; problème de fichier de configuration de nœud sur le nœud master
- Code de sortie 9 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de Cloud Azure a échoué ; problème de fichier de configuration de nœud sur un nœud infrastructure ou application
- Code de sortie 10 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de Cloud Azure a échoué ; correction des nœuds master ou impossibilité de définir master comme non planifiable
- Code de sortie 11 : le déploiement des métriques a échoué
- Code de sortie 12 : le déploiement de la journalisation a échoué

Pour les codes de sortie 7 à 10, le cluster OpenShift a été installé, mais la configuration du fournisseur de Cloud Azure a échoué. Vous pouvez établir une connexion SSH au nœud master (OpenShift Origin) ou au nœud bastion (OpenShift Container Platform) et, à partir de là, établir une connexion SSH à chacun des nœuds du cluster et résoudre les problèmes.

Une cause courante des échecs correspondant aux codes de sortie 7 à 9 est que le principal du service ne disposait pas des autorisations appropriées sur l’abonnement ou le groupe de ressources. Si tel est effectivement le problème, attribuez les autorisations appropriées, puis réexécutez manuellement le script qui a échoué et tous les scripts suivants.
Veillez à redémarrer le service qui a échoué (par exemple, systemctl restart atomic-openshift-node.service) avant de réexécuter les scripts.

Pour un dépannage supplémentaire, établissez une connexion SSH à votre nœud master sur le port 2200 (OpenShift Origine) ou nœud bastion sur le port 22 (OpenShift Container Platform). Vous devez être à la racine (sudo su-), puis accédez au répertoire suivant : /var/lib/waagent/custom-script/download.

Vous devriez voir un dossier nommé « 0 » et « 1 ». Dans chacun de ces dossiers, vous voyez deux fichiers : stderr et stdout. Vous pouvez examiner ces fichiers afin de déterminer la cause de la défaillance.
