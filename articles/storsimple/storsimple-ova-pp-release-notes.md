<properties 
   pageTitle="Notes de publication pour StorSimple Virtual Array | Microsoft Azure"
   description="Décrit les problèmes critiques non résolus et les solutions possibles pour StorSimple Virtual Array."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Notes de publication pour StorSimple Virtual Array (version préliminaire) 

## Vue d’ensemble

Les notes de publication suivantes identifient les problèmes critiques non résolus pour la version préliminaire publique de décembre 2015 de Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel local StorSimple ou appareil virtuel StorSimple).

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez les informations contenues dans les notes de publication avant de déployer votre appareil virtuel StorSimple.

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array est en version préliminaire et est destiné à des fins d'évaluation et de planification du déploiement. L'installation de cette version préliminaire dans un environnement de production n'est pas prise en charge. 
>
>- Si vous rencontrez des problèmes avec StorSimple Virtual Array, publiez-les sur le [forum MSDN de StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

Le tableau suivant récapitule les problèmes connus de cette version.

| N°| Fonctionnalité | Problème | Solution de contournement/commentaires |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | Mises à jour | Les appareils virtuels créés dans la version préliminaire ne peuvent pas être mis à jour vers une version à disponibilité générale prise en charge.| Ces appareils virtuels doivent être basculés vers la version à disponibilité générale à l'aide d'un flux de travail de récupération d'urgence (DR).|
| **2.** | Partages ou volumes à plusieurs niveaux | Le verrouillage de la plage d'octets pour les applications qui fonctionnent avec les volumes à plusieurs niveaux StorSimple n'est pas prise en charge. Si le verrouillage de la plage d'octets est activé, la hiérarchisation StorSimple ne fonctionnera pas. | Les mesures recommandées incluent :<ul><li>Désactiver le verrouillage de la plage d'octets dans la logique de votre application.</li><li> Choisir de placer les données de cette application dans des volumes localement épinglés par opposition à des volumes à plusieurs niveaux.</li>*Inconvénient* : si le verrouillage de la plage d'octets est activé avec l'utilisation de volumes localement épinglés, n'oubliez pas que le volume localement épinglé peut être en ligne avant même que la restauration ne soit terminée. Dans ce cas, si une restauration est en cours, vous devez attendre que l'opération se termine.|
| **3.** | Partages à plusieurs niveaux | L'utilisation de fichiers volumineux peut entraîner montée en charge de niveau lente. | Lorsque vous utilisez des fichiers volumineux, vérifiez que la taille du plus grand fichier est inférieure à 3 % de la taille du partage.|
| **4.** | Partages ou volumes épinglés localement | La capacité utilisable d'un volume ou d'un partage épinglé localement correspond à 85-90 % de la capacité déployée. La plage de 10 à 15 % restante est réservée aux métadonnées. | Vous devrez configurer un volume de plus grande taille pour obtenir la capacité utilisable souhaitée. Par exemple, pour une capacité utilisable de 1 To, vous devrez peut-être configurer un volume de 1,15 To.|
| **5.** | Interface utilisateur web locale | Si les fonctionnalités de sécurité améliorées sont activées dans Internet Explorer (IE ESC), certains pages de l'interface utilisateur web locale, comme **Dépannage** ou **Maintenance**, peuvent ne pas fonctionner correctement. Les boutons sur ces pages peuvent également ne pas fonctionner. | Désactivez les fonctionnalités de sécurité améliorées d'Internet Explorer.|
| **6.** | Interface utilisateur web locale | La localisation de l'interface utilisateur web locale n'est pas prise en charge dans cette version. | Ceci sera implémenté dans une version ultérieure.|
| **7.** | Interface utilisateur web locale | Sur une machine virtuelle Hyper-V, les interfaces réseau de l'interface utilisateur web sont affichées sous forme d'interfaces 10 Gbits/s. | Ce comportement est le reflet de Hyper-V. Hyper-V affiche toujours 10 Gbits/s pour les cartes de réseau virtuel.|                                                                                                 
| **8.** | Récupération d'urgence | La récupération d'urgence d'un serveur de fichiers ne peut s'effectuer que dans le même domaine que celui de l'appareil source. La récupération d'urgence vers un appareil cible dans un autre domaine n'est pas prise en charge dans cette version. | Ceci sera implémenté dans une version ultérieure.|
| **9.**| Capacité utilisée pour les partages| Vous pouvez constater la consommation du partage en l'absence de données sur le partage. Ceci est dû au fait que la capacité utilisée pour les partages inclut des métadonnées. | |                                                                                                                                                                                                                                                         
| **10.** | Paramètres de fuseau horaire| Si le fuseau horaire est modifié pour un appareil inscrit, la modification n'est pas répercutée dans les tests de diagnostic exécutés via l'interface utilisateur web locale. | Ce problème sera résolu dans une version ultérieure.|
| **11.** | Paramètres réseau | Lorsqu'une adresse IP statique est configurée pour une interface réseau via l'interface utilisateur web, l'adresse IP est modifiée. Toutefois, l'adresse IP du serveur DNS est également modifiée en une adresse de site local IPv6. | Ce problème sera résolu dans une version ultérieure.|
| **12.** | Azure PowerShell | Les appareils virtuels StorSimple ne peuvent pas être gérés via Azure PowerShell dans cette version. | La gestion des appareils virtuels doit être effectuée par le biais du portail Azure Classic et l'interface utilisateur web locale.|
| **13.** | Travaux | La progression du travail n'est pas précise. La progression du travail peut passer directement de 0 % à 100 %. | Ceci sera résolu dans une version ultérieure.|
| **14.** | Disque de données configuré | Une fois que vous avez configuré un disque de données d'une certaine spécifiée et créé l'appareil virtuel StorSimple correspondant, vous ne devez pas développer, ni réduire le disque de données. Toute tentative de ce type peut entraîner une perte de toutes les données au niveau local de l'appareil. | |
| **15.** | Contenu de l'aide | Les rubriques du contenu de l'aide ne sont pas disponibles.| Toute la documentation est disponible par le biais du Centre de téléchargement Microsoft et le site de documentation Azure. |   

<!---HONumber=AcomDC_0121_2016-->