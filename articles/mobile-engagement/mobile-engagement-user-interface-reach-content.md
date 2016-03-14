<properties 
   pageTitle="Interface utilisateur d’Azure Mobile Engagement - Contenu Reach" 
   description="Apprenez à gérer le contenu unique des différents types de campagnes de notifications Push dans Azure Mobile Engagement" 
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

# Gestion du contenu unique des différents types de campagnes de notifications Push
 
Vous pouvez utiliser la section Contenu d'une nouvelle campagne Reach pour modifier le contenu de vos annonces, sondages, Push de données et vignettes (Windows Phone uniquement). Le paramètre du contenu des campagnes Push est spécifique au type de campagne.
 
### Types de contenu :
- Annonces
- Sondages
- Push de données
- Vignettes (Windows Phone uniquement)
 
## Contenu des annonces
 ![Reach-Content1][30]

### Choisissez le type de votre annonce :
-    Notification uniquement : il s’agit d’une simple notification standard. Cela signifie que si un utilisateur clique dessus, aucun affichage supplémentaire n'apparaîtra. Seule l'action qui y est associée s'affichera.
-    Annonce texte : il s’agit d’une notification qui invite l’utilisateur à regarder une vue de texte.
-    Annonce Web : il s’agit d’une notification qui invite l’utilisateur à regarder l’affichage Web.

### Voir aussi
- [Reach - Procédures - Annonces][Link 3] 

### À propos des annonces d'affichage Web :
Les occurrences du modèle « {deviceid} » dans le code HTML ou le code JavaScript que vous fournissez ici seront automatiquement remplacées par l'identificateur de l'appareil qui affiche l'annonce. Il s'agit d'un moyen simple pour récupérer les identificateurs d'appareil Azure Mobile Engagement dans un service Web externe hébergé dans votre arrière-guichet. Si vous souhaitez créé un affichage Web plein écran (sans utiliser les boutons par défaut Action et Quitter que nous offrons), vous pouvez utiliser les fonctions suivantes depuis le code JavaScript de votre annonce d'affichage Web :

-    effectuer l’action de l’annonce : ReachContent.actionContent()
-    quitter l’annonce : ReachContent.actionContent()
 
### Choisissez votre action :

### À propos des URL d'action :
Toute URL qui peut être interprétée par le système d'exploitation d'un appareil cible peut être utilisée comme une URL d'action. Toute URL dédiée pouvant être prise en charge par votre application (par exemple pour permettre à vos utilisateurs de passer à un écran spécifique) peut également être utilisée comme URL d'action. Chaque occurrence du modèle {deviceid} est automatiquement remplacée par l'identificateur de l'appareil réalisant l'action. Cela peut être utilisé pour récupérer facilement des identificateurs d'appareil Azure Mobile Engagement via un service Web externe hébergé dans votre arrière-guichet.

- **Actions Android et iOS**
    - Ouvrir une page Web
    - http://[web-site-domain\] 
    - Exemple :http://www.azure.com
    - Envoyer un courrier électronique
    - mailto:[destinataire-e-mail]?subject=[objet]&body=[message] 
    - Exemple : mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS
    - sms:[numéro de téléphone] 
    - Exemple :sms:2125551212
    - Composer un numéro de téléphone
    - tel:[numéro de téléphone] 
    - Exemple :tel:2125551212
- **Actions Android uniquement**
    - Télécharger une application du Play Store
    - market://details?id=[app package] 
    - Exemple :market://details?id=com.microsoft.office.word
    - Démarrer une recherche géolocalisée
    - geo:0,0?q=[requête de recherche] 
    - Exemple :geo:0,0?q=starbucks,paris
