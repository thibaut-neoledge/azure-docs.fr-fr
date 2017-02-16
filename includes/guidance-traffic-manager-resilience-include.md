## <a name="highly-available-solutions-with-azure-traffic-manager"></a>Solutions hautement disponibles avec Azure Traffic Manager
Vous devez déterminer si Azure Traffic Manager peut à lui seul satisfaire les exigences de haute disponibilité de votre charge de travail ou si vous devez le combiner à d’autres solutions DNS ou processus. Selon vos besoins, vous pouvez utiliser :

* **Traffic Manager de manière autonome**. Si une disponibilité de 99,99 % est suffisante pour votre charge de travail, vous pouvez utiliser Traffic Manager seul. En cas de défaillance du service Traffic Manager, les utilisateurs ne pourront pas accéder à votre charge de travail avant la restauration du service.
* **Utiliser une autre solution de gestion du trafic avec Azure Traffic Manager**. En cas de défaillance du service de gestion du trafic, vous pouvez modifier votre enregistrement CNAME et désigner l’autre service. Votre charge de travail reste disponible et est distribuée à tous les emplacements qui l’hébergent. Cette solution est la plus coûteuse, mais elle peut être nécessaire pour les charges de travail nécessitant un niveau SLA plus élevé.



<!--HONumber=Jan17_HO3-->


