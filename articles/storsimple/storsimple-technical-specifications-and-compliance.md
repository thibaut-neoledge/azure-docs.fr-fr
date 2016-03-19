<properties 
   pageTitle="Caractéristiques techniques StorSimple | Microsoft Azure"
   description="Décrit les caractéristiques techniques et les informations de conformité aux normes réglementaires des composants matériels StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/20/2016"
   ms.author="alkohli" />

# Caractéristiques techniques et conformité de l’appareil StorSimple

## Vue d'ensemble

Les composants matériels de l’appareil Microsoft Azure StorSimple sont conformes aux caractéristiques techniques et normes réglementaires présentées dans cet article. Les caractéristiques techniques décrivent les modules d’alimentation et de refroidissement (PCM), les lecteurs de disque et les boîtiers. Les informations de conformité couvrent les éléments tels que les normes internationales, la sécurité et les émissions et le câblage.

## Caractéristiques du module d’alimentation et de refroidissement  

L’appareil StorSimple intègre deux modules d’alimentation et de refroidissement (PCM) conformes SBB à double ventilateur de 100-240 V. Cette configuration fournit une alimentation redondante. Si un PCM échoue, l’appareil continue à fonctionner normalement sur l’autre PCM jusqu’au remplacement du module défaillant.

Le boîtier EBOD utilise un PCM de 580 W et le boîtier principal un PCM de 764 W. Les tableaux suivants répertorient les caractéristiques techniques des PCM.

| Caractéristique | PCM 580 W (EBOD) | PCM 764 W (principal) |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Puissance de sortie maximale | 580 W | 764 W |
| Fréquence | 50/60 Hz | 50/60 Hz |
| Sélection de la plage de tension | Détermination automatique : 90 à 264 V CA, 47/63 Hz | Détermination automatique : 90 à 264 V CA, 47/63 Hz |
| Courant d’appel maximal | 20 A | 20 A |
| Correction du facteur de puissance | > 95 % de la tension d’entrée nominale | > 95 % de la tension d’entrée nominale |
| Harmonique | Conforme à la norme EN61000-3-2 | Conforme à la norme EN61000-3-2 |
| Sortie | Tension en mode veille 5 V à 2,0 A | Tension en mode veille 5 V à 2,7 A |
| | + 5 V à 42 A | + 5 V à 40 A |
| | + 12 V à 38 A | + 12 V à 38 A |
| Enfichable à chaud | Oui | Oui |
| Commutateurs et voyants LED | Interrupteur marche/arrêt et quatre voyants LED d’état | Interrupteur marche/arrêt et six voyants LED d’état |
| Refroidissement du boîtier | Ventilateurs axiaux avec vitesse variable réglable | Ventilateurs axiaux avec vitesse variable réglable |

 
## Statistiques sur la consommation énergétique  

Le tableau suivant répertorie les données de consommation énergétique standard (les valeurs réelles peuvent différer des valeurs publiées) des différents modèles d’appareil StorSimple.
 
 Conditions | 240 V CA | 240 V CA | 240 V CA | 110 V CA | 110 V CA | 110 V CA 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Ventilateurs lents, lecteurs inactifs | 1,45 A |0,31 kW | 1 057,76 BTU/heure | 3,19 A | 0,34 kW | 1 160,13 BTU/heure 
 Ventilateurs lents, lecteurs en cours d’accès | 1,54 A | 0,33 kW | 1 126,01 BTU/heure | 3,27 A | 0,36 kW | 1 228,37 BTU/heure 
 Ventilateurs rapides, lecteurs inactifs, deux blocs d’alimentation sous tension | 2,14 A | 0,49 kW | 1 671,95 BTU/heure | 4,99 A | 0,54 kW | 1 842,56 BTU/heure 
 Ventilateurs rapides, lecteurs inactifs, un bloc d’alimentation sous tension, un inactif | 2,05 A | 0,48 kW | 1 637,83 BTU/heure | 4,58 A | 0,50 kW | 1 706,07 BTU/heure 
 Ventilateurs rapides, lecteurs en cours d’accès, deux blocs d’alimentation sous tension | 2,26 A | 0,51 kW | 1 740,19 BTU/heure | 4,95 A | 0,54 kW | 1 842,56 BTU/heure 
 Ventilateurs rapides, lecteurs en cours d’accès, un bloc d’alimentation sous tension, un inactif | 2,14 A |0,49 kW | 1 671,95 BTU/heure | 4,81 A | 0,53 kW | 1 808,44 BTU/heure 

## Caractéristiques des lecteurs de disque  

L’appareil StorSimple prend en charge jusqu’à 12 disques SAS (Serial Attached SCIS) de 3,5 pouces. Les lecteurs réels peuvent être une combinaison de disques SSD ou de lecteurs de disque dur (HDD), selon la configuration du produit. Les 12 emplacements de disque se trouvent dans une configuration de 3 par 4 à l’avant du boîtier. Le boîtier EBOD permet le stockage de 12 autres lecteurs de disque. Il s’agit toujours de disques durs.

## Dimensions et poids des boîtiers  

Les tableaux suivants répertorient les dimensions et les caractéristiques de poids des différents boîtiers.

### Dimensions de boîtier
Le tableau suivant répertorie les dimensions du boîtier en millimètres et en pouces.

|Boîtier |Millimètres |Pouces |
|----------|------------|-------| 
| Hauteur |87,9 | 3,46 |
| Largeur entre brides de montage | 483 | 19,02 |
| Largeur du boîtier | 443 | 17,44 |
| Profondeur de la bride de montage avant à l’extrémité du corps du boîtier | 577 | 22,72 |
| Profondeur, du panneau de commande à l’extrémité la plus lointaine du boîtier | 630,5 | 24,82 |
| Profondeur, de la bride de montage à l’extrémité la plus lointaine du boîtier |	603 | 23,74 |

