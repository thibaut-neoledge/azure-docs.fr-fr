<properties
    pageTitle="Documentation Azure Government | Microsoft Azure"
    description="This provides a comparision of features and guidance on developing applications for Azure Government"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="09/29/2016"
    ms.author="ryansoc"/>



#  <a name="azure-government-compute"></a>Calcul Azure Government

##  <a name="virtual-machines"></a>Machines virtuelles

Pour plus d’informations sur ce service et son utilisation, consultez [Tailles de Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variantes

Les références de machines virtuelles suivantes sont mises à la disposition générale (GA) dans Azure Government :

Référence de la machine virtuelle|Gouvernement américain VA|Gouvernement américain IA|Remarques
---|---|---|---
Une|GA|GA|Aucun
Dv1|GA|-|Aucun
DSv1|GA|-|Aucun
Dv2|GA|GA|15 bientôt disponible
F|GA|GA|Aucun
G|Prévu|-|Aucun

###  <a name="data-considerations"></a>Considérations sur les données

Les informations suivantes identifient les limites d’Azure Government pour les machines virtuelles Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Les données entrées, stockées et traitées au sein d’une machine virtuelle peuvent contenir des données contrôlées à l’exportation. Fichiers binaires exécutés au sein des machines virtuelles Azure. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Chaînes de connexion SQL.  Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure.  | Les métadonnées n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre machine virtuelle Azure.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de rôles client, Groupes de ressources, Noms de déploiements, Noms de ressources, Balises de ressources  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>



<!--HONumber=Oct16_HO2-->


