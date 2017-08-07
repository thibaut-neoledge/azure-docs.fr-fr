---
title: "Créer votre premier maître Jenkins sur une machine virtuelle Linux (Ubuntu) sur Azure"
description: "Tirez profit du modèle de solution pour déployer Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: f892ec7bd61124f7958a50ebdfb49c7310d4ee18
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Créer votre premier maître Jenkins sur une machine virtuelle Linux (Ubuntu) sur Azure

Ce guide de démarrage rapide vous indique comment installer la dernière version stable de Jenkins sur une machine virtuelle Linux (Ubuntu 14.04 LTS) avec les outils et les plug-ins configurés pour fonctionner avec Azure. Ces outils sont les suivants :
<ul>
<li>Git pour contrôle de code source</li>
<li>Plug-in d’informations d’identification Azure pour se connecter en toute sécurité</li>
<li>Plug-in d’agents de machine virtuelle Azure pour le build élastique, le test et l’intégration continue</li>
<li>Plug-in de stockage Azure pour stocker les artefacts</li>
<li>Interface de ligne de commande Azure pour déployer des applications à l’aide de scripts</li>
</ul>

Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte Azure gratuit
> * Créer un maître Jenkins sur une machine virtuelle Azure avec un modèle de solution 
> * Effectuer la configuration initiale de Jenkins
> * Installer les plug-ins suggérés

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>Créer la machine virtuelle dans Azure en déployant le modèle de solution pour Jenkins

Les modèles de démarrage rapide Azure vous permettent de déployer rapidement et de façon fiable des technologies complexes sur Azure.  Azure Resource Manager vous donne la possibilité d’approvisionner vos applications à l’aide d’un [modèle déclaratif](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins). Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.

Pour comprendre les différentes options de coût, consultez les informations sur [les abonnements et la tarification](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins?tab=PlansAndPrice) de ce modèle.

Accédez à [l’image Place de marché de Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) et cliquez sur **OBTENIR MAINTENANT**.  

Dans le Portail Azure, cliquez sur **Créer**.  Ce modèle nécessite l’utilisation de Resource Manager. La liste déroulante de modèles est donc désactivée.
   
![Boîte de dialogue du Portail Azure](./media/install-jenkins-solution-template/ap-create.png)

Dans l’onglet **Configurer les paramètres de base** :

![Configurer les paramètres de base](./media/install-jenkins-solution-template/ap-basic.png)

* Fournissez un nom pour votre instance Jenkins.
* Sélectionnez un type de disque de machine virtuelle.  Pour les charges de travail de production, choisissez une machine virtuelle et un disque SSD plus volumineux afin d’optimiser les performances.  Pour plus d’informations sur les types de disques Azure, consultez [cet article](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage).
* Le nom d’utilisateur doit respecter les exigences de longueur et ne doit pas inclure de mots réservés ni de caractères non pris en charge. Les noms tels que « admin » ne sont pas autorisés.  Pour plus d’informations sur les exigences relatives au nom d’utilisateur et au mot de passe, consultez [cet article](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq).
* Pour le type d’authentification, créez une instance qui est sécurisée par un mot de passe ou une [clé publique SSH](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Si vous utilisez un mot de passe, ce dernier doit remplir 3 des exigences suivantes : un caractère minuscule, un caractère majuscule, un chiffre et un caractère spécial.
* Conserver le type de version Jenkins comme **LTS**
* Sélectionnez un abonnement.
* Créez un groupe de ressources ou utilisez-en un existant mais vide. 
* Sélectionnez un emplacement.

Dans l’onglet **Configure additional options (Configurer des options supplémentaires)** :

![Configurer des options supplémentaires](./media/install-jenkins-solution-template/ap-addtional.png)

* Fournissez une étiquette de nom de domaine pour identifier de manière unique le maître Jenkins.

Cliquez sur **OK** pour accéder à l’étape suivante. 

Une fois la validation réussie, cliquez sur **OK** pour télécharger le modèle et les paramètres. 

Ensuite, sélectionnez **Acheter** pour approvisionner toutes les ressources.

## <a name="setup-ssh-port-forwarding"></a>Configurer le réacheminement de port SSH

Par défaut, l’instance Jenkins utilise le protocole http et écoute le port 8080. Les utilisateurs ne doivent pas s’authentifier sur des protocoles non sécurisés.
    
Configurez le réacheminement de port pour visualiser l’interface utilisateur Jenkins sur votre ordinateur local.

### <a name="if-you-are-using-windows"></a>Si vous utilisez Windows, procédez comme suit :

Installez PuTTY et exécutez la commande ci-après si vous utilisez un mot de passe pour sécuriser Jenkins :
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Pour ouvrir une session, entrez le mot de passe.

![Pour ouvrir une session, entrez le mot de passe.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Si vous utilisez SSH, exécutez cette commande :
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Si vous utilisez Linux ou Mac :

Si vous utilisez un mot de passe pour sécuriser votre maître Jenkins, exécutez cette commande :
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Pour ouvrir une session, entrez le mot de passe.

Si vous utilisez SSH, exécutez cette commande :
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Se connecter à Jenkins
Une fois que vous avez démarré votre tunnel, accédez à http://localhost:8080/ sur votre ordinateur local.

Déverrouillez le tableau de bord Jenkins pour la première fois avec le mot de passe d’administrateur initial.

![Déverrouiller Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Pour obtenir un jeton, connectez-vous par le biais de SSH à la machine virtuelle et exécutez `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Déverrouiller Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

Vous êtes invité à installer les plug-ins suggérés.

Ensuite, créez un utilisateur administrateur pour votre maître Jenkins.

Votre instance Jenkins est à présent prête à l’emploi. Vous pouvez afficher une vue en lecture seule en accédant à http://\<nom DNS public de l’instance que vous venez de créer\>.

![Jenkins est prêt à l’emploi](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un maître Jenkins avec le modèle de solution
> * Effectuer la configuration initiale de Jenkins
> * Installer les plug-ins

Pour découvrir comment utiliser des agents de machine virtuelle Azure pour l’intégration continue avec Jenkins, cliquez sur le lien ci-dessous :

> [!div class="nextstepaction"]
> [Machines virtuelles Azure en tant qu’agents Jenkins](jenkins-azure-vm-agents.md)