### Poids du boîtier  

Selon la configuration, un boîtier principal entièrement chargé peut peser de 21 à 33 kg et doit donc être manipulé par deux personnes.
 
| Boîtier | Poids |
|-----------|--------| 
| Poids maximal (dépend de la configuration) |30 à 33 kg |
| Vide (aucun disque monté) |21 à 23 kg |

## Caractéristiques ambiantes pour le boîtier  

Cette section répertorie les caractéristiques liées à l’environnement du boîtier. La température, l’humidité, l’altitude, les chocs, les vibrations, l’orientation, la sécurité et la compatibilité électromagnétique (CEM) sont inclus dans cette catégorie.

### Température et humidité

| Boîtier | Plage de températures ambiantes | Taux d’humidité ambiante | Température maximale du thermomètre mouillé |
|------------------|----------------------------|---------------------------|--------------------|
| En fonctionnement | 5 °C à 35 °C (41 °F à 95 °F) | 20 à 80 % sans condensation | 28 °C (82 °F) |
| Hors fonctionnement | -40 °C à 70 °C (40 °F à 158 °F) | 5 à 100 % sans condensation | 29 °C (84 °F) |

### Ventilation, altitude, chocs, vibrations, orientation, sécurité et CEM
 
| Boîtier | Caractéristiques en fonctionnement |
|--------------------|---------------------------------------------------------------------------| 
| Ventilation | La ventilation du système s’effectue de l’avant vers l’arrière. Le système doit être utilisé avec une installation basse pression à échappement vers l’arrière. La contre-pression créée par les portes de rack et les obstacles ne doit pas dépasser 5 pascals (0,5 mm de colonne d’eau). |
| Altitude, en fonctionnement | -30 mètres à 3 045 mètres (-100 pieds à 10 000 pieds) avec la température de service maximale réduite de 5 °C à plus de 7 000 mètres. |
| Altitude, hors fonctionnement | -305 mètres à 12 192 mètres (-1 000 pieds à 40 000 pieds) |
| Chocs, en fonctionnement | 5 g 10 ms ½ sinus |
| Chocs, hors fonctionnement | 30 g 10 ms ½ sinus |
| Vibrations, en fonctionnement | Moyenne quadratique 0,21 g, 5-500 Hz aléatoire |
| Vibrations, hors fonctionnement | Moyenne quadratique 1,04 g, 2-200 Hz aléatoire |
| Vibrations, déplacement | 3 g 2-200 Hz sinus |
| Orientation et montage | Montage en rack de 19" (2 unités EIA) |
| Rails de rack | Convient pour une profondeur minimale de rack de 700 mm (31,50 pouces), conforme à la norme CEI 297 |
| Sécurité et homologations |	CE et UL EN 61000-3, CEI 61000-3, UL 61000-3 |
| CEM | EN55022 (CISPR - A), FCC A |

## Conformité aux normes internationales
L’appareil Microsoft Azure StorSimple est conforme aux normes internationales suivantes :

- CE - EN 60950-1  
- Rapport CB sur CEI 60950-1  
- UL et cUL sur UL 60950-1  

## Conformité aux normes de sécurité  

L’appareil Microsoft Azure StorSimple est conforme aux normes de sécurité suivantes :

- Homologation du type de produit du système : UL, cUL, CE  
- Conformité aux normes de sécurité : UL 60950, CEI 60950, EN 60950  

## Conformité électromagnétique 

L’appareil Microsoft Azure StorSimple est conforme aux niveaux de sécurité suivants :

### Émissions

L’appareil est conforme aux normes CEM pour les niveaux d’émissions par conduction et par rayonnement.

- Niveaux maximums d’émissions par conduction : CFR 47 partie 15 classe A, EN55022 classe A, CISPR classe A  
- Niveaux maximums d’émissions par rayonnement : CFR 47 partie 15B classe A, EN55022 classe A, CISPR classe A   

### Harmonique et papillotement  

L’appareil est conforme à la norme EN61000-3-2/3.

### Niveaux maximums d’immunité  
L’appareil est conforme à la norme EN55024.

## Conformité du cordon d’alimentation secteur
  
L’ensemble fiche-cordon d’alimentation doit être conforme aux normes applicables dans le pays où l’appareil est utilisé et bénéficier de certifications de sécurité acceptables dans ce pays. Les tableaux suivants répertorient les normes applicables aux États-Unis et en Europe.

### Cordons d’alimentation secteur - États-Unis (doivent être répertoriés par un laboratoire d’essai reconnu nationalement)

| Composant | Caractéristique |
| --------------- | ----------------------------------------------------------------- | 
| Type de cordon | SV ou SVT, 18 AWG minimum, 3 conducteurs, longueur maximale 2 mètres |
| Fiche | Fiche avec mise à la terre NEMA 5-15P, avec une tension nominale de 120 V, 10 A ; ou CEI 320 C14, 250 V, 10 A |
| Prise | CEI 320 C-13, 250 V, 10 A |

### Cordons d’alimentation secteur - Europe

| Composant | Caractéristique |
| --------------- | ----------------------------------------------------------------- | 
| Type de cordon | Harmonisé, H05-VVF-3G1.0 |
| Prise | CEI 320 C-13, 250 V, 10 A |

## Câbles réseau pris en charge  

Pour les interfaces réseau 10 GbE, DATA 2 et DATA 3, consultez la [liste des modules et des câbles réseau pris en charge](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## Étapes suivantes

Vous êtes désormais prêt à déployer un appareil StorSimple dans votre centre de données. Pour plus d'informations, consultez [Déploiement de votre appareil local](storsimple-deployment-walkthrough-u2.md).

<!---HONumber=AcomDC_0121_2016-->