---
title: 'Services de domaine Azure AD : Activer les services de domaine Azure AD | Microsoft Docs'
description: Prise en main des services de domaine Azure Active Directory
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
ms.date: 10/19/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 64bb5f7e78a6e48faf3487da780597b25891a2fa


---
# <a name="enable-azure-ad-domain-services"></a>Activer Azure AD Domain Services
## <a name="task-3-enable-azure-ad-domain-services"></a>Tâche 3 : activer les services de domaine Azure AD
Cette tâche consiste à activer Azure AD Domain Services pour votre répertoire. Pour activer Azure AD Domain Services pour votre annuaire, effectuez les étapes de configuration suivantes.

1. Accédez au **portail Azure Classic** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Sélectionnez le nœud **Active Directory** dans le volet gauche.
3. Sélectionnez le client Azure AD (annuaire) pour lequel vous souhaitez activer les services de domaine Azure AD.
   
    ![Sélectionner un annuaire Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Cliquez sur l'onglet **Configurer** .
   
    ![Configurer l’onglet de l’annuaire](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Faites défiler jusqu’à la section **services de domaine**.
   
    ![Section de configuration des services de domaine](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)
6. Définissez l’option **Activer les services de domaine pour cet annuaire** sur **OUI**. Quelques options de configuration supplémentaires pour Azure AD Domain Services s’affichent alors dans la page.
   
    ![Activer les services de domaine](./media/active-directory-domain-services-getting-started/enable-domain-services.png)
   
   > [!NOTE]
   > Quand vous activez Azure AD Domain Services pour votre client, Azure AD génère et stocke les hachages d’informations d’identification Kerberos et NTLM nécessaires pour l’authentification des utilisateurs.
   > 
   > 
7. Spécifiez le **Nom de domaine DNS des services de domaine**.
   
   * Le nom de domaine par défaut de l’annuaire (qui se termine par le suffixe de domaine **.onmicrosoft.com** ) est sélectionné par défaut.
   * La liste contient tous les domaines qui ont été configurés pour votre annuaire Azure AD, y compris les domaines vérifiés et non vérifiés que vous configurez sous l’onglet « Domaines ».
   * Vous pouvez également saisir un nom de domaine personnalisé. Dans cet exemple, nous avons saisi un nom de domaine personnalisé « contoso100.com ».
     
     > [!WARNING]
     > Assurez-vous que le préfixe associé au nom de domaine spécifié (par exemple, « contoso100 » dans le nom de domaine « contoso100.com ») présente moins de 15 caractères. Vous ne pouvez pas créer un domaine des services de domaine Azure Active Directory avec un préfixe de domaine comportant plus de 15 caractères.
     > 
     > 
8. Vérifiez que le nom de domaine DNS choisi pour le domaine géré n’existe pas au sein du réseau virtuel. Plus précisément, vérifiez que :
   
   * Vous disposez déjà d’un domaine présentant le nom de domaine DNS au sein du réseau virtuel
   * Le réseau virtuel que vous avez sélectionné dispose d’une connexion VPN avec votre réseau local et vous disposez d’un domaine présentant le même nom de domaine DNS sur votre réseau local
   * Il existe un service cloud portant le même nom sur le réseau virtuel
9. L’étape suivante consiste à sélectionner un réseau virtuel dans lequel vous souhaitez que les services de domaine Azure AD soient disponibles. Dans la liste déroulante **Connecter les services de domaine à ce réseau virtuel**, sélectionnez le réseau virtuel et le sous-réseau dédié que vous avez créés.
   
   * Assurez-vous que le réseau virtuel que vous avez spécifié appartient à une région Azure prise en charge par les services de domaine Azure Active Directory. Pour connaître les régions Azure dans lesquelles Azure AD Domain Services est disponible, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) .
   * Les réseaux virtuels appartenant à une région où Azure AD Domain Services n’est pas pris en charge n’apparaissent pas dans la liste déroulante.
   * Utilisez un sous-réseau dédié au sein du réseau virtuel pour les services de domaine Azure AD. Veillez à ne pas sélectionner le sous-réseau de passerelle. Consultez [Mise en réseau - Éléments à prendre en compte](active-directory-ds-networking.md). 
   * De même, les réseaux virtuels créés à l’aide du logiciel Azure Resource Manager ne sont pas affichés dans cette liste. Les réseaux virtuels basés sur Resource Manager ne sont pas pris en charge par Azure AD Domain Services pour le moment.
10. Pour activer Azure AD Domain Services, cliquez sur **Enregistrer** dans le volet des tâches en bas de la page.
11. La page affiche l’état « En attente... » le temps qu’Azure AD Domain Services soit activé pour votre annuaire.
    
    ![Activer les services de domaine : état d’attente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)
    
    > [!NOTE]
    > Les services de domaine Azure AD offrent une haute disponibilité pour votre domaine géré. Une fois Azure AD Domain Services activé, les adresses IP auxquelles les services de domaine sont disponibles sur le réseau virtuel s’affichent une par une. La deuxième adresse IP s’affiche sous peu, dès que le service active la haute disponibilité pour votre domaine. Une fois que la haute disponibilité est configurée et active pour votre domaine, deux adresses IP apparaissent normalement dans la section **services de domaine** de l’onglet **Configurer**.
    > 
    > 
12. Après environ 20 à 30 minutes, la première adresse IP à laquelle les services de domaine sont disponibles sur votre réseau virtuel apparaît dans le champ **Adresse IP** de la page **Configurer**.
    
    ![Services de domaine activés : première adresse IP configurée](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
13. Une fois que la haute disponibilité est opérationnelle pour votre domaine, deux adresses IP apparaissent dans la page. Votre domaine géré est disponible sur votre réseau virtuel sélectionné à ces deux adresses IP. Notez ces adresses IP afin de pouvoir mettre à jour les paramètres DNS pour votre réseau virtuel. Cette étape permet aux machines virtuelles du réseau virtuel de se connecter au domaine en vue de diverses opérations telles que la jonction de domaine.
    
    ![Services de domaine activés : les deux adresses IP configurées](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> En fonction de la taille de votre client Azure AD (nombre d’utilisateurs, de groupes, etc.), la synchronisation avec votre domaine géré peut prendre du temps. Ce processus de synchronisation se produit en arrière-plan. Pour les clients volumineux comportant des dizaines de milliers d’objets, un ou deux jours peuvent s’écouler avant que la totalité des utilisateurs, des appartenances aux groupes et des informations d’identification soit synchronisée.
> 
> 

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 : mettre à jour les paramètres DNS pour le réseau virtuel Azure
La tâche de configuration suivante consiste à [mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md).




<!--HONumber=Dec16_HO2-->


