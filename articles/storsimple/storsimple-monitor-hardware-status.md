<properties 
   pageTitle="Composants matériels de StorSimple et leur état | Microsoft Azure"
   description="Découvrez comment surveiller les composants matériels de votre appareil StorSimple via le service StorSimple Manager."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Utiliser le service StorSimple Manager pour surveiller les composants et l’état du matériel

## Vue d’ensemble

Cet article décrit les différents composants physiques et logiques de votre appareil StorSimple local. Il explique également comment surveiller l'état des composants de l’appareil à l'aide de la page **Maintenance** du service StorSimple Manager.

La page **Maintenance** affiche l'état du matériel de tous les composants de l’appareil StorSimple.

La liste des composants pour 8100 présente trois sections :

- **Composants partagés** – Ceux-ci ne font pas partie des contrôleurs, tels que les disques, le boîtier, les composants PCM et la température PCM, la tension de ligne et les capteurs de courant de ligne.

- **Composants du contrôleur 0** – Il s’agit des composants qui résident sur le contrôleur 0, tels que le contrôleur, le connecteur et l’expander SAS, les capteurs de température de contrôleur et les différentes interfaces réseau.

- **Composants du contrôleur 1**– Il s’agit des composants qui constituent le contrôleur 1, similaires à ceux détaillés pour le contrôleur 0.

Un appareil 8600 présente des composants supplémentaires qui correspondent au boîtier EBOD (Extended Bunch of Disks). La liste des composants se divise en cinq sections. Trois de ces sections contiennent les composants du boîtier principal et sont identiques à celles décrites pour le modèle 8100. Il y a deux sections supplémentaires relatives au boîtier EBOD :

- **Composants partagés du boîtier EBOD** – Composants présents dans les boîtiers EBOD et PCM qui ne font pas partie du contrôleur EBOD.

- **Composants du contrôleur 0 du boîtier EBOD** – Il s’agit des composants qui résident dans le boîtier EBOD 0, tels que le contrôleur EBOD, le connecteur et l’expander SAS, et les capteurs de température de contrôleur.

- **Composants du contrôleur 1 du boîtier EBOD** – Il s’agit des composants qui constituent le boîtier EBOD 1, similaires à ceux détaillés pour le boîtier EBOD 0.

>[AZURE.NOTE] **La section relative à l’état du matériel n'est pas présente dans la page de maintenance des appareils StorSimpe virtuels (1100).**


## Surveillance de l'état du matériel

Procédez comme suit pour afficher l'état du matériel d'un composant d’appareil :

