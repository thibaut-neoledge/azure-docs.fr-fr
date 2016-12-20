---
title: "Modifier la configuration de l’appareil StorSimple | Microsoft Docs"
description: "Explique comment utiliser le service StorSimple Manager pour reconfigurer un appareil StorSimple qui a déjà été déployé."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/29/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2dfc682fa54577ec5ab9d8a1e21ea82f035cc41e


---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilisation du service StorSimple Manager pour modifier la configuration de votre appareil StorSimple
## <a name="overview"></a>Vue d’ensemble
La page **Configurer** du portail Azure Classic contient tous les paramètres reconfigurables d’un appareil StorSimple géré par un service StorSimple Manager. Ce didacticiel explique comment utiliser la page **Configurer** pour effectuer les tâches au niveau de l’appareil :

* Modification des paramètres de l’appareil 
* Modification des paramètres d’heure 
* Modification des paramètres DNS 
* Modification des interfaces réseau
* Échange ou réaffectation d’adresses IP

## <a name="modify-device-settings"></a>Modification des paramètres de l’appareil
Les paramètres de l’appareil incluent le nom convivial de l’appareil et sa description.

Un appareil StorSimple qui est connecté au service StorSimple Manager se voit attribuer un nom par défaut. Celui-ci reflète généralement le numéro de série de l’appareil. Par exemple, voici comment interpréter un nom d’appareil par défaut constitué de 15 caractères, tel que 8600-SHX0991003G44HT :

* **8600** : indique le modèle de l’appareil.
* **SHX** : indique le site de fabrication.
* **0991003** : indique un produit spécifique.
* **G44HT**: les 5 derniers chiffres sont incrémentés pour créer des numéros de série uniques. Il ne s’agit pas nécessairement d’une suite.

Vous pouvez utiliser le portail Azure Classic pour modifier le nom de l’appareil et lui attribuer le nom convivial unique de votre choix. Ce nom peut contenir tout type de caractère et ne doit pas dépasser 64 caractères.

Vous pouvez aussi définir une description de l’appareil. Cette description vise généralement à identifier le propriétaire et l’emplacement physique de l’appareil. Le champ de description doit contenir moins de 256 caractères.

## <a name="modify-time-settings"></a>Modification des paramètres d’heure
Votre appareil doit synchroniser l’heure pour s’authentifier auprès du fournisseur de services de stockage cloud. Sélectionnez votre fuseau horaire dans la liste déroulante et spécifiez au maximum deux serveurs NTP (Network Time Protocol). Le serveur NTP principal est obligatoire. Il est spécifié durant la configuration de l’appareil dans Windows PowerShell pour StorSimple. Vous pouvez choisir le serveur NTP par défaut de Windows Server, **time.windows.com**. Vous pouvez consulter la configuration du serveur NTP principal dans le portail Azure Classic, mais vous devez utiliser l’interface Windows PowerShell pour la modifier.

La configuration du serveur NTP secondaire est facultative. Vous pouvez utiliser le portail Azure Classic pour configurer un serveur NTP secondaire. 

Au moment de configurer le serveur NTP, vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet. Quand vous spécifiez un serveur NTP public, vous devez veiller à ce que vos pare-feu réseau et autres dispositifs de sécurité soient configurés pour autoriser le trafic NTP à transiter vers et à partir du réseau externe. Si le trafic NTP bidirectionnel n’est pas autorisé, vous devez utiliser un serveur NTP interne (un contrôleur de domaine Windows assure cette fonction). Si votre appareil ne peut pas synchroniser l’heure, il ne pourra peut-être pas communiquer avec votre fournisseur de stockage cloud.

