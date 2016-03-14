<properties 
   pageTitle="Guide de dépannage d’Azure Mobile Engagement - Analyse" 
   description="Dépannage des problèmes d’analyse, de surveillance, de segmentation et de tableau de bord dans Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/29/2016"
   ms.author="piyushjo"/>

# Guide de dépannage pour les problèmes d’analyse, de surveillance, de segmentation et de tableau de bord

Les éléments suivants sont des problèmes potentiels liés à la façon dont Azure Mobile Engagement rassemble des informations sur vos applications, périphériques et utilisateurs.

## Informations manquantes/retardées

### Problème
- Les informations sont retardées dans l'analyse, la segmentation ou le tableau de bord.
- Il manque des informations de surveillance.
- Il manque des informations d'analyse, de segmentation ou du tableau de bord.
- Limites de segmentation atteinte.

### Causes

- Vous pouvez utiliser l'API Analytics, Monitor et Segments pour voir si des données manquantes dans l'IU sont visibles avec les API.
- Si le Kit de développement logiciel (SDK) Azure Mobile Engagement n'est pas correctement intégrée dans votre application, vous ne serez pas en mesure de voir les informations dans Analyse, Segmentation, Surveillance ou Tableaux de bord.
- Les segments ne peuvent pas être modifiée après avoir été créés, ils peuvent uniquement être « clonés » (copiés) ou « détruits » (supprimés). Les segments peuvent uniquement contenir 10 critères.
- La meilleure façon de tester les informations manquantes de l'analyse consiste à configurer un périphérique de test, à désinstaller et/ou à réinstaller l'application sur le périphérique de test.
- Les informations sont actualisées toutes les 24 heures pour l'analyse, la segmentation ou les tableaux de bord.
- Les informations figurant dans les nouveaux segments ne peuvent pas être affichées au cours de 24 heures qui suivent leur création, même si le segment est basé sur des informations antérieures.
- Le filtrage de vos données d’analyse, dans l’interface utilisateur, affiche tous les exemples de ce type, et ce, quelle que soit la version de votre application (par exemple les « Incidents » filtrés par nom s’afficheront depuis la version 1 et la version 2 de votre application).
- La période de l'analyse est basée sur la date des paramètres des périphériques des utilisateurs ; ainsi, un utilisateur dont le téléphone a une date incorrecte pourrait s'afficher dans une période incorrecte.
- Aucune donnée côté serveur n'est enregistrée lorsque vous utilisez le bouton pour « tester » les notifications Push. Les données sont enregistrées uniquement pour les vraies campagnes de notification Push.

## Impossible de trouver des éléments dans l'interface utilisateur

### Problème
- Impossible de créer des segments en fonction de certains critères de balise d'informations d'application intégrés ou personnalisés.
- Impossible de trouver certains critères de balise d'informations d'application intégrés ou personnalisés dans l'analyse, la surveillance ou les tableaux de bord.
- Impossible d'interpréter les données de l'analyse, de la surveillance, de la segmentation ou des tableaux de bord.

### Causes

- Certains éléments intégrés et certaines balises d'informations d'application sont uniquement disponibles en tant que critères push mais ne peuvent pas être ajoutés à un segment ou être visibles depuis l'analyse, la surveillance ou le tableau de bord. 
- Pour les éléments intégrés et les balises d'informations d'application qui ne peuvent pas être ajoutés à un segment, vous devrez configurer la liste des critères de ciblage dans chaque campagne pour effectuer la même fonction que le ciblage basé sur un segment.
- Consultez les menus contextuels dans les sections Analyse, Surveillance, Segmentation et Tableaux de bord de l'interface utilisateur d'Azure Mobile Engagement pour obtenir de l'aide de l'aide et consignes.

## Dépannage d'incident

### Problème
- Incidents d'application apparaissant dans l'analyse, la surveillance ou le tableau de bord.

### Causes

- Pour résoudre les problèmes d'incident d'application dans Analyse, Surveillance ou Tableau de bord, veillez à consulter les notes de publication pour les problèmes connus avec les versions précédentes du Kit de développement logiciel (SDK).
- Pour mieux résoudre les incidents d'application, effectuez un événement à partir d'un périphérique de test avec votre application installée et recherchez l'ID de votre périphérique dans la section « Moniteur – Événements » de l'interface utilisateur d'Azure Mobile Engagement. Effectuez ensuite l'événement à l'origine de l'incident de votre application et cherchez des informations supplémentaires dans la section « Moniteur – Incident » de l'interface utilisateur d'Azure Mobile Engagement. 

 

<!---HONumber=AcomDC_0302_2016-->