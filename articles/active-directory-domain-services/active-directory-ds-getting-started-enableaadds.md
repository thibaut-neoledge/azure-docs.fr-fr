---
title: "Azure Active Directory Domain Services : activation | Microsoft Docs"
description: "Activer Azure Active Directory Domain Services à l’aide du portail Azure Classic"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e3f8dd4b820da8a14437aa313e4296dc17111674
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>Activer Azure Active Directory Domain Services à l’aide du portail Azure Classic

> [!IMPORTANT]
> L’expérience de portail Azure Classic présentée dans cet article et permettant d’activer Azure AD Domain Services sera bientôt supprimée. Pour découvrir plus de déploiements, [**utilisez plutôt la nouvelle expérience de portail Azure (préversion)**](active-directory-ds-getting-started.md).
>

## <a name="task-3-enable-azure-active-directory-domain-services"></a>Tâche 3 : Activer Azure Active Directory Domain Services
Pour activer Azure Active Directory Domain Services (Azure AD DS) afin de gérer votre répertoire, procédez comme suit :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com).
2. Dans le volet de gauche, cliquez sur le bouton **Active Directory**.
3. Sélectionnez le locataire Azure AD (annuaire) pour lequel vous souhaitez activer Azure AD DS.

    ![Sélectionner un annuaire Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Sur la page de la **version préliminaire de l’annuaire**, cliquez sur l’onglet **Configurer**.

    ![Configurer l’onglet de l’annuaire](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Sous **Services de domaine**, affectez à l’option **Activer les services de domaine pour cet annuaire** la valeur **Oui**.  
    D’autres options de configuration d’Azure AD DS s’affichent sur la page.

    ![Activer les services de domaine](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > Lorsque vous activez Azure AD DS pour votre locataire, Azure AD génère et stocke les hachages d’informations d’identification Kerberos et NTLM nécessaires pour l’authentification des utilisateurs.
   >
   >
6. Spécifiez le **Nom de domaine DNS des services de domaine**.

   * Le nom de domaine par défaut de l’annuaire (qui présente le suffixe **.onmicrosoft.com**) est sélectionné par défaut.

   * La liste contient tous les domaines qui ont été configurés pour votre annuaire Azure AD, y compris les domaines vérifiés et non vérifiés que vous configurez sous l’onglet **Domaines**.

   * Vous pouvez également saisir un nom de domaine personnalisé. Dans cet exemple, le nom de domaine personnalisé est *contoso100.com*.

     > [!WARNING]
     > Le préfixe du nom de domaine spécifié (par exemple, *contoso100* dans le nom de domaine *contoso100.com*) doit contenir au maximum 15 caractères. Vous ne pouvez pas créer un domaine Azure AD DS dont le préfixe contient plus de 15 caractères.
     >
     >
7. Vérifiez que le nom de domaine DNS choisi pour le domaine géré n’existe pas au sein du réseau virtuel. Plus précisément, vérifiez ce qui suit :

   * Vous disposez d’un domaine présentant le nom de domaine DNS au sein du réseau virtuel.

   * Le réseau virtuel que vous avez sélectionné comprend une connexion VPN avec votre réseau local et vous disposez d’un domaine présentant le même nom de domaine DNS sur votre réseau local.

   * Il existe un service cloud portant ce nom sur le réseau virtuel.
8. Sélectionnez le réseau virtuel sur lequel vous souhaitez qu’Azure AD DS soit disponible. Dans la liste déroulante **Connecter les services de domaine à ce réseau virtuel**, sélectionnez le réseau virtuel et le sous-réseau dédié que vous avez créés. Tenez également compte des éléments suivants :

   * Assurez-vous que le réseau virtuel que vous avez spécifié appartient à une région Azure prise en charge par Azure AD DS. Pour connaître les régions Azure dans lesquelles Azure AD DS est disponible, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/).

   * Les réseaux virtuels appartenant à une région dans laquelle Azure AD DS n’est pas pris en charge n’apparaissent pas dans la liste déroulante.

   * Utilisez un sous-réseau dédié au sein du réseau virtuel pour Azure AD DS. Veillez à ne *pas* sélectionner le sous-réseau de passerelle. Consultez [Mise en réseau - Éléments à prendre en compte](active-directory-ds-networking.md).

   * De même, les réseaux virtuels créés à l’aide du logiciel Azure Resource Manager ne sont pas affichés dans la liste déroulante. Les réseaux virtuels basés sur Resource Manager ne sont pas pris en charge par Azure AD DS pour le moment.
9. Pour activer Azure AD DS, cliquez sur **Enregistrer** dans le volet des tâches, en bas de la page.
    * La page affiche l’état *En attente* pendant l’activation de cette fonctionnalité pour votre annuaire.

        ![Fenêtre Activer les services de domaine](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure AD DS offre une haute disponibilité pour votre domaine managé. Une fois Azure Active Directory Domain Services activé, les adresses IP auxquelles les services de domaine sont disponibles sur le réseau virtuel s’affichent l’une après l’autre. La deuxième adresse IP s’affiche rapidement après la première, dès que le service active la haute disponibilité pour votre domaine. Une fois que la haute disponibilité est configurée et active pour votre domaine, deux adresses IP apparaissent normalement dans la section **services de domaine** de l’onglet **Configurer**.
        >
        >
    * Après 20 à 30 minutes, la première adresse IP à laquelle les services de domaine sont disponibles sur votre réseau virtuel apparaît dans le champ **Adresse IP** de la page **Configurer**.

        ![Fenêtre Azure AD DS affichant la première adresse IP configurée](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * Une fois que la haute disponibilité est opérationnelle pour votre domaine, deux adresses IP apparaissent sur la page. Votre domaine géré est disponible sur votre réseau virtuel sélectionné à ces deux adresses IP.

10. Notez ces adresses IP afin de pouvoir mettre à jour les paramètres DNS pour votre réseau virtuel. Cette étape permet aux machines virtuelles du réseau virtuel de se connecter au domaine en vue de procéder à diverses opérations, telles que la jonction de domaine.

    ![Fenêtre Azure AD DS affichant les deux adresses IP configurées](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> En fonction de la taille de votre locataire Azure AD (par exemple, le nombre d’utilisateurs ou de groupes), la synchronisation avec votre domaine managé peut prendre du temps. Ce processus de synchronisation se produit en arrière-plan. Pour les locataires volumineux comportant des dizaines de milliers d’objets, un ou deux jours peuvent s’écouler avant que la totalité des utilisateurs, des appartenances aux groupes et des informations d’identification soit synchronisée.
>
>

## <a name="next-step"></a>Étape suivante
[Tâche 4 : Mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-update-dns.md)

