---
title: Configurer un proxy web pour un appareil StorSimple | Microsoft Docs
description: "Découvrez comment utiliser Windows PowerShell for StorSimple pour configurer les paramètres du proxy web de votre appareil StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6c2ca351-a7c6-4da6-ab5e-c081e6d08261
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 66ee6ce15e51b14366eac0512c899d1c425c6092
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016

---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurer le proxy web pour votre appareil StorSimple
## <a name="overview"></a>Vue d'ensemble
Ce didacticiel explique comment utiliser Windows PowerShell for StorSimple afin de configurer et d’afficher les paramètres du proxy web pour votre appareil StorSimple. Les paramètres de proxy web sont utilisés par l’appareil StorSimple lors de la communication avec le cloud. Un serveur proxy web est utilisé pour ajouter une autre couche de sécurité, filtrer le contenu, mettre en cache les données afin d’alléger la bande passante voire faciliter l’analyse.

Le proxy web est une configuration facultative pour votre appareil StorSimple. Vous pouvez configurer le proxy web uniquement via Windows PowerShell for StorSimple. La configuration est un processus en deux étapes :

1. Vous configurez tout d’abord les paramètres du proxy web via l’Assistant d’installation ou Windows PowerShell pour les applets de commande StorSimple.
2. Puis, vous activez les paramètres du proxy web configurés via Windows PowerShell pour les applets de commande StorSimple.

Une fois la configuration du proxy web terminée, vous pouvez afficher les paramètres du proxy web configurés dans le service Microsoft Azure StorSimple Manager et Windows PowerShell pour StorSimple. 

Après avoir lu ce didacticiel, vous pourrez :

* Configurer le proxy web à l’aide de l’assistant d’installation et des applets de commande
* Activer le proxy web à l’aide des applets de commande
* Affichage des paramètres de proxy web dans le portail Azure Classic
* Résoudre les erreurs lors de la configuration du proxy web

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurer le proxy web via Windows PowerShell pour StorSimple
Vous utilisez l’une des procédures suivantes pour configurer les paramètres du proxy web :

* L’assistant d’installation, qui vous guide dans les différentes étapes de configuration.
* Les applets de commande Windows PowerShell pour StorSimple

Chacune de ces méthodes est abordée dans les sections suivantes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configuration du proxy web à l’aide de l’assistant d’installation
Vous pouvez utiliser l’assistant d’installation, qui vous guide dans les différentes étapes de configuration du proxy web. Procédez comme suit pour configurer le proxy web sur votre appareil.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Pour configurer le proxy web à l’aide de l’assistant d’installation
1. Dans le menu de la console série, choisissez l’option 1, **Se connecter avec accès total** et spécifiez le **mot de passe administrateur**. Tapez la commande suivante pour démarrer une session de l’assistant d’installation :
   
    `Invoke-HcsSetupWizard`
