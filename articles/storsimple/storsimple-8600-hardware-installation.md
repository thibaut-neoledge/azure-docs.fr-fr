<properties 
   pageTitle="Installation de votre appareil StorSimple 8600"
   description="Explique comment déballer, monter en rack et brancher les câbles de votre appareil StorSimple 8600."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/08/2015"
   ms.author="v-sharos" />

# Installation de votre appareil StorSimple 8600

## Vue d'ensemble
Microsoft Azure StorSimple 8600 est un appareil composé d’un boîtier principal et d’un boîtier EBOD. Ce didacticiel explique comment déballer, monter en rack et brancher les câbles de votre appareil StorSimple 8600 avant de configurer son logiciel.

## Déballage de votre appareil StorSimple 8600

La procédure suivante explique de façon claire et détaillée comment déballer votre appareil de stockage StorSimple 8600. Cet appareil est livré dans deux emballages distincts, l’un pour le boîtier principal et l’autre pour le boîtier EBOD.

### Préparation du déballage de votre appareil

Avant de déballer l’appareil, lisez les informations suivantes.

>[AZURE.WARNING]![icône de poids lourd](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
>
> 1. Compte tenu du poids de l’appareil, deux personnes doivent être disponibles pour vous aider à le porter et à le manipuler. En effet, un appareil complet peut peser jusqu’à 32 kg.
>
> 2. Placez le carton sur une surface plane et droite.

Ensuite, procédez comme suit pour déballer votre appareil.

#### Pour déballer votre appareil

1. Vérifiez que les cartons et le polystyrène ne comportent pas de trace d’impacts, de coupures, d’infiltrations d’eau ou tout autre type de dégâts. Si le carton ou le reste de l’emballage vous semble trop endommagé, ne l’ouvrez pas. Contactez le support technique Microsoft pour savoir si l’appareil est en état de marche.

2. Ouvrez les deux cartons et déballez le boîtier principal et le boîtier EBOD. L’illustration suivante représente tous les éléments composant l’emballage de votre appareil de stockage.

    ![Déballage de votre appareil de stockage](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
 
    **Figure 1 : vue de votre appareil de stockage déballé**

     Étiquette | Description 
     ----- | -------------
     1 | Carton d’emballage
     2 | Câbles SAS (dans le carton des accessoires et des câbles)
     3 | Polystyrène de protection inférieur
     4 | Appareil
     5 | Polystyrène de protection supérieur
     6 | Carton contenant les accessoires

3. Une fois les cartons déballés, vérifiez que vous disposez des éléments suivants :

  - 1 boîtier principal (le boîtier principal et le boîtier EBOD sont dans deux cartons distincts) 
  - 1 boîtier EBOD
  - 4 câbles d’alimentation, 2 dans chaque carton
  - 2 câbles SAS (pour raccorder le boîtier principal au boîtier EBOD)
  - 1 câble Ethernet croisé
  - 2 câbles de console série
  - 1 convertisseur de série USB pour l’accès en série
  - 4 adaptateurs QSFP-SFP+ uniques à utiliser avec les interfaces réseau 10 Gigabit Ethernet
  - 2 kits de montage en rack (4 rails latéraux avec matériel de montage, 2 pour chaque boîtier), 1 dans chaque carton
  - Documentation de mise en route

    Si vous n’avez pas reçu l’un des éléments ci-dessus, contactez le support technique Microsoft.

L’étape suivante consiste à monter votre appareil en rack.

## Montage en rack de votre appareil StorSimple 8600

Respectez les étapes suivantes pour installer votre appareil de stockage StorSimple 8600 en un rack standard de 19 pouces avec poteaux avant et arrière. Cet appareil est fourni avec deux boîtiers : un boîtier principal et un boîtier EBOD. Tous deux doivent être montés en rack.

L’installation se déroule en plusieurs étapes, chacune étant décrite dans les procédures suivantes.

### Préparation du site

Les boîtiers doivent être installés dans un rack standard de 19 pouces équipé de poteaux avant et arrière. Procédez comme suit pour préparer l’installation en rack.

#### Pour préparer le site pour le montage en rack

1. Vérifiez que le boîtier principal et le boîtier EBOD sont posés en toute sécurité sur une surface de travail plate, stable et de niveau (ou similaire).

2. Vérifiez que le site où vous envisagez de monter l’appareil dispose d’une alimentation secteur standard provenant d’une source indépendante ou d’une unité de distribution de l’alimentation (PDU) en rack avec un onduleur (UPS).

3. Assurez-vous qu’un emplacement de 4U (2 x 2U) est disponible sur le rack dans lequel vous avez l’intention de monter les boîtiers.

>[AZURE.WARNING]![icône de poids lourd](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
> 
> Compte tenu du poids de l’appareil, deux personnes doivent être disponibles pour vous aider à le porter et à le manipuler. En effet, un boîtier complet peut peser jusqu’à 32 kg.

### Configuration requise pour le rack

Les boîtiers sont conçus pour une installation dans une armoire à rack standard de 19 pouces présentant les caractéristiques suivantes :

- Profondeur minimale de 27,84 pouces entre les poteaux du rack
- Poids maximal de 32 kg pour l’appareil
- Contre-pression maximale de 5 pascals (colonne d’eau de 0,5 mm)

### Kit du rail de montage en rack

Un ensemble de rails de montage compatible avec l’armoire à rack de 19 pouces est fourni. Les rails ont été testés pour supporter le poids maximal d’un boîtier. Ces rails permettent également d’installer plusieurs boîtiers sans perte d’espace dans le rack. Installez d’abord le boîtier EBOD.

#### Pour installer le boîtier EBOD sur les rails

1. Placez le boîtier sur la surface de travail et retirez les capuchons d’embase avant gauche et droit. Les capuchons sont simplement enfoncés sur les embases.

2. En général, ces rails sont installés en usine. S’ils ne le sont pas, installez les glissières du rail gauche et celles du rail droit sur les côtés du châssis du boîtier. Six vis métriques permettent de les fixer de chaque côté. Pour faciliter l’orientation, les mentions **LH – Front** (avant gauche) et **RH – Front** (avant droit) sont indiquées sur les glissières et l’extrémité qui est apposée à l’arrière du boîtier est effilée.

    ![Fixation des glissières de rail sur le châssis du boîtier](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Figure 2 : fixation des glissières de rail sur les côtés du boîtier**

    Étiquette | Description
    ----- | -----------
    1 | M 3 x 4 vis à tête ronde
    2 | Glissières du châssis
 
3. Fixez le rail gauche et le rail droit sur les éléments verticaux de l’armoire à rack. Les crochets sont marqués **LH** (gauche), **RH** (droite) et **This side up** (Ce côté vers le haut) afin de vous aider à orienter correctement les éléments.

4. Recherchez les ergots à l’avant et à l’arrière du rail. Développez le rail afin qu’il soit suffisamment long pour relier les poteaux du rack, puis insérez les ergots dans les trous des éléments verticaux du poteau arrière du rack. Vérifiez que le rail est de niveau.

5. Sécurisez le rail sur l’élément vertical du rack à l’aide des deux vis métriques fournies. Utilisez l’une des vis à l’avant et l’autre à l’arrière.

6. Répétez ces étapes pour l’autre rail.

     ![Fixation des glissières de rail sur l’armoire à rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Figure 3 : fixation des rails au rack**

     Étiquette | Description
     ----- | -----------
     1 | Vis de serrage
     2 | Ouverture carrée pour vis du poteau avant du rack
     3 | Ergots de positionnement du rail avant gauche
     4 | Vis de serrage
     5 | Ergots de positionnement du rail avant droit

### Montage du boîtier EBOD dans le rack 

À l’aide des rails du rack qui viennent d’être installés, procédez comme suit pour monter le boîtier EBOD dans le rack.

#### Montage du boîtier EBOD

1. Avec l’aide d’un assistant, soulevez le boîtier et alignez-le avec les rails du rack. 

2. Insérez le boîtier avec précaution dans les rails, puis poussez-le complètement dans l’armoire à rack.

    ![Insertion de l’appareil dans le rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Figure 4 : montage du boîtier dans le rack**

3. Sécurisez le boîtier dans le rack en installant une vis cruciforme fournie sur chaque embase, à gauche et à droite.

4. Placez les capuchons sur les embases et appuyez dessus pour les mettre en place.

     ![Installation des capuchons d’embase](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Figure 5 : installation des capuchons d’embase**
 
     Étiquette | Description
     ----- | -----------
     1 | Vis de fixation du boîtier


### Montage du boîtier principal dans le rack

Une fois que vous avez terminé le montage du boîtier EBOD, respectez les mêmes étapes pour monter le boîtier principal.

> [AZURE.NOTE]
> 
> - Vous pouvez laisser quelques emplacements vides dans le rack entre le boîtier principal et le boîtier EBOD. 
> - Utilisez le câble SAS de 2 m fourni pour connecter le boîtier principal au boîtier EBOD.
> - Il n’y a pas de contraintes de positionnement relatif de l’unité principale par rapport à l’unité EBOD. Par conséquent, le boîtier principal peut être placé dans l’emplacement supérieur et le boîtier EBOD dans l’emplacement inférieur ou vice versa.

L’étape suivante consiste à brancher l’alimentation, le réseau et l’accès en série à votre appareil.

## Branchement des câbles de votre appareil StorSimple 8600

Les procédures suivantes expliquent comment brancher les câbles d’alimentation, de réseau et d’accès en série sur votre appareil StorSimple 8600.

### Composants requis

Avant de commencer à raccorder votre appareil, vous devez disposer des éléments suivants :

- Votre boîtier principal et votre boîtier EBOD, totalement déballés
- 4 câbles d’alimentation (2 par boîtier) fournis avec votre appareil
- 2 câbles SAS fournis avec l’appareil pour connecter le boîtier EBOD au boîtier principal
- Accès à 2 unités de distribution de l’alimentation (PDU) (recommandé)
- Câbles réseau
- Câbles série fournis
- Convertisseur de série USB avec le pilote approprié installé sur votre ordinateur (si nécessaire)
- Adaptateurs QSFP-SFP+ uniques fournis à utiliser avec les interfaces réseau 10 Gigabit Ethernet
- [Transmetteurs, câbles et commutateurs pris en charge pour les interfaces réseau 10 Gigabit Ethernet](https://msdn.microsoft.com/library/azure/dn891474.aspx) 

### Branchement des câbles SAS

Votre appareil possède un boîtier principal et un boîtier EBOD. Ces boîtiers doivent être connectés l’un à l’autre à l’aide de câbles SAS.

Pour ce faire, vous devez dans un premier temps identifier le boîtier principal et le boîtier EBOD.

#### Pour identifier le boîtier principal et le boîtier EBOD

1. Identifiez le boîtier principal et le boîtier EBOD en examinant les fonds de panier respectifs. Reportez-vous à la figure suivante.

    ![Boîtier principal et boîtier EBOD](./media/storsimple-8600-hardware-installation/HCSBackplaneofprimaryandEBODenclosure.png)

    **Figure 6 : dos du boîtier principal et du boîtier EBOD**

     Étiquette | Description
     ----- | -----------
     1 | Boîtier principal
     2 | Boîtier EBOD


2. Recherchez les numéros de série sur les deux boîtiers. L’étiquette du numéro de série est apposée sur la languette arrière de chaque boîtier. Les numéros de série doivent être identiques sur les deux boîtiers. Contactez immédiatement le support technique Microsoft si les numéros de série ne correspondent pas. Reportez-vous à la figure suivante pour vous aider à trouver les numéros de série.

    ![Emplacement du numéro de série](./media/storsimple-8600-hardware-installation/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)

    **Figure 7 : vue arrière du boîtier avec emplacement du numéro de série**

     Étiquette | Description
     ----- | -----------
     1 | Languette du boîtier


Ensuite, utilisez les câbles SAS fournis pour connecter le boîtier EBOD au boîtier principal.

#### Pour connecter le boîtier principal et le boîtier EBOD

1. Identifiez les quatre ports SAS (Serial Attached SCSI) sur le boîtier principal et le boîtier EBOD. Les ports SAS sont indiqués par la mention EBOD sur le boîtier principal et par la mention CTRL sur le boîtier EBOD, comme illustré sur la figure 8.

    ![Emplacement des ports SAS](./media/storsimple-8600-hardware-installation/HCSSAScablingforyourdevice.png)

    **Figure 8 : emplacement des ports SAS sur le boîtier principal et le boîtier EBOD**

     Étiquette | Description
     ----- | -----------
     Une | Boîtier principal
     B | Boîtier EBOD
     1 | Contrôleur 0
     2 | Contrôleur 1
     3 | Contrôleur 0 du boîtier EBOD
     4 | Contrôleur 1 du boîtier EBOD
     5/6 | Ports SAS sur le boîtier principal (mention EBOD)
     7/8 | Ports SAS sur le boîtier EBOD (mention CTRL)

2. Utilisez les câbles SAS fournis pour connecter les ports EBOD aux ports CTRL.

    Le port EBOD sur le contrôleur 0 doit être raccordé au port CTRL du contrôleur 0 du boîtier EBOD. Le port EBOD sur le contrôleur 1 doit être raccordé au port CTRL du contrôleur 1 du boîtier EBOD. Reportez-vous à la figure 8.

### Branchement des câbles d’alimentation

Le boîtier principal et le boîtier EBOD contiennent tous deux des modules redondants d’alimentation et de refroidissement (PCM). Dans chaque boîtier, chaque PCM doit être installé et connecté à une autre source d’alimentation pour garantir une haute disponibilité.

Procédez comme suit pour brancher les câbles d’alimentation de votre appareil.

#### Pour brancher les câbles d’alimentation

1. Assurez-vous que les commutateurs d’alimentation sont en position d’arrêt sur tous les PCM.

2. Pour le boîtier principal, branchez les câbles d’alimentation aux deux PCM. Les câbles d’alimentation sont représentés en rouge dans le schéma suivant.

3. Vérifiez que le deux PCM du boîtier principal utilisent des sources d’alimentation distinctes.

4. Fixez les câbles d’alimentation aux unités de distribution de l’alimentation du rack, comme indiqué sur le schéma suivant.

5. Répétez les étapes 2 à 4 pour le boîtier EBOD.

6. Activez le boîtier EBOD en mettant en marche tous les commutateurs d’alimentation du PCM.

7. Assurez-vous que le boîtier EBOD est activé en vérifiant que les voyants de son contrôleur sont allumés (voyants verts à l’arrière du châssis).

8. À présent, activez le boîtier principal en mettant en marche tous les commutateurs d’alimentation du PCM.

9. Assurez-vous que le système fonctionne en vérifiant que les voyants du contrôleur sont allumés.

10. Assurez-vous que la connexion entre le contrôleur EBOD et le contrôleur de l’appareil est active en vérifiant les voyants SAS (4 voyants du port SAS) sur le contrôleur du boîtier EBOD. Les voyants SAS doivent être verts.

> [AZURE.IMPORTANT]Pour garantir une haute disponibilité de votre système, il est conseillé de se conformer strictement au schéma de branchement des câbles d’alimentation représenté ci-dessous.

   ![Schéma de branchement des câbles d’alimentation](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforPower.png)

   **Figure 9 : branchement des câbles d’alimentation de votre appareil**

     Label | Description
     ----- | -----------
     1     | Primary enclosure
     2     | PCM 0
     3     | PCM 1
     4     | Controller 0
     5     | Controller 1
     6     | EBOD controller 0
     7     | EBOD controller 1
     8     | EBOD enclosure
     9     | PDUs
 

### Branchement des câbles réseau

Votre appareil est dans une configuration de veille active : à un moment donné, un module de contrôleur est actif et procède au traitement de toutes les opérations de disque et du réseau pendant que l’autre module de contrôleur est en veille. En cas de défaillance du contrôleur actif, celui qui est en veille s’active immédiatement et poursuit le traitement de toutes les opérations de disque et de mise en réseau.

Pour prendre en charge ce basculement de contrôleur redondant, vous devez brancher les câbles réseau de votre appareil comme indiqué dans les étapes suivantes.

#### Pour brancher les câbles de connexion réseau

1. Votre appareil possède six interfaces réseau sur chaque contrôleur : quatre ports Ethernet de 1 Gbit/s et deux de 10 Gbit/s. Reportez-vous au schéma suivant pour identifier les ports de données sur le fond de panier de votre appareil.

     ![Fond de panier de l’appareil 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Figure 10 : dos de votre appareil avec ports de données**
 
     Étiquette | Description
     ------- | -----------
     0/1/4/5 | Interfaces réseau de 1 Gigabit Ethernet
     2/3 | Interfaces réseau de 10 Gigabit Ethernet
     6 | Ports série

2. Pour une haute disponibilité, l’appareil nécessite au moins deux connexions pour chaque contrôleur.
    1. Le port de données DATA 0 est automatiquement activé et configuré via la console série de l’appareil. Hormis DATA 0, un autre port de données doit également être configuré via le portail de gestion.
    2. Identifiez les interfaces réseau identiques sur chaque contrôleur. Par exemple, si vous choisissez de connecter DATA 0 et DATA 3 pour l’un des contrôleurs, vous devez connecter les ports de données correspondants DATA 0 et DATA 3 sur l’autre contrôleur. 

3. Pour une haute disponibilité, vérifiez que vous connectez les éléments suivants :
   1. Interfaces identiques sur chaque contrôleur avec le réseau approprié pour garantir la disponibilité en cas de défaillance d’un contrôleur.
   2. Interfaces de chaque contrôleur avec au moins deux commutateurs différents afin de garantir la disponibilité en cas de défaillance d’un commutateur.
   3. Port de données DATA 0 au LAN principal (réseau avec accès à Internet). Les autres ports de données peuvent être connectés au segment SAN/iSCSI LAN (VLAN) du réseau, en fonction du rôle prévu.

Configurez au moins une interface réseau pour l’accès au cloud et une pour iSCSI. Pour une haute disponibilité et des performances, configurez deux paires d’interfaces réseau sur chaque contrôleur. Consultez le schéma suivant pour le branchement des câbles réseau. (La configuration réseau minimale est indiquée par des lignes bleues pleines. Pour une haute disponibilité et des performances, la configuration supplémentaire requise est représentée par des lignes en pointillés.)

![Câble réseau de votre appareil 4U](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Figure 11 : branchement des câbles réseau de votre appareil**

Étiquette | Description
----- | -----------
Une | LAN avec accès à Internet
B | Contrôleur 0
C | PCM 0
D | Contrôleur 1
E | PCM 1
F | Contrôleur 0 du boîtier EBOD
G | Contrôleur 1 du boîtier EBOD
H/I | Hôtes (par exemple, les serveurs de fichiers)
0-5 | Interfaces réseau
6 | Boîtier principal
7 | Boîtier EBOD

### Branchement des câbles de port série

Procédez comme suit pour brancher les câbles de port série de votre appareil.

#### Pour brancher les câbles de connexion en série

1. Votre appareil possède un port série sur chaque contrôleur, qui est identifié par une icône en forme de clé. Reportez-vous à la figure 10 pour localiser les ports série sur le fond de panier de votre appareil.

2. Identifiez le contrôleur actif sur le fond de panier de votre appareil. Un voyant bleu clignotant indique que le contrôleur est actif.

3. Utilisez le câble série fourni (si nécessaire, le convertisseur USB de série pour votre ordinateur portable) et connectez votre console ou votre ordinateur (avec émulation de terminal vers l’appareil) au port série du contrôleur actif.

4. Installez les pilotes USB de série (fournis avec l’appareil) sur votre ordinateur.

5. Configurez la connexion série comme suit :
   - 115 200 bauds
   - 8 bits de données
   - 1 bit d’arrêt
   - Aucune parité
   - Valeur de contrôle du flux réglée sur **Aucun**

6. Vérifiez que la connexion fonctionne en appuyant sur Entrée sur la console. Un menu de la console série doit apparaître.

> [AZURE.NOTE]**Gestion en service réduit :** lorsque l’appareil est installé dans un centre de données distant ou dans une salle informatique avec un accès limité, assurez-vous que les connexions série sur les deux contrôleurs sont toujours connectées à un commutateur de console série ou à un équipement similaire. Cela permet de commander à distance hors bande et de prendre en charge les opérations en cas d’interruption du réseau ou en cas de défaillance inattendue.

Vous avez terminé le branchement des câbles d’alimentation, d’accès réseau et de connexion en série de votre appareil. L’étape suivante consiste à configurer le logiciel sur votre appareil.

## Étapes suivantes

Vous êtes maintenant prêt à procéder au [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).
 

<!---HONumber=July15_HO2-->