1. Accédez à**Appareils** et sélectionnez un appareil StorSimple. Accédez au menu de niveau appareil, puis cliquez sur **Maintenance**. 
2. Recherchez la section **État du matériel** et faites un choix parmi les composants disponibles (comme décrit ci-dessus). Cliquez simplement sur la flèche en regard de l'étiquette du composant pour développer la liste et afficher l'état des différents composants de l’appareil. Consultez la [liste détaillée des composants du boîtier principal](#component-list-for-primary-enclosure-of-storsimple-device) et la [liste détaillée des composants détaillées du boîtier EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

2. Utilisez le codage couleur suivant pour interpréter l'état du composant :
	-  **Coche verte** – Composant **Sain** ou **OK**.
	-  **Jaune** – Composant en état d’**avertissement**.
	-  **Point d'exclamation rouge** – Composant présentant un **échec** ou **nécessitant une attention**.
	-  **Blanc avec texte noir** – Composant absent.

3. Si vous rencontrez un composant dont l'état n'est pas **sain**, contactez le Support technique de Microsoft. Si les alertes sont activées sur votre appareil, vous recevrez un message d'alerte. Si vous devez remplacer un composant matériel défaillant, consultez la rubrique [Remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).


## Liste des composants du boîtier principal de l'appareil StorSimple

Le tableau suivant présente les composants physiques et logiques contenus dans le boîtier principal de votre appareil StorSimple local.

|Composant|Module|Type|Emplacement|Unité remplaçable sur site (FRU) ?|Description|
|---|---|---|---|---|---|
|Lecteur à l’emplacement [0-11]|Lecteurs de disque|Physique|Partagé|Oui|Les disques SSD et HDD sont représentés par une ligne dans le boîtier principal.|
|Capteur de température ambiante|Boîtier|Physique|Partagé|Non|Mesure de la température à l’intérieur du châssis.|
|Capteur de température du plan médian|Boîtier|Physique|Partagé|Non|Mesure la température du plan médian.|
|Alarme sonore|Boîtier|Physique|Partagé|Non|Indique si le sous-système d'alarme sonore du châssis est fonctionnel.|
|Boîtier|Boîtier|Physique|Partagé|Oui|Indique la présence d'un châssis.|
|Paramètres du boîtier|Boîtier|Physique|Partagé|Non|Fait référence à la façade avant du châssis.|
|Capteurs de tension de ligne|PCM|Physique|Partagé|Non|L’état de plusieurs capteurs de tension de ligne s’affiche et indique si la tension mesurée se trouve dans la plage de tolérance.|
|Capteurs de courant de ligne|PCM|Physique|Partagé|Non|L’état de plusieurs capteurs de courant de ligne s’affiche et indique si l’intensité mesurée se trouve dans la plage de tolérance.|
|Capteurs de température dans PCM|PCM|Physique|Partagé|Non|L’état de plusieurs capteurs de température tels que les capteurs Inlet et Hotspot s’affiche et indique si la température mesurée se trouve dans la plage de tolérance.|
|Alimentation électrique [0-1]|PCM|Physique|Partagé|Oui|Une ligne représente chacun des blocs d'alimentation dans les deux PCM situés à l'arrière de l’appareil.|
|Refroidissement [0-1]|PCM|Physique|Partagé|Oui|Une ligne représente chacun des quatre ventilateurs résidant dans les deux PCM.|
|Batterie [0-1]|PCM|Physique|Partagé|Oui|Une ligne représente chaque module de batterie de secours inséré dans le PCM.|
|Metis|N/A|Opérateurs logiques|Partagé|N/A|Affiche l'état de la batterie : si elles doivent être rechargées ou arrivent en fin de vie.|
|Cluster|N/A|Opérateurs logiques|Partagé|N/A|Affiche l'état du cluster créé entre les deux modules de contrôleur intégrés.|
|Nœud de cluster|N/A|Opérateurs logiques|Partagé|N/A|Indique l'état du contrôleur en tant que partie du cluster.|
|Quorum du cluster|N/A|Opérateurs logiques||N/A|Indique la présence de l'appartenance de disque majoritaire dans le pool de stockage du disque dur.|
|Espace de données du disque dur|N/A|Opérateurs logiques|Partagé|N/A|Espace de stockage utilisé pour les données dans le pool de stockage de disque dur (HDD).|
|Espace de gestion du disque dur|N/A|Opérateurs logiques|Partagé|N/A|Espace réservé dans le pool de stockage du disque dur pour les tâches de gestion.|
|Espace du quorum du disque dur|N/A|Opérateurs logiques|Partagé|N/A|Espace réservé dans le pool de stockage du disque dur pour le quorum du cluster.|
|Espace de remplacement du disque dur|N/A|Opérateurs logiques|Partagé|N/A|Espace réservé dans le pool de stockage du disque dur pour le remplacement du contrôleur.|
|Espace de données SSD|N/A|Opérateurs logiques|Partagé|N/A|Espace de stockage utilisé pour les données dans le pool de stockage SSD.|
|Espace NVRAM SSD|N/A|Opérateurs logiques|Partagé|N/A|Espace de stockage dans le pool de stockage SSD dédié à la logique de la mémoire NVRAM.|
|Pool de stockage du disque dur|N/A|Opérateurs logiques|Partagé|N/A|Affiche l'état du pool de stockage logique créé à partir de disques durs de périphérique.|
|Pool de stockage SSD|N/A|Opérateurs logiques|Partagé|N/A|Affiche l'état du pool de stockage logique créé à partir de SSD de périphérique.|
|Contrôleur [0-1] [état]|E/S|Physique|Controller|Oui|Affiche l'état du contrôleur, et s'il est en mode actif ou attente au sein du châssis.|
|Capteurs de température du contrôleur|E/S|Physique|Controller|Non|L’état de plusieurs capteurs de température, tels que les capteurs du module E/S, de température du processeur, ainsi que les capteurs DIMM et PCIe, est affiché et indique si la température se situe dans la plage de tolérance.|
|Expander SAS|E/S|Physique|Controller|Non|Indique l'état de l'expandeur SAS (serial attached SCSI), qui est utilisé pour connecter le stockage intégré au contrôleur.|
|Connecteur SAS [0-1]|E/S|Physique|Controller|Non|Indique l'état de chaque connecteur SAS utilisé pour connecter le stockage intégré à l’expander SAS.|
|Interconnexion de plan médian SBB|E/S|Physique|Controller|Non|Indique l'état du connecteur de plan médian, qui est utilisé pour connecter chaque contrôleur au plan médian.|
|Cœur de processeur|E/S|Physique|Controller|Non|Indique l'état des cœurs de processeurs dans chaque contrôleur.|
|Puissance électronique du boîtier|E/S|Physique|Controller|Non|Indique l'état du système d'alimentation utilisé par le boîtier.|
|Diagnostic électronique du boîtier|E/S|Physique|Controller|Non|Indique l'état des sous-systèmes de diagnostic fournis par le contrôleur.|
|Baseboard Management Controller (BMC)|E/S|Physique|Controller|Non|Indique l'état du BMC (baseboard management controller). Il s’agit d’un processeur de service spécialisé qui surveille le périphérique matériel via des capteurs et communique avec l'administrateur système via une connexion indépendante.|
|Ethernet|E/S|Physique|Controller|Non|Indique l'état de chacune des interfaces réseau, autrement dit, des ports de gestion et de données fournis sur le contrôleur.|
|NVRAM|E/S|Physique|Controller|Non|Indique l'état de la mémoire NVRAM, une mémoire vive non volatile avec batterie de secours qui sert à conserver des informations d’application critiques en cas de panne d'alimentation.|

## Liste des composants du boîtier EBOD de l'appareil StorSimple

Le tableau suivant présente les composants physiques et logiques contenus dans le boîtier EBOD de votre appareil StorSimple local.

|Composant|Module|Type|Emplacement|FRU ?|Description|
|---|---|---|---|---|---|
|Lecteur à l’emplacement [0-11]|Lecteurs de disque|Physique|Partagé|Oui|Une ligne représente chacun des disques HDD à l’avant du boîtier EBOD.|
|Capteur de température ambiante|Boîtier|Physique|Partagé|Non|Mesure de la température à l’intérieur du châssis.|
|Capteur de température du plan médian|Boîtier|Physique|Partagé|Non|Mesure la température du plan médian.|
|Alarme sonore|Boîtier|Physique|Partagé|Non|Indique si le sous-système d'alarme sonore du châssis est fonctionnel.|
|Boîtier|Boîtier|Physique|Partagé|Oui|Indique la présence d'un châssis.|
|Paramètres du boîtier|Boîtier|Physique|Partagé|Non|Fait référence à l’OPS ou à la façade avant du châssis.|
|Capteurs de tension de ligne|PCM|Physique|Partagé|Non|L’état de plusieurs capteurs de tension de ligne s’affiche et indique si la tension mesurée se trouve dans la plage de tolérance.|
|Capteurs de courant de ligne|PCM|Physique|Partagé|Non|L’état de plusieurs capteurs de courant de ligne s’affiche et indique si l’intensité mesurée se trouve dans la plage de tolérance.|
|Capteurs de température dans PCM|PCM|Physique|Partagé|Non|L’état de plusieurs capteurs de température tels que les capteurs Inlet et Hotspot s’affiche et indique si la température mesurée se trouve dans la plage de tolérance.|
|Alimentation électrique [0-1]|PCM|Physique|Partagé|Oui|Une ligne représente chacun des blocs d'alimentation dans les deux PCM situés à l'arrière de l’appareil.|
|Refroidissement [0-1]|PCM|Physique|Partagé|Oui|Une ligne représente chacun des quatre ventilateurs résidant dans les deux PCM.|
|Stockage local [HDD]|N/A|Opérateurs logiques|Partagé|N/A|Affiche l'état du pool de stockage logique créé à partir de disques durs de périphérique.|
|Contrôleur [0-1] [état]|E/S|Physique|Controller|Oui|Affiche l'état des contrôleurs du module EBOD.|
|Capteurs de température dans EBOD|E/S|Physique|Controller|Non|L’état de plusieurs capteurs de température de chaque contrôleur s’affiche et indique si la température mesurée se trouve dans la plage de tolérance.|
|Expander SAS|E/S|Physique|Controller|Non|Indique l'état de l'expandeur SAS, qui est utilisé pour connecter le stockage intégré au contrôleur.|
|Connecteur SAS [0-2]|E/S|Physique|Controller|Non|Indique l'état de chaque connecteur SAS utilisé pour connecter le stockage intégré à l’expander SAS.|
|Interconnexion de plan médian SBB|E/S|Physique|Controller|Non|Indique l'état du connecteur de plan médian, qui est utilisé pour connecter chaque contrôleur au plan médian.|
|Puissance électronique du boîtier|E/S|Physique|Controller|Non|Indique l'état du système d'alimentation utilisé par le boîtier.|
|Diagnostic électronique du boîtier|E/S|Physique|Controller|Non|Indique l'état des sous-systèmes de diagnostic fournis par le contrôleur.|
|Connexion au contrôleur de périphérique|E/S|Physique|Controller|Non|Indique l'état de la connexion entre le module E/S du module EBOD et le contrôleur de périphérique.|

## Étapes suivantes
- Pour utiliser le service StorSimple Manager pour gérer votre appareil, consultez [Utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).
 
- Si vous devez résoudre les problèmes d’un composant de l’appareil dont l’état est détérioré ou en échec, consultez [Indicateurs de surveillance StorSimple](storsimple-monitoring-indicators.md).

- Pour remplacer un composant matériel défectueux, consultez [Remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

- Si les problèmes persistent, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).

<!---HONumber=AcomDC_0525_2016-->