- **Actions iOS uniquement**
    - Télécharger une application depuis le Magasin d'applications
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - Exemple :http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Actions Windows
    - Ouvrir une page Web
    - http://[web-site-domain\] 
    - Exemple :http://www.azure.com
    - Envoyer un courrier électronique
    - mailto:[destinataire-e-mail]?subject=[objet]&body=[message] 
    - Exemple : mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS (application Skype du Store requise)
    - sms:[numéro de téléphone] 
    - Exemple :sms:2125551212
    - Composer un numéro de téléphone (application Skype du Store requise)
    - tel:[numéro de téléphone] 
    - Exemple :tel:2125551212
    - Télécharger une application du Play Store
    - ms-windows-store:PDP?PFN=[ID de package d’application] 
    - Exemple :ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Démarrer une recherche Bing Cartes
    - bingmaps:?q=[requête de recherche] 
    - Exemple :bingmaps:?q=starbucks,paris
    - Utiliser un modèle personnalisé
    - [schéma personnalisé]://[paramètres du schéma personnalisé] 
    - Exemple :myCustomProtocol://myCustomParams
    - Utiliser un package de données (application du Store pour la lecture d'extension requise)
    - [dossier][données].[extension] 
    - Exemple :myfolderdata.txt
 
### Génération d'une URL de suivi :
-    Consultez la section « Paramètres » de la <UI Documentation> pour les instructions relatives à la génération d'une URL de suivi qui permettra aux utilisateurs de télécharger une autre de vos applications.
 
### Définition du texte de votre annonce
Remplissez le titre, le contenu et le texte des boutons de votre annonce. Vous pouvez cibler une audience pour une future campagne suivant les commentaires Reach indiquant la façon dont les utilisateurs ont répondu à cette campagne. Le ciblage de l'audience peut se baser sur les commentaires déterminant si la campagne a été envoyée par notification Push, si elle a obtenu une réponse, si elle a été activée ou quittée.

### Voir aussi
- [Documentation sur l’interface utilisateur - Reach - Nouveau critère Push][Link 28]

## Contenu des sondages
![Reach-Content2][31] Remplissez le titre, la description et le texte des boutons de votre annonce. Ajoutez ensuite des questions et des choix de réponse pour ces questions. Vous pouvez cibler une audience pour une future campagne suivant les commentaires Reach indiquant la façon dont les utilisateurs ont répondu à cette campagne. Le ciblage de l'audience est déterminé selon que la campagne est issue d'une transmission de type push, qu'elle a obtenu une réponse, qu'elle a été activée ou quittée. Le ciblage de l'audience peut également se baser sur les commentaires de réponse du sondage pour lequel la question à choix multiple est utilisée comme critère.

### Voir aussi
- [Documentation sur l’interface utilisateur - Reach - Nouveau critère Push][Link 28]
 
## Contenu des Push de données
![Reach-Content3][32]

### Choisissez le type de vos données :
- Texte
- Données binaires
- Données Base64

### Définition du contenu de vos données
- Si vous sélectionnez une transmission de type push des données de texte, copiez et collez le texte dans la boîte « contenu ».
- Si vous sélectionnez une transmission de type push de données binaires ou base64, utilisez le bouton « Télécharger votre fichier » pour télécharger votre fichier.
- Vous pouvez cibler une audience pour une future campagne suivant les commentaires Reach indiquant la façon dont les utilisateurs ont répondu à cette campagne. Le ciblage de l'audience est déterminé selon que la campagne est issue d'une transmission de type push, qu'elle a obtenu une réponse, qu'elle a été activée ou quittée.

### Voir aussi
- [Documentation sur l’interface utilisateur - Reach - Nouveau critère Push][Link 28]

## Contenu des vignettes (Windows Phone uniquement)
![Reach-Content4][33]

### Définition du contenu de vos vignettes
La charge utile de la vignette correspond au texte qui s'affichera dans la vignette de votre application sur les appareils Windows Phone. Un Push de vignette est la version du service de notifications Push de Microsoft (MPNS) d'un Push natif pour Windows Phone. Ce type de Push de vignette est le seul type de push qui n'a pas de réponse. L'audience des futures campagnes ne peut donc pas être déterminée en fonction des résultats d'une campagne de Push de vignette.

### Voir aussi
- [Documentation sur les API- API Reach - Push natif][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=AcomDC_0302_2016-->