# Inspection-HTTPS-Android-D-sactivation-du-SSL-Pinning-avec-Objection-Proxy-Burp-mitmproxy-

Désactivation du SSL Pinning Android avec Objection & Burp Suite
🧾 Présentation du LAB

Dans ce laboratoire, nous avons réalisé une analyse dynamique d’une application Android vulnérable nommée InsecureBankv2 afin d’étudier le mécanisme de SSL Pinning et les méthodes permettant de le contourner.
L’objectif principal était d’utiliser les outils Frida et Objection pour désactiver la protection SSL Pinning, puis intercepter et analyser le trafic HTTPS de l’application via Burp Suite.

Ce laboratoire démontre comment certaines implémentations faibles du SSL Pinning peuvent être contournées facilement à l’aide d’outils de runtime hooking.

🎯 Objectifs du LAB
Vérifier le bon fonctionnement de l’environnement Android
Installer et configurer Frida et Objection
Connecter un émulateur Android via ADB
Désactiver le SSL Pinning dynamiquement
Intercepter le trafic HTTPS avec Burp Suite
Observer les hooks injectés automatiquement
Comprendre les limites des protections SSL classiques


| Outil            | Version              |
| ---------------- | -------------------- |
| Windows 11       | OS principal         |
| Android Emulator | Android 9            |
| Frida            | 17.9.1               |
| Objection        | 1.12.4               |
| Burp Suite       | Community Edition    |
| ADB              | Android Debug Bridge |


Vérification de l’émulateur Android


Cette étape montre la vérification de la connexion entre l’émulateur Android et ADB à l’aide de la commande :

adb devices

Le périphérique emulator-5556 apparaît dans la liste des appareils connectés, ce qui confirme que l’émulateur est correctement détecté et prêt pour l’analyse dynamique.

Cette étape est indispensable avant l’utilisation de Frida et Objection.

![](https://github.com/user-attachments/assets/9423c16b-f364-457e-ade2-5965a773b0d4)


Vérification des versions Frida et Objection


Cette capture montre la vérification des versions des outils utilisés dans le laboratoire :

frida --version
objection version

Les résultats affichent :

Frida : 17.9.1
Objection : 1.12.4

Cette vérification garantit la compatibilité entre les outils d’instrumentation dynamique et l’environnement Android utilisé durant le laboratoire.


![](https://github.com/user-attachments/assets/ab2da12c-d009-422d-8807-202c6b88b8d0)

Lancement d’Objection avec bypass SSL Pinning


Dans cette étape, l’application com.android.insecurebankv2 a été lancée avec Objection en exécutant automatiquement la commande de désactivation du SSL Pinning :

objection -g com.android.insecurebankv2 explore --startup-command "android sslpinning disable"

Cette commande injecte automatiquement plusieurs hooks Frida dans l’application afin de neutraliser les mécanismes de validation des certificats SSL/TLS.

Le bypass est réalisé dynamiquement sans modification directe de l’APK.


![](https://github.com/user-attachments/assets/0c4ab8ba-8fef-494b-8c7c-cce321a59987)

Hooks Frida injectés automatiquement


Cette capture montre les hooks injectés automatiquement par Objection après le démarrage de l’application.

Les messages suivants apparaissent :

Custom TrustManager ready
verifyChain() overridden
checkTrustedRecursive() overridden

Ces hooks remplacent les mécanismes natifs de vérification des certificats Android par des versions permissives qui acceptent tous les certificats, y compris celui de Burp Suite.

Grâce à cette manipulation, le trafic HTTPS de l’application devient interceptable et déchiffrable.


![](https://github.com/user-attachments/assets/1d65a0b5-2f6c-4e01-b478-fd843680a08f)

Interception du trafic HTTPS avec Burp Suite


Après le bypass du SSL Pinning :

L’application accepte le certificat Burp Suite
Les connexions HTTPS ne sont plus bloquées
Les requêtes réseau deviennent visibles dans Burp

Le trafic peut alors être :

analysé
modifié
rejoué
testé dynamiquement

Cette étape démontre l’impact critique d’un SSL Pinning mal implémenté.

| Critère                 | Frida Script Manuel | Objection   |
| ----------------------- | ------------------- | ----------- |
| Complexité              | Moyenne             | Faible      |
| Automatisation          | Partielle           | Totale      |
| Écriture de code        | Nécessaire          | Non         |
| Rapidité d’exploitation | Moyenne             | Très rapide |
| Personnalisation        | Élevée              | Limitée     |
| Facilité d’utilisation  | Intermédiaire       | Très simple |

✅ Résultats obtenus

| Étape                     | Résultat |
| ------------------------- | -------- |
| Émulateur Android détecté | ✅        |
| Frida configuré           | ✅        |
| Objection fonctionnel     | ✅        |
| SSL Pinning désactivé     | ✅        |
| Hooks injectés            | ✅        |
| Certificat Burp accepté   | ✅        |
| Trafic HTTPS intercepté   | ✅        |


🛡️ Recommandations de sécurité
1. Implémenter un SSL Pinning robuste

Utiliser un Certificate Pinning basé sur le hash de la clé publique :

CertificatePinner pinner = new CertificatePinner.Builder()
.add("api.monserveur.com", "sha256/HASH==")
.build();
2. Détecter Frida et Objection

Mettre en place :

détection des ports Frida
détection des processus suspects
vérification de /proc/maps
détection des hooks runtime
3. Déplacer le SSL Pinning vers le code natif

Le pinning implémenté uniquement en Java reste facilement hookable.
Une implémentation NDK augmente considérablement la difficulté d’exploitation.

4. Combiner plusieurs mécanismes de protection

Une stratégie multicouche doit inclure :

Root Detection
Anti-Frida
Anti-Debug
Integrity Checks
SSL Pinning natif
🧠 Conclusion

Ce laboratoire nous a permis de comprendre le fonctionnement du SSL Pinning Android ainsi que les techniques modernes utilisées pour le contourner durant une analyse dynamique.

Grâce à Frida et Objection, nous avons pu désactiver automatiquement les vérifications TLS de l’application InsecureBankv2 et intercepter le trafic HTTPS avec Burp Suite sans modifier l’APK.

Ce LAB démontre que les protections SSL Pinning implémentées uniquement en Java restent vulnérables aux outils de hooking dynamique.
Seule une approche de sécurité multicouche combinant NDK, anti-Frida et mécanismes d’intégrité avancés peut réellement renforcer la résistance des applications Android face aux analyses offensives.



