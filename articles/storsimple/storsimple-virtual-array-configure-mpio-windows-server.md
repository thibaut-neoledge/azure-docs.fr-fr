---
title: "Configurer MPIO sur un hôte connecté à StorSimple Virtual Array | Microsoft Docs"
description: "Décrit comment configurer Multipath I/O (MPIO) pour votre tableau virtuel StorSimple connecté à un hôte exécutant Windows Server 2012 R2."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: alkohli
ms.openlocfilehash: c75c6ed40754aee964e2b68f4f569dc1422507f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurer MPIO (Multipath I/O) sur un hôte Windows Server pour la baie virtuelle StorSimple
## <a name="overview"></a>Vue d'ensemble
Cet article décrit comment installer la fonctionnalité Multipath I/O (MPIO) sur votre hôte Windows Server, appliquer des paramètres de configuration spécifiques pour les volumes StorSimple uniquement, puis vérifiez MPIO pour les volumes StorSimple. La procédure suppose que votre baie virtuelle StorSimple 1200 avec deux interfaces réseau est connectée à un hôte Windows Server avec deux interfaces réseau. Les informations contenues dans cet article s’appliquent uniquement à la baie virtuelle. Pour plus d’informations sur les appareils StorSimple série 8000, accédez à [Configuration de MPIO pour un hôte StorSimple](storsimple-configure-mpio-windows-server.md). 