2. Si c’est la première fois que vous utilisez l’assistant d’installation pour inscrire un appareil, vous devez configurer tous les paramètres réseau requis jusqu’à atteindre la configuration du proxy web. Si votre périphérique est déjà inscrit, vous pouvez accepter tous les paramètres réseau configurés jusqu’à atteindre la configuration du proxy web. Dans l’assistant d’installation, lorsque vous êtes invité à configurer les paramètres du proxy web, entrez **Yes**.
3. Pour l’option **Web Proxy URL**, spécifiez l’adresse IP ou le nom de domaine complet (FQDN) de votre serveur proxy web et le numéro de port TCP que vous souhaitez que votre appareil utilise lors de la communication avec le cloud. Utilisez le format suivant :
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Par défaut, le numéro de port TCP 8080 est spécifié.
4. Choisissez le type d’authentification **NTLM**, **De base** ou **Aucun**. L’option De base est l’authentification la moins sécurisée pour la configuration du serveur proxy. NT LAN Manager (NTLM) est un protocole d’authentification hautement sécurisé et plus complexe qui utilise un système de messagerie à trois voies (parfois quatre si une intégrité supplémentaire est requise) pour authentifier un utilisateur. L’authentification par défaut est NTLM. Pour plus d’informations, voir la rubrique Authentification [de base](http://hc.apache.org/httpclient-3.x/authentication.html) et [NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Dans le service StorSimple Manager, les graphiques d’analyse de l’appareil ne fonctionnent pas lorsque l’authentification de base ou NTLM est activée dans la configuration du serveur proxy pour l’appareil. Pour que les graphiques de surveillance fonctionnent, vous devez vous assurer que l’authentification est définie sur Aucune.**
   > 
   > 
5. Si vous utilisez l’authentification, spécifiez le **nom d’utilisateur du proxy web** et le **mot de passe du proxy web**. Vous devez également confirmer le mot de passe.
   
    ![Configuration du proxy web sur l’appareil StorSimple1](./media/storsimple-configure-web-proxy/IC751830.png)

Si vous inscrivez votre appareil pour la première fois, poursuivez l’inscription. Si votre appareil a déjà été enregistré, l’assistant se ferme. Les paramètres configurés seront enregistrés.

Le proxy web est désormais également activé. Vous pouvez ignorer l’étape [d’activation du proxy web](#enable-web-proxy) et passer directement à [l’affichage des paramètres du proxy web dans le portail Azure Classic](#view-web-proxy-settings-in-the-azure-classic-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configuration du proxy web via les applets de commande Windows PowerShell pour StorSimple
Vous pouvez également configurer les paramètres du proxy web via  les applets de commande Windows PowerShell pour StorSimple Procédez comme suit pour configurer le proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Configuration du proxy web via les applets de commande
1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur de l’appareil**. Le mot de passe par défaut est `Password1`.
2.  À l’invite de commandes, tapez :
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Spécifiez et confirmez le mot de passe lorsque vous y êtes invité, comme indiqué ci-dessous.
   
    ![Configuration du proxy web sur l’appareil StorSimple3](./media/storsimple-configure-web-proxy/IC751831.png)

Le proxy web est désormais configuré et doit être activé.

## <a name="enable-web-proxy"></a>Activation du proxy web
Le proxy web est désactivé par défaut. Après avoir configuré les paramètres du proxy web sur votre appareil StorSimple, vous devez utiliser Windows PowerShell pour StorSimple afin d’activer les paramètres du proxy web.

> [!NOTE]
> **Cette étape ne sera pas nécessaire si vous avez utilisé l’assistant d’installation pour configurer le proxy web. Le proxy web est automatiquement activé par défaut après une session de l’assistant d’installation.**
> 
> 

Procédez comme suit dans Windows PowerShell pour StorSimple afin d’activer le proxy web sur votre appareil :

#### <a name="to-enable-web-proxy"></a>Activation du proxy web
1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur de l’appareil**. Le mot de passe par défaut est `Password1`.
2.  À l’invite de commandes, tapez :
   
    `Enable-HcsWebProxy`
   
    Vous avez maintenant activé la configuration du proxy web sur votre appareil StorSimple.
   
    ![Configuration du proxy web sur l’appareil StorSimple4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Affichage des paramètres de proxy web dans le portail Azure Classic
Les paramètres du proxy web sont configurés via l’interface Windows PowerShell et ne peuvent pas être modifiés dans le portail Azure Classic. Vous pouvez toutefois afficher ces paramètres configurés dans le portail Azure Classic. Procédez comme suit pour afficher le proxy web.

#### <a name="to-view-web-proxy-settings"></a>Affichage des paramètres du proxy web
1. Accédez à **Service StorSimple Manager > Appareils**. Cliquez sur un appareil, puis passez à la page **Configurer**.
2. Faites défiler la page **Configurer** vers le bas jusqu’à la section des **Paramètres du proxy web**. Vous pouvez afficher les paramètres configurés du proxy web sur votre appareil StorSimple comme indiqué ci-dessous.
   
    ![Affichage du proxy web dans le Portail de gestion](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)

## <a name="errors-during-web-proxy-configuration"></a>Erreurs lors de la configuration du proxy web
Si les paramètres du proxy web ont été configurés correctement, les messages d’erreur s’afficheront dans Windows PowerShell pour StorSimple. Le tableau suivant décrit certains de ces messages d’erreur, leurs causes probables et les actions recommandées.

| Numéro de série | Code d’erreur HRESULT | Cause principale possible | Action recommandée |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |La commande est exécutée à partir du contrôleur passif et ne peut pas communiquer avec le contrôleur actif. |Exécutez cette commande depuis le contrôleur actif. Pour exécuter la commande à partir du contrôleur passif, vous devez résoudre la connectivité entre le contrôleur passif et le contrôleur actif. Vous devez contacter le support technique Microsoft si cette connectivité est interrompue. |
| 2. |0x800710dd - L’identificateur de l’opération n’est pas valide |Les paramètres du proxy ne sont pas pris en charge sur un appareil virtuel StorSimple. |Les paramètres du proxy ne sont pas pris en charge sur un appareil virtuel StorSimple. Ils peuvent uniquement être configurés sur un appareil StorSimple physique. |
| 3. |0x80070057 - Paramètre non valide |L’un des paramètres fournis pour les paramètres de proxy n’est pas valide. |L’URI n’est pas fourni dans le format correct. Utilisez le format suivant : `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - Serveur RPC indisponible |La cause première est l’une des suivantes :</br></br>Le cluster n’est pas disponible.</br></br>Le service Chemin de données n'est pas en cours d'exécution.</br></br>La commande est exécutée à partir du contrôleur passif et ne peut pas communiquer avec le contrôleur actif. |Veuillez contacter le support technique Microsoft pour vous assurer que le cluster est disponible et que le service Datapath est en cours d’exécution.</br></br>Exécutez la commande à partir du contrôleur actif. Si vous souhaitez exécuter la commande à partir du contrôleur passif, vous devez vous assurer que le contrôleur passif peut communiquer avec le contrôleur actif. Vous devez contacter le support technique Microsoft si cette connectivité est interrompue. |
| 5. |0x800706be - Échec de l’appel RPC |Le cluster est arrêté. |Veuillez contacter le support technique Microsoft pour vous assurer que le cluster est disponible. |
| 6. |0x8007138f - Ressource de cluster introuvable |Impossible de trouver la ressource de cluster du service de plateforme. Cela peut se produire lorsque l’installation ne s’est pas déroulée correctement. |Vous devrez peut-être effectuer une réinitialisation de votre appareil aux paramètres d’usine. Vous devrez peut-être créer une ressource de plateforme. Pour les étapes suivantes, veuillez contacter le support technique Microsoft. |
| 7. |0x8007138c - La ressource de cluster n’est pas en ligne |Les ressources de plateforme ou datapath ne sont pas en ligne. |Veuillez contacter le support technique Microsoft pour vous assurer que les ressources de service datapath et de plateforme sont en ligne. |

> [!NOTE]
> * La liste des messages d’erreur ci-dessus n’est pas exhaustive. 
> * Les erreurs liées aux paramètres du proxy web ne s’affichent pas dans le portail Azure Classic de votre service StorSimple Manager. Si un problème survient au niveau du proxy web après la configuration, l’état de l’appareil affiche **Hors connexion** dans le portail Azure Classic.|
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Si vous rencontrez des problèmes lors du déploiement de votre appareil ou la configuration des paramètres de proxy web, voir [Dépannage du déploiement de votre appareil StorSimple](storsimple-troubleshoot-deployment.md).
* Pour apprendre à utiliser le service StorSimple Manager, voir [Utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).