Pour consulter la liste des serveurs NTP publics, accédez au [site web des serveurs NTP](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Que se passe-t-il si l’appareil est déployé dans un autre fuseau horaire ?
Si l’appareil est déployé dans un autre fuseau horaire, le fuseau horaire de l’appareil change. Sachant que toutes les stratégies de sauvegarde utilisent le fuseau horaire de l’appareil, les stratégies de sauvegarde s’ajustent automatiquement en fonction du nouveau fuseau horaire. Aucune intervention de l’utilisateur n’est nécessaire.

## <a name="modify-dns-settings"></a>Modification des paramètres DNS
Un serveur DNS est utilisé au moment où votre appareil tente de communiquer avec votre fournisseur de services de stockage cloud. Pour bénéficier d’une haute disponibilité, vous devez configurer à la fois le serveur DNS principal et le serveur DNS secondaire pendant le déploiement initial de l’appareil. Pour reconfigurer le serveur DNS principal, vous devez utiliser l’interface Windows PowerShell sur votre appareil StorSimple.

Pour modifier le serveur DNS secondaire, vous pouvez utiliser le portail Azure Classic.

## <a name="modify-network-interfaces"></a>Modification des interfaces réseau
Votre appareil dispose de six interfaces réseau, dont quatre sont de type 1 Gigabit Ethernet, les deux autres étant de type 10 Gigabit Ethernet. Ces interfaces sont désignées par les intitulés DATA 0 à DATA 5. Ainsi, DATA 0, DATA 1, DATA 4 et DATA 5 sont des interfaces réseau 1 Gigabit Ethernet, tandis que DATA 2 et DATA 3 sont de type 10 Gigabit Ethernet.

Configurez les **Paramètres de l’interface réseau** pour chaque interface à utiliser. Pour garantir une haute disponibilité, votre appareil doit de préférence disposer d’au moins deux interfaces iSCSI et de deux interfaces compatible cloud. Nous vous recommandons, sans que cela soit obligatoire, de désactiver les interfaces non utilisées.

Vous devez configurer une adresse IP virtuelle pour chaque interface réseau que vous configurez.

Par défaut, DATA 0 est compatible cloud. Quand vous configurez DATA 0, vous devez aussi configurer deux adresses IP fixes, une pour chaque contrôleur. Ces adresses IP fixes permettent d’accéder directement aux contrôleurs de l’appareil et s’avèrent utiles pour installer des mises à jour sur l’appareil ou pour accéder aux contrôleurs à des fins de dépannage.

Dans StorSimple série 8000 Update 1, la métrique de routage de DATA 0 est définie au plus bas. Par conséquent, si votre appareil exécute StorSimple série 8000 Update 1, tout le trafic cloud sera acheminé via DATA 0. Prenez-en note au cas où vous disposeriez de plusieurs interfaces réseau compatibles cloud sur votre appareil StorSimple.

> [!NOTE]
> Les adresses IP fixes du contrôleur servent à traiter les mises à jour de l’appareil. Par conséquent, les adresses IP fixes doivent être routables et être en mesure de se connecter à Internet.
> 
> 

Pour chaque interface réseau, les paramètres suivants sont affichés :

* **Vitesse** : paramètre non configurable par l’utilisateur. DATA 0, DATA 1, DATA 4 et DATA 5 sont toujours des interfaces réseau 1 Gigabit Ethernet, tandis que DATA 2 et DATA 3 sont des interfaces 10 Gigabit Ethernet.
  
  > [!NOTE]
  > La vitesse et le mode duplex sont toujours autonégociés. Les trames Jumbo ne sont pas prises en charge.
  > 
  > 
* **État de l’interface** : une interface peut être activée ou désactivée. Si elle est activée, l’appareil essaie de l’utiliser. Nous vous recommandons d’activer uniquement les interfaces qui sont connectées au réseau et utilisées. Désactivez celles que vous n’utilisez pas.
* **Type d’interface** : ce paramètre vous permet d’isoler le trafic iSCSI du trafic de stockage cloud. Ce paramètre peut avoir l’une des valeurs suivantes :
  
  * **Compatible cloud** : l’appareil utilise cette interface pour communiquer avec le cloud.
  * **Compatible iSCSI** : l’appareil utilise cette interface pour communiquer avec l’hôte iSCSI.
    
    Nous vous recommandons d’isoler le trafic iSCSI du trafic de stockage cloud. Notez aussi que si votre hôte se trouve dans le même sous-réseau que votre appareil, il est inutile d’affecter une passerelle. En revanche, si votre hôte se trouve dans un sous-réseau différent de celui de votre appareil, vous devez affecter une passerelle.
* **Adresse IP** : il peut s’agir d’une adresse IPv4 ou IPv6 ou les deux à la fois. Les familles d’adresses IPv4 et IPv6 sont prises en charge pour les interfaces réseau de l’appareil. Quand vous utilisez IPv4, spécifiez une adresse IP 32 bits (*xxx.xxx.xxx.xxx*) en notation décimale à point. Quand vous utilisez IPv6, indiquez simplement un préfixe à 4 chiffres. Une adresse 128 bits sera alors générée automatiquement pour l’interface réseau de votre appareil à partir de ce préfixe.
* **Sous-réseau** : ce paramètre fait référence au masque de sous-réseau et est configuré via l’interface Windows PowerShell.
* **Passerelle** : il s’agit de la passerelle par défaut que cette interface doit utiliser quand elle tente de communiquer avec les nœuds situés dans le même espace d’adressage IP (sous-réseau). La passerelle par défaut doit se trouver dans le même espace d’adressage (sous-réseau) que l’adresse IP de l’interface, ce qui est détermine par le masque de sous-réseau.
* **Adresse IP fixe** : ce champ est seulement disponible pendant la configuration de l’interface DATA 0. Pour certaines opérations telles que les mises à jour ou le dépannage de l’appareil, vous serez peut-être amené à vous connecter directement au contrôleur de l’appareil. L’adresse IP fixe permet d’accéder à la fois au contrôleur actif et au contrôleur passif de votre appareil.

Vous pouvez reconfigurer le contrôleur 0 et le contrôleur 1 par le biais du portail Azure Classic.

> [!NOTE]
> * Pour garantir un bon fonctionnement, vérifiez la vitesse et le mode duplex de l’interface sur le commutateur auquel chaque interface de l’appareil est connectée. Les interfaces du commutateur doivent négocier avec Gigabit Ethernet (1 000 Mbits/s) ou être configurées en conséquence et être en mode duplex intégral. Les interfaces fonctionnant à des vitesses plus lentes ou en mode semi-duplex entraîneront des problèmes de performances.
> * Pour limiter au maximum les perturbations et les temps d’arrêt, nous vous recommandons d’activer portfast sur chacun des ports du commutateur auxquels l’interface réseau iSCSI de votre appareil doit se connecter. C’est la garantie que la connectivité réseau sera rapidement établie en cas de basculement.
> 
> 

## <a name="swap-or-reassign-ips"></a>Échange ou réaffectation d’adresses IP
Pour l’heure, si une adresse IP virtuelle est affectée à une interface réseau du contrôleur et qu’elle est en cours d’utilisation (par un même appareil ou un autre appareil du réseau), le contrôleur opère un basculement. Par conséquent, vous devez suivre la procédure appropriée si vous échangez les adresses IP virtuelles de l’interface réseau de l’appareil, car vous vous retrouverez avec des adresses IP en double.

Pour échanger ou réaffecter les adresses IP virtuelles d’interfaces réseau, procédez comme suit :

#### <a name="to-reassign-ips"></a>Pour réaffecter des adresses IP
1. Effacez l’adresse IP des deux interfaces.
2. Une fois les adresses IP effacées, affecter les nouvelles adresses IP aux interfaces respectives.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [configurer MPIO pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).




<!--HONumber=Nov16_HO3-->