La fonctionnalité MPIO dans Windows Server aide à créer des configurations de stockage à tolérance de pannes et haut niveau de disponibilité. MPIO utilise les composants de chemin d’accès physique redondants (adaptateurs, câbles et commutateurs) pour créer des chemins d’accès logiques entre le serveur et l’appareil de stockage. En cas de défaillance de composant à l’origine de l’échec du chemin d’accès logique, la logique de gestion multivoie utilise un autre chemin pour l’E/S afin que les applications puissent toujours accéder à leurs données. De plus, selon votre configuration, MPIO peut également améliorer les performances en rééquilibrant la charge sur ces chemins d’accès. Pour plus d’informations, consultez la [Présentation de MPIO](https://technet.microsoft.com/library/cc725907.aspx "Présentation de MPIO and features").

Pour la haute disponibilité de votre solution StorSimple, configurez MPIO sur les hôtes Windows Server connectés à votre tableau virtuel StorSimple 1200. Les serveurs hôtes peuvent alors tolérer l’échec d’un lien, d’un réseau ou d’une interface. 

Procédez comme suit pour configurer MPIO : 

* Conditions préalables à la configuration
* Étape 1 : installer MPIO sur l’hôte Windows Server
* Étape 2 : configurer MPIO pour les volumes StorSimple
* Étape 3 : monter des volumes StorSimple sur l’hôte

Chacune des étapes ci-dessus est abordée dans les sections suivantes.

## <a name="prerequisites"></a>Composants requis
Cette section détaille la configuration requise de l’hôte Windows Server et de votre baie virtuelle.

### <a name="on-windows-server-host"></a>Sur l’hôte Windows Server
* Assurez-vous que votre hôte Windows Server possède 2 interfaces réseau activées.

### <a name="on-storsimple-virtual-array"></a>Sur le tableau virtuel StorSimple
* La baie virtuelle doit être configurée comme un serveur iSCSI. Pour en savoir plus, consultez [Configurer une baie virtuelle comme un serveur iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md). Une ou plusieurs interfaces réseau doivent être activées sur la baie.   
* Les interfaces réseau de votre baie virtuelle doivent être accessibles à partir de l’hôte Windows Server.
* Un ou plusieurs volumes doivent être créés sur votre baie virtuelle StorSimple. Pour plus d’informations, consultez [Ajouter un volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) à votre tableau virtuel StorSimple. Dans cette procédure, nous avons créé 3 volumes (1 localement épinglé et 2 volumes à plusieurs niveaux, comme indiqué ci-dessous) sur le tableau virtuel.
  
    ![mpio0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuration matérielle pour le tableau virtuel StorSimple
La figure ci-dessous illustre la configuration matérielle pour la gestion multivoie haute disponibilité et à équilibrage de charge pour votre hôte Windows Server et votre instance StorSimple Virtual Array utilisée dans cette procédure.

![configuration matérielle mpio](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

Comme indiqué dans la figure précédente :

* Votre tableau virtuel StorSimple approvisionné sur Hyper-V est un appareil actif à nœud unique configuré comme un serveur iSCSI.
* Deux interfaces de réseau virtuel sont activées sur votre baie. Dans l’interface utilisateur web locale de votre tableau virtuel, vérifiez que deux interfaces réseau sont activées en accédant à **Paramètres réseau** comme indiqué ci-dessous :
  
    ![Interfaces réseau activées sur 1200](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    Notez les adresses IPv4 des interfaces réseau activées (Ethernet, Ethernet 2 par défaut) et enregistrez-les pour une utilisation ultérieure sur l’hôte.
* Deux interfaces réseau sont activées sur votre hôte Windows Server. Si les interfaces connectées pour l’hôte et la baie sont sur le même sous-réseau, 4 chemins sont disponibles. C’est le cas dans cette procédure. Toutefois, si chaque interface réseau sur la baie et l’interface de l’hôte sont sur un autre sous-réseau IP (non routable), alors seuls 2 chemins sont disponibles.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Étape 1 : installer MPIO sur l’hôte Windows Server
MPIO est une fonctionnalité facultative sur Windows Server et n’est pas installé par défaut. Il doit être installé en tant que fonctionnalité via le Gestionnaire de serveur. Pour installer cette fonctionnalité sur votre hôte Windows Server, effectuez la procédure suivante.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Étape 2 : configurer MPIO pour les volumes StorSimple
MPIO doit être configuré afin d’identifier les volumes StorSimple. Pour configurer MPIO pour reconnaître des volumes StorSimple, procédez comme suit.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Étape 3 : monter des volumes StorSimple sur l’hôte
Une fois MPIO configuré sur Windows Server, le ou les volumes créés sur la baie StorSimple peuvent être montés et peuvent alors tirer parti de MPIO pour la redondance. Pour monter un volume, effectuez les étapes suivantes.

#### <a name="to-mount-volumes-on-the-host"></a>Montage de volumes sur l’hôte
1. Ouvrez la fenêtre **Propriétés de l’initiateur iSCSI** sur l’hôte Windows Server. Accédez à **Gestionnaire de serveur > Tableau de bord > Outils > Initiateur iSCSI** .
2. Dans la boîte de dialogue **Propriétés de l’initiateur iSCSI**, cliquez sur **Détection**, puis cliquez sur **Détecter un portail cible**.
3. Dans la boîte de dialogue **Détecter un portail cible** , procédez comme suit :
   
    1. Entrez l’adresse IP de la première interface réseau activée sur votre baie virtuelle StorSimple. Par défaut, il s’agit d’ **Ethernet**. 
    2. Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI**.
     
    > [!IMPORTANT]
    > Si vous utilisez un réseau privé pour les connexions iSCSI, entrez l’adresse IP du port DATA connecté au réseau privé.
     
4. Répétez les étapes 2 et 3 pour une deuxième interface réseau (par exemple, Ethernet 2) sur votre baie. 
5. Sélectionnez l’onglet **Cibles** dans la boîte de dialogue **Propriétés de l’initiateur iSCSI**. Pour votre baie virtuelle, vous devez voir la surface de chaque volume en tant que cible sous **Cibles découvertes**. Dans ce cas, trois cibles (correspondant aux trois volumes) doivent être découvertes.
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. Cliquez sur **Connexion** pour établir une session iSCSI avec votre tableau virtuel StorSimple. Une boîte de dialogue **Se connecter à la cible** s’affiche. Sélectionnez la case à cocher **Activer la prise en charge de plusieurs chemins d’accès** . Cliquez sur **Avancé**.
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. Dans la boîte de dialogue **Paramètres avancés** , procédez comme suit :                                        
   
    1. Dans la liste déroulante **Adaptateur local**, sélectionnez **Initiateur Microsoft iSCSI**.
    2. Dans la liste déroulante **IP de l’initiateur** , sélectionnez l’adresse IP de l’hôte.
    3. Dans la liste déroulante d’adresses IP du **portail cible** , sélectionnez l’adresse IP de l’interface de la baie.
    4. Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI**.
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. Cliquez sur **Propriétés**. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. Dans la boîte de dialogue **Propriétés**, cliquez sur **Ajouter une session**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. Dans la boîte de dialogue **Se connecter à la cible**, sélectionnez la case à cocher **Activer la prise en charge de plusieurs chemins d’accès**. Cliquez sur **Avancé**.
11. Dans la boîte de dialogue **Paramètres avancés** :                                        
    
    1. Dans la liste déroulante **Adaptateur local** , sélectionnez Initiateur Microsoft iSCSI.

    2. Dans la liste déroulante **IP de l’initiateur** , sélectionnez l’adresse IP correspondant à l’hôte. Dans ce cas, vous connectez deux interfaces réseau sur la baie à une seule interface réseau sur l’hôte. Par conséquent, cette interface est identique à celle fournie pour la première session.

    3. Dans la liste déroulante d’ **adresses IP du portail cible** , sélectionnez l’adresse IP de la deuxième interface de données activée sur la baie.

    4. Cliquez sur **OK** pour revenir à la boîte de dialogue Propriétés de l’initiateur iSCSI. Vous avez ajouté une deuxième session à la cible.
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. Après avoir ajouté les sessions souhaitées (chemins d’accès), dans la boîte de dialogue **Propriétés de l’initiateur iSCSI**, sélectionnez la cible et cliquez sur **Propriétés**. Sous l’onglet Sessions de la boîte de dialogue **Propriétés** , notez les quatre identificateurs de session qui correspondent aux permutations de chemin d’accès possibles. Pour annuler une session, activez la case à cocher en regard d’un identificateur de session, puis cliquez sur **Déconnexion**.

    6. Pour afficher les appareils présentés dans les sessions, sélectionnez l’onglet **Périphériques** . Pour configurer la stratégie MPIO pour un appareil sélectionné, cliquez sur **MPIO**.

    7. La boîte de dialogue **Détails** s’affiche. Sous l’onglet **MPIO**, vous pouvez sélectionner les paramètres **Stratégie d’équilibrage de charge** appropriés. Vous pouvez également afficher le type de chemin d’accès **Actif** ou **Veille**.
12. Répétez les étapes 8 à 11 pour ajouter des sessions supplémentaires (chemins) à la cible. Avec deux interfaces sur l’hôte et deux sur la baie virtuelle, vous pouvez ajouter un total de quatre sessions pour chaque cible. 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. Vous devez répéter ces étapes pour chaque volume (surfaces en tant que cible).
    
    ![mpio15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. Ouvrez **Gestion de l’ordinateur** en accédant à **Gestionnaire de serveur > Tableau de bord > Gestion de l’ordinateur**. Dans le volet gauche, cliquez sur **Stockage > Gestion des disques**. Le ou les volumes créés sur la baie virtuelle StorSimple visibles pour cet hôte s’affichent sous **Gestion des disques** en tant que nouveaux disques.
15. Initialisez le disque et créez un nouveau volume. Pendant le processus de formatage, sélectionnez une taille d’unité d’allocation de 64 Ko. Répétez le processus pour tous les volumes disponibles.
    
    ![Gestion des disques](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. Sous **Gestion des disques**, cliquez avec le bouton droit sur le **Disque** et sélectionnez **Propriétés**.
17. Dans la boîte de dialogue **Propriétés de l’appareil de disque à chemins multiples**, cliquez sur l’onglet **MPIO**.
    
    ![Propriétés du disque](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. Dans la section **Nom DSM**, cliquez sur **Détails** et vérifiez que les paramètres par défaut sont définis. Les paramètres par défaut sont les suivants :
    
    * Période de vérification du chemin d’accès = 30
    * Nombre de tentatives = 3
    * Période de suppression d’objets de périphériques physiques = 20
    * Intervalle avant nouvelle tentative = 1
    * Vérification du chemin activée = désactivé.
    
    > [!NOTE]
    > **Ne modifiez pas les paramètres par défaut.**
   
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [utilisation du service StorSimple Device Manager pour gérer votre tableau virtuel StorSimple](storsimple-virtual-array-manager-service-administration.md).

