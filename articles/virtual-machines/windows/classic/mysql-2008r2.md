---
title: "Création d’une machine virtuelle Azure Classic exécutant MySQL | Microsoft Docs"
description: "Créez une machine virtuelle Azure exécutant Windows Server 2012 R2, puis une base de données MySQL avec le modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 1bfce97dba3cfc371900c0e3c6a93369e768c181
ms.lasthandoff: 04/18/2017


---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Installation de MySQL sur une machine virtuelle créée avec le modèle de déploiement classique exécutant Windows Server 2016
[MySQL](https://www.mysql.com) est une base de données SQL open source connue. Ce didacticiel vous montre comment installer et exécuter la **version de communauté de MySQL 5.7.18** comme serveur MySQL sur une machine virtuelle s’exécutant sous **Windows Server 2016**. Votre expérience peut être légèrement différente sur d’autres versions de MySQL ou Windows Server.

Pour obtenir des instructions sur l’installation de MySQL sur Linux, consultez [Installation de MySQL sur Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Création d’une machine virtuelle exécutant Windows Server 2016
Si vous ne disposez pas d’une machine virtuelle s’exécutant sous Windows Server 2016, vous pouvez utiliser ce [didacticiel](./tutorial.md) pour créer la machine virtuelle.

## <a name="attach-a-data-disk"></a>Association d’un disque de données
Une fois la machine virtuelle créée, vous pouvez éventuellement attacher un disque de données. L’ajout d’un disque de données est recommandé pour les charges de travail de production et pour éviter de manquer d’espace sur le lecteur incluant le système d’exploitation (C:).

Consultez [Attacher un disque de données à une machine virtuelle Windows](../attach-disk-portal.md) et suivez les instructions permettant d’attacher un disque vide. Définissez le paramètre de cache d’hôte sur la valeur **Aucun** ou **Lecture seule**.

## <a name="log-on-to-the-virtual-machine"></a>Connexion à la machine virtuelle
[Connectez-vous ensuite à la machine virtuelle](./connect-logon.md) pour installer MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installation et exécution de MySQL Community Server sur la machine virtuelle
Pour installer, configurer et exécuter la version Community de MySQL Server, procédez comme suit :

> [!NOTE]
> Lors du téléchargement des éléments à l’aide d’Internet Explorer, vous pouvez désactiver la **Configuration de sécurité renforcée** d’IE et simplifier le processus de téléchargement. Dans le menu Démarrer, cliquez sur Outils administratifs / Gestionnaire de serveur / Serveur local, puis cliquez sur **Configuration de sécurité renforcée** d’IE et désactivez-la.
>
>

1. Une fois connecté à la machine virtuelle à l’aide du Bureau à distance, cliquez sur **Internet Explorer** dans l’écran d’accueil.
2. Sélectionnez le bouton **Outils** dans l’angle supérieur droit (icône en forme de roue dentée), puis cliquez sur **Options Internet**. Cliquez successivement sur l’onglet **Sécurité**, sur l’icône **Sites de confiance**, puis sur le bouton **Sites**. Ajoutez http://*.mysql.com à la liste des sites de confiance. Cliquez sur **Fermer**, puis sur **OK**.
3. Dans la barre d’adresses d’Internet Explorer, saisissez https://dev.mysql.com/downloads/mysql/.
4. Utilisez le site MySQL pour rechercher et télécharger la dernière version du programme d’installation de MySQL pour Windows. Lorsque vous choisissez le programme d’installation de MySQL, téléchargez la version qui comporte le jeu de fichiers complet (par exemple, le fichier mysql-installer-community-5.7.18.0.msi d’une taille de 352,8 Mo), puis enregistrez le fichier d’installation.
5. Lorsque le programme d’installation est téléchargé, cliquez sur **Exécuter** pour le lancer.
6. Sur la page **Contrat de licence**, acceptez le contrat de licence, puis cliquez sur **Suivant**.
7. Sur la page **Choix du type de configuration**, cliquez sur le type souhaité, puis sur **Suivant**. Les étapes suivantes reposent sur l’hypothèse que vous avez sélectionné le type **Serveur uniquement** .
8. Si la page **Vérifier les exigences** s’affiche, cliquez sur **Exécuter** pour laisser le programme d’installation installer les composants manquants. Suivez les instructions qui s’affichent, par exemple, le runtime redistribuable C++.
9. Sur la page **Installation**, cliquez sur **Exécuter**. Une fois l’installation terminée, cliquez sur **Suivant**.

10. Sur la page **Configuration du produit**, cliquez sur **Suivant**.

11. Sur la page **Type et réseau** , spécifiez le type de configuration et les options de connectivité requises, notamment le port TCP si nécessaire. Sélectionnez **Afficher les options avancées**, puis cliquez sur **Suivant**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. Sur la page **Comptes et rôles** , spécifiez un mot de passe racine MySQL fort. Ajoutez des comptes d’utilisateurs MySQL si nécessaire, puis cliquez sur **Suivant**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. Sur la page **Windows Service**, spécifiez les modifications à apporter aux paramètres par défaut pour exécuter MySQL Server en tant que service Windows, puis cliquez sur **Suivant**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. Les choix proposés sur la page **Plug-ins et extensions** sont facultatifs. Cliquez sur **Suivant** pour continuer.
15. Sur la page **Options avancées**, spécifiez les modifications à apporter aux options de journalisation, puis cliquez sur **Suivant**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. Sur la page **Appliquer la configuration du serveur**, cliquez sur **Exécuter**. Une fois la configuration terminée, cliquez sur **Terminer**.
17. Sur la page **Configuration du produit**, cliquez sur **Suivant**.
18. Sur la page **Installation terminée**, cliquez sur **Copier le journal dans le Presse-papiers** pour l’examiner plus tard, puis cliquez sur **Terminer**.
19. Sur l’écran d’accueil, saisissez**mysql**, puis cliquez sur **Client de ligne de commande MySQL 5.7**.
20. Entrez le mot de passe racine que vous avez indiqué à l’étape 12 pour ouvrir une invite vous permettant d’émettre des commandes pour configurer MySQL. Pour plus d’informations sur les commandes et sur la syntaxe, voir la page [Manuels de référence de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html)(en anglais).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Vous pouvez également configurer les paramètres par défaut de configuration du serveur, tels que les lecteurs et les répertoires de base et de données. Pour plus d’informations, consultez la section [6.1.2 Paramètres par défaut de configuration du serveur](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Pour permettre aux ordinateurs des clients d’accéder au service MySQL sur Internet, vous devez configurer un point de terminaison pour le port TCP et créer une règle de Pare-feu Windows. La valeur par défaut du port sur lequel le service MySQL Server écoute les clients MySQL est 3306. Vous pouvez définir un autre port, tant que le port est cohérent avec la valeur fournie sur la page **Type et réseau** (étape 11 de la procédure précédente).

> [!NOTE]
> Pour la production, lorsque vous rendez le service MySQL Server disponible à tous les ordinateurs sur Internet, tenez compte des implications de sécurité. Vous pouvez définir l’ensemble des adresses IP source autorisées à utiliser le point de terminaison avec une liste de contrôle d’accès (ACL). Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle](setup-endpoints.md).
>
>

Pour configurer un point de terminaison pour le service MySQL Server :

1. Dans le portail Azure, cliquez sur **Machines virtuelles (Classic)**, sur le nom de votre machine virtuelle MySQL, puis sur **Points de terminaison**.
2. Dans la barre de commandes, cliquez sur **Ajouter**.
3. Sur la page **Ajouter un point de terminaison**, entrez un nom unique dans le champ **Nom**.
4. Sélectionnez **TCP** pour le protocole.
5. Entrez le numéro du port, par exemple **3306**, pour **Port public** et **Port privé**, puis cliquez sur **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Ajouter une règle de Pare-feu Windows pour autoriser le trafic MySQL
Pour ajouter une règle de pare-feu Windows qui autorise le trafic MySQL à partir d’Internet, exécutez la commande suivante dans une _invite de commandes Windows PowerShell avec élévation de privilèges_ sur la machine virtuelle du serveur MySQL.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Tester votre connexion à distance
Pour tester votre connexion à distance à la machine virtuelle Azure exécutant le service MySQL Server, vous devez fournir le nom DNS du service cloud contenant la machine virtuelle.

1. Dans le portail Azure, cliquez sur **Machines virtuelles (classic)**, sur le nom de votre machine virtuelle MySQL Server, puis sur **Vue d’ensemble**.
2. Dans le tableau de bord de la machine virtuelle, notez la valeur du champ **Nom DNS**. Voici un exemple :

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. Sur un ordinateur local exécutant MySQL ou le client MySQL, exécutez la commande suivante pour vous connecter en tant qu’utilisateur MySQL :

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   Par exemple, pour le nom d’utilisateur MySQL _dbadmin3_ et le nom DNS _testmysql.cloudapp.net_ pour la machine virtuelle, vous pouvez démarrer MySQL en utilisant la commande suivante :

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l'exécution de MySQL, consultez la [Documentation de MySQL](http://dev.mysql.com/doc/).

