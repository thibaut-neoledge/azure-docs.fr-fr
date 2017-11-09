---
title: "Déployer LAMP sur une machine virtuelle Linux dans Azure | Microsoft Docs"
description: Didacticiel - Installer la pile LAMP sur une machine virtuelle Linux dans Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: c00e6a190633348411f47490808739d570cafd69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Installer un serveur web LAMP sur une machine virtuelle Azure
Cet article vous guide à travers le déploiement d’un serveur web Apache, de celui de MySQL et de PHP (la pile LAMP) sur une machine virtuelle Ubuntu dans Azure. Si vous préférez le serveur web NGINX, consultez le didacticiel [Pile LEMP](tutorial-lemp-stack.md). Pour voir le serveur LAMP fonctionner, vous pouvez éventuellement installer et configurer un site WordPress. Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu (la lettre « L » dans la pile LAMP)
> * Ouvrez le port 80 pour le trafic web
> * Installer Apache, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur le serveur LAMP


Pour plus d’informations sur la pile LAMP, notamment des recommandations relatives à un environnement de production, consultez la [Documentation Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Installer Apache, MySQL et PHP

Exécutez la commande suivante pour mettre à jour les sources de package Ubuntu et installer Apache, MySQL et PHP. Remarquez le signe insertion (^) à la fin de la commande.


```bash
sudo apt update && sudo apt install lamp-server^
```



Vous êtes invité à installer les packages et les autres dépendances. Lorsque vous y êtes invité, définissez un mot de passe racine pour MySQL, puis [Entrée] pour continuer. Suivez les invites restantes. Ce processus installe les extensions PHP minimales requises pour utiliser PHP avec MySQL. 

![Page de mot de passe racine MySQL][1]

## <a name="verify-installation-and-configuration"></a>Vérifier l’installation et la configuration


### <a name="apache"></a>Apache

Vérifiez la version d’Apache à l’aide de la commande suivante :
```bash
apache2 -v
```

Avec Apache installé et le port 80 ouvert sur votre machine virtuelle, le serveur web est maintenant accessible à partir d’Internet. Pour afficher la page par défaut d’Apache2 Ubuntu, ouvrez un navigateur web et entrez l’adresse IP publique de la machine virtuelle. Utilisez l’adresse IP publique dont vous vous êtes servi pour vous connecter par SSH à la machine virtuelle :

![Page par défaut d’Apache][3]


### <a name="mysql"></a>MySQL

Vérifiez la version de MySQL avec la commande suivante (remarquez le paramètre `V` avec une majuscule) :

```bash
mysql -V
```

Nous vous recommandons d’exécuter le script suivant pour vous aider à sécuriser l’installation de MySQL :

```bash
mysql_secure_installation
```

Entrez votre mot de passe racine MySQL et configurez les paramètres de sécurité pour votre environnement.

Si vous souhaitez créer une base de données MySQL, ajoutez des utilisateurs ou changez les paramètres de configuration, puis connectez-vous à MySQL :

```bash
mysql -u root -p
```

Lorsque vous avez terminé, quittez l’invite de mysql en tapant `\q`.

### <a name="php"></a>PHP

Vérifiez la version de PHP avec la commande suivante :

```bash
php -v
```

Si vous voulez poursuivre le test, créez une page d’informations PHP rapide pour l’afficher dans un navigateur. La commande suivante crée la page d’informations PHP :

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

À présent, vous pouvez vérifier la page d’informations PHP que vous avez créée. Ouvrez un navigateur web et accédez à `http://yourPublicIPAddress/info.php`. Remplacez l’adresse IP publique de votre machine virtuelle. Elle doit ressembler à cette image.

![Page d’informations PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un serveur LAMP dans Azure. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu
> * Ouvrez le port 80 pour le trafic web
> * Installer Apache, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur le serveur LAMP

Passez au didacticiel suivant pour savoir comment mieux protéger les serveurs SSL à l’aide des certificats SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web avec SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png