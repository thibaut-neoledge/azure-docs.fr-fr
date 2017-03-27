---
title: "Utilisation d’un bac à sable (sandbox) Hadoop pour en savoir plus sur Hadoop | Microsoft Docs"
description: "Pour en savoir plus sur l’utilisation de l’écosystème Hadoop, vous pouvez configurer un bac à sable (sandbox) Hadoop à partir de Hortonworks sur une machine virtuelle Azure. "
keywords: "émulateur Hadoop, bac à sable (sandbox) hadoop"
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1618ed7971ffef0eae55b73b4bdd04f3f14195ba
ms.openlocfilehash: a070df78bf95173aa48da60d24d14d08d9be8d9a
ms.lasthandoff: 01/07/2017


---
# <a name="get-started-in-the-hadoop-ecosystem-with-a-hadoop-sandbox-on-a-virtual-machine"></a>Prise en main de l’écosystème Hadoop avec un bac à sable (sandbox) Hadoop sur une machine virtuelle

Apprenez à installer le bac à sable (sandbox) Hadoop de Hortonworks sur une machine virtuelle pour vous familiariser avec l’écosystème Hadoop. Le bac à sable fournit un environnement de développement local pour se familiariser avec Hadoop, Hadoop Distributed File System (HDFS) et la soumission de tâches. Une fois que vous êtes familiarisé avec Hadoop, vous pouvez commencer à l’utiliser sur Azure en créant un cluster HDInsight. Pour plus d’informations sur la prise en main, consultez [Prise en main de Hadoop sur HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Composants requis
* [Oracle VirtualBox](https://www.virtualbox.org/) Téléchargez et installez l’application à partir d’[ici](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Téléchargement et installation de la machine virtuelle
1. Accédez aux [téléchargements Hortonworks](http://hortonworks.com/downloads/#sandbox).
2. Cliquez sur **TÉLÉCHARGER POUR VIRTUALBOX** pour télécharger le dernier bac à sable (sandbox) Hrotonworks sur une machine virtuelle. Vous serez invité à vous inscrire à Hortonworks avant que le téléchargement commence. Une à deux heures sont nécessaires pour le téléchargement, en fonction de la vitesse de votre réseau.
   
    ![Image du lien pour le téléchargement de Hortonworks Sandbox pour VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)
3. À partir de la même page web, cliquez sur le lien **Importer dans Virtual Box** pour télécharger un fichier PDF contenant des instructions d’installation de la machine virtuelle.

Pour télécharger un bac à sable (sandbox) de version HDP plus ancienne, développez l’archive :

![Archive sandbox Hortonworks](./media/hdinsight-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Démarrage de la machine virtuelle

1. Ouvrez Oracle VirtualBox pour machine virtuelle.
2. À partir du menu **Fichier**, cliquez sur **Importer l’appliance**, puis spécifiez l’image de sandbox Hortonworks.
1. Sélectionnez Hortonworks Sandbox, cliquez sur **Démarrer**, puis sur **Démarrage normal**. Une fois que la machine virtuelle a terminé le processus de démarrage, elle affiche les instructions de connexion.
   
    ![Démarrage normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)
2. Ouvrez un navigateur web et accédez à l’URL affichée (généralement http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Définition de mots de passe Sandbox

1. Dans la page de **prise en main** de Hortonworks Sandbox, sélectionnez **View Advanced Options (Options d’affichage avancées)**. Utilisez les informations de cette page pour vous connecter à Hortonworks Sandbox à l’aide de SSH. Utilisez le nom et le mot de passe fournis.
   
   > [!NOTE]
   > Si vous n’avez pas de client SSH installé, vous pouvez utiliser le client SSH en ligne fourni par la machine virtuelle à l’adresse **http://localhost:4200/**.
   > 
   
    La première fois que vous vous connectez à l’aide du client SSH, vous serez invité à modifier le mot de passe du compte racine. Entrez le nouveau mot de passe que vous utiliserez pour vous connecter à l’aide du client SSH à l’avenir.
2. Une fois connecté, entrez la commande suivante :
   
        ambari-admin-password-reset
   
    Lorsque vous y êtes invité, indiquez le mot de passe du compte d’administrateur Ambari. Il sera utilisé pour accéder à l’interface utilisateur web d’Ambari.

## <a name="use-hive-commands"></a>Utilisation de commandes Hive

1. À partir d’une connexion SSH au bac à sable, utilisez la commande suivante pour démarrer l’interpréteur de commandes Hive :
   
        hive
2. Une fois l’interpréteur de commandes a démarré, procédez comme suit pour afficher les tables fournies avec le bac à sable :
   
        show tables;
3. Utilisez la commande suivante pour récupérer 10 lignes de la table `sample_07` :
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Étapes suivantes
* [Apprendre à utiliser Visual Studio avec Hortonworks Sandbox](hdinsight-hadoop-emulator-visual-studio.md)
* [Se familiariser avec Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Didacticiel Hadoop - Prise en main de HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)


