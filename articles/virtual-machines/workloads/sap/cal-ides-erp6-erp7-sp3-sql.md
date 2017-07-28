---
title: "Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Azure | Microsoft Docs"
description: "Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Azure"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.contentlocale: fr-fr
ms.lasthandoff: 06/23/2017


---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Azure
Cet article explique comment déployer un système SAP IDES exécuté avec SQL Server et le système d’exploitation Windows sur Azure via SAP Cloud Appliance Library (SAP CAL) 3.0. Les captures d’écran montrent la procédure étape par étape. Pour déployer une autre solution, suivez la même procédure.

Pour démarrer avec SAP CAL, accédez au site web [SAP Cloud Appliance Library](https://cal.sap.com/). Il existe également un blog SAP sur le nouveau [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
Depuis le 29 mai 2017, vous pouvez utiliser le modèle de déploiement Azure Resource Manager en plus du modèle de déploiement classique pour déployer SAP CAL. Nous vous recommandons d’utiliser le nouveau modèle de déploiement Resource Manager et d’ignorer le modèle de déploiement classique.

Si vous avez déjà créé un compte SAP CAL qui utilise le modèle classique, *vous devez créer un autre compte SAP CAL*. Ce compte doit être destiné exclusivement au déploiement dans Azure à l’aide du modèle Resource Manager.

Après vous être connecté à SAP CAL, la première page vous conduit généralement à la page **Solutions**. Les solutions proposées sur SAP CAL sont toujours plus nombreuses, si bien que vous devrez peut-être en faire défiler plusieurs avant de trouver celle souhaitée. La solution SAP IDES basée sur Windows mise en surbrillance disponible exclusivement sur Azure montre le processus de déploiement :

![Solutions SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Création d’un compte dans SAP CAL
1. Lorsque vous vous connectez à SAP CAL pour la première fois, utilisez votre identifiant SAP S-User ou un autre identifiant utilisateur enregistré auprès de SAP. Ensuite, définissez un compte SAP CAL utilisé par SAP CAL pour déployer des appliances sur Azure. Dans la définition du compte, procédez comme suit :

    a. Sélectionnez le modèle de déploiement sur Azure (Resource Manager ou classique).

    b. Entrez votre abonnement Azure. Un compte SAP CAL ne peut être attribué qu’à un seul abonnement. Si vous avez besoin de plus d’un abonnement, vous devez créer un autre compte SAP CAL.
    
    c. Accordez à SAP CAL l’autorisation de déployer dans votre abonnement Azure.

    > [!NOTE]
    Les étapes suivantes montrent comment créer un compte SAP CAL pour les déploiements Resource Manager. Si vous disposez déjà d’un compte SAP CAL lié à un modèle de déploiement classique, vous *devez* suivre ces étapes pour créer un nouveau compte SAP CAL. Le nouveau compte SAP CAL doit être déployé dans le modèle Resource Manager.

2. Pour créer un nouveau compte SAP CAL, vous disposez de deux choix dans la page **Compte** pour Azure : 

    a. **Microsoft Azure (classique)** est le modèle de déploiement classique et n’est plus recommandé.

    b. **Microsoft Azure** est le nouveau modèle de déploiement Resource Manager.

    ![Comptes SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Pour déployer dans le modèle Resource Manager, sélectionnez **Microsoft Azure**.

    ![Comptes SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Entrez **l’ID d’abonnement** Azure qui se trouve sur le portail Azure. 

    ![ID d’abonnement SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. Pour autoriser le déploiement de SAP CAL dans l’abonnement Azure que vous avez défini, cliquez sur **Autoriser**. La page suivante s’affiche dans l’onglet de navigateur :

    ![Connexion aux services cloud d’Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Si plusieurs utilisateurs sont répertoriés, choisissez le compte Microsoft lié en tant que coadministrateur de l’abonnement Azure que vous avez sélectionné. La page suivante s’affiche dans l’onglet de navigateur :

    ![Confirmation des services cloud d’Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Cliquez sur **Accepter**. Si l’autorisation est accordée, la définition du compte SAP CAL s’affiche à nouveau. Après une courte période, un message confirme que le processus d’autorisation a réussi.

7. Pour affecter le nouveau compte SAP CAL créé à votre utilisateur, entrez votre **ID utilisateur** dans la zone de texte à droite et cliquez sur **Ajouter**. 

    ![Association du compte à l’utilisateur](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Pour associer votre compte à l’utilisateur que vous utilisez pour vous connecter à SAP CAL, cliquez sur **Réviser**. 

9. Pour créer l’association entre votre utilisateur et le compte SAP CAL nouvellement créé, cliquez sur **Créer**.

    ![Association d’un utilisateur au compte](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Vous avez correctement créé un compte SAP CAL capable d’effectuer ce qui suit :

- Utiliser le modèle de déploiement Resource Manager.
- Déployer des systèmes SAP dans votre abonnement Azure.

> [!NOTE]
Avant de pouvoir déployer la solution SAP IDES basée sur Windows et SQL Server, vous devrez peut-être souscrire à un abonnement SAP CAL. Sinon, la solution risque d’apparaître comme **Verrouillée** dans la page de vue d’ensemble.

### <a name="deploy-a-solution"></a>Déployer une solution
1. Après avoir configuré un compte SAP CAL, sélectionnez la solution **Solution SAP IDES sur Windows et SQL Server**. Cliquez sur **Créer une instance** et vérifiez les conditions d’utilisation. 

2. Sur la page **Basic Mode: Create Instance** (Mode de base : Créer une instance), procédez comme suit :

    a. Entrez une instance **Nom**.

    b. Sélectionnez une **Région** Azure. Vous aurez peut-être besoin d’un abonnement SAP CAL pour avoir le choix entre plusieurs régions Azure.

    c.  Entrez le **Mot de passe** maître pour la solution, comme indiqué :

    ![SAP CAL - Mode de base : Créer une instance](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Cliquez sur **Create**. Après un délai variable en fonction de la taille et de la complexité de la solution (SAP CAL en donne une estimation), la solution apparaît comme active et prête à être utilisée : 

    ![Instances de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Pour trouver le groupe de ressources et tous ses objets créés par SAP CAL, accédez au portail Azure. Les machines virtuelles sont repérables car elles commencent par le nom d’instance qui a été spécifié dans SAP CAL.

    ![Objets du groupe de ressources](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. Dans le portail SAP CAL, accédez aux instances déployées, puis cliquez sur **Connect** (Connexion). La fenêtre indépendante suivante apparaît : 

    ![Connexion à l’instance](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Avant de pouvoir utiliser l’une des options de connexion aux systèmes déployés, cliquez sur **Getting Started Guide** (Guide de prise en main). La documentation désigne les utilisateurs pour chacune des méthodes de connectivité. Les mots de passe de ces utilisateurs sont configurés sur le mot de passe maître défini au début du processus de déploiement. Dans la documentation, d’autres utilisateurs plus fonctionnels sont répertoriés avec leurs mots de passe, que vous pouvez utiliser pour vous connecter au système déployé.

    ![Documentation de présentation de SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

En quelques heures, un système SAP IDES sain est déployé dans Azure.

Si vous avez acheté un abonnement SAP CAL, SAP prend entièrement en charge les déploiements via SAP CAL sur Azure. La file d’attente de prise en charge est BC-VCM-CAL.


