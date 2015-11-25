Intégration des Web Services Cartegie
=====================================

Contenu
-------

[Contexte](#2.1)  
[Pré requis](#2.2)  
[Objectif](#2.3)  
[Problématique de Sécurité](#2.4)  
[Intégration JS des différents Web Services](#3.1)  
[Intégration de la bibliothèque JS Cartegie](#3.2)  
[Eléments de configurations du service](#3.3)  
[Webservice Siretage](#3.4) 
[Configuration (Env. de recette)](#4.1)  
[Tags des champs de saisie pour les paramètres d’appel WS](#4.2)  
[Tags pour affichage du résultat du siretage multiple](#4.3)  
Tags pour remplir les champs (à partir des éléments présents le tableau de résultat) après sélection d’un élément du siretage multiple	6  
Tags pour pour remplir les champs du formulaire (à partir d’un appel ws fiche entreprise) après sélection d’un élément du siretage multiple	6  
Bouton de soumission pour appel WS Siretage	7  
Exemple d’implémentation du WS Siretage	8  
Webservice RNVP	11  
Configuration (Env. de recette)	11  
Tags de champs de saisie pour les paramètres d’appel WS	11  
Tags pour affichage du résultat de la RNVP	11  
Bouton de soumission pour appel WS RNVP	12  
Loader  Hors du bouton	12  
Webservice saisie des champs CP, Ville, Rue en mode assistée	13  
Configuration (Env. de recette)	13  
Tags de champs de saisie pour les paramètres d’appel WS	13  
Exemple	14  



##Contexte <a id="2.1"></a>

Cartegie propose différents Web Services de traitement et d’enrichissement d’information.  
Actuellement, le seul moyen d’intégrer ceux-ci chez un client sont via un développement par celui-ci à partir d’une documentation technique mise à disposition.  
Cela nécessite des compétences dans un langage de programmation par le client et un apprentissage sur le principe de fonctionnement logique des différents webservices.  

##Pré requis <a id="2.2"></a>

Le client dispose déjà de son formulaire (d’inscription ou de contact) sur son site web.  

##Objectif <a id="2.3"></a>

Le client doit pouvoir utiliser nos webservices en n’effectuant aucun développement pour intégrer l’appel à nos webservices dans leurs formulaires déjà existant.  
La seule manipulation à réaliser par le client sera de placer des tags spécifiques sur des champs ou des boutons du formulaire (du formulaire HTML) et faire appel à une librairie Javascript mis à disposition de Cartegie (qui se chargera de toute la logique technique de communication aux web services).  
On peut ainsi rendre n’importe quel formulaire HTML interconnecté à nos webservices.  

##Problématique de Sécurité <a id="2.4"></a>

Afin de sécuriser l’appel et de s’assurer qu’il n’y a pas usurpation d’identité, nous utilisons :  

 - un système de clés privées / publiques et le mécanisme
   d’authentification standardisé OAuth2 / Open ID Connect  
   
 - ainsi qu’un système d’approbation du domaine demandeur de la requête   
	 - Tant que   le domaine demandeur de la requête n’a pas été approuvé au préalable
   par le propriétaire des clés, celui-ci reçoit un email lui demandant
   de cliquer sur un lien pour autoriser ou non le dit domaine.   
   
Remarque : La mise en place de ces 2 points reste à finaliser pour industrialiser la création d’un compte client

##Intégration JS des différents Web Services<a id="3.1"></a>


####Intégration de la bibliothèque JS Cartegie <a id="3.2"></a>
Quel que soit le web service à implémenter, le seul script JS que le client doit charger est ctg_ws_app.min.js.
Cette application charge le système d’injection de fichiers (include()), jquery > 1.8 si non présent, le fichier d’authentification et, en fonction du paramétrage, les différents scripts de services.
Pour l’environnement de recette, voici la ligne de code à écrire :   

`<script src="http://webservices.formulaire.cartegie.local/wsproxy/Scripts/ctg_ws_app.min.js"></script>`

####Eléments de configurations du service<a id="3.3"></a>

`<script>
Var ctg_config = {
      ‘api_key’ : ‘CLE_CLIENT_PUBLIQUE’,
      ‘services’ : [‘annuaire_inverse’, ‘siretage’, rnvp’, ‘rnvp_assiste’, ‘email’]
      //+Configurations spécifiques à chaque service
      }
</script>`

La variable ctg_config est le seul paramètre accessible à l’utilisateur. Il est tenu d’y déclarer au minimum sa clé api publique et le service qu’il souhaite utiliser. 

L’application se charge automatiquement de l’authentification et de l’injection du code correspondant au(x) service(s) nécessaires :

<table><tr><th>Service	</th></tr>
<tr><td>siretage</td></tr>	
<tr><td>rnvp	</td></tr>
<tr><td>rnvp_assiste</td></tr>
<tr><td>geocodage</td></tr>
<tr><td>annuaire_inverse</td></tr>
<tr><td>email</td></tr></table>
####Webservice Siretage<a id="3.4"></a>
#####Configuration (Environnement de recette)<a id="4.1"></a>
`<script src="http://webservices.formulaire.cartegie.local/wsproxy/Scripts/ctg_ws_app.min.js"></script>`
 `<script> 
var ctg_config = {
 'api_key': "CLE_PUBLIQUE_CLIENT", 
'services': ["siretage"], 
'siretage_conf':{
		'infos_retournees':['NOMEN', 'ENSEIGNE', 'L4_VOIE', 'L5_DISTSP', 'L6_POST'],
		'nom_colonnes': { 'NOMEN': 'Nom Entreprise', 
		'ENSEIGNE': 'Enseigne', 
		'L4_VOIE': 'Adresse', 
		'L5_DISTSP': 'Adresse 2', 
		'L6_POST': 'Adresse3' },
		'message_pas_de_resultat': "Votre demande ne retourne aucun résultat",
		'cacher_modale_bootstrap_sur_selection': true,
		'remplir_champs_apres_selection': true
	}
}
</script>
`
Toute la configuration nécessaire au web service de siretage se trouve dans le dictionnaire siretage_conf.
<table><tr><th>Configuration</th><th>Valeurs</th><th>Précisions</th></tr>
<tr><td>
Infos_retournées </td><td>NOMEN (Raison Sociale) 
ENSEIGNE (Raison sociale de l’enseigne)
SIGLE,
L3_CADR (Ligne 3 d’adresse)
L4_VOIE(Ligne 4 d’adresse)
L5_DISTSP(Ligne 5 d’adresse)
L6_POST(Ligne 6 d’adresse)
ETAT (Actif/Inactif)</td><td>[] une  liste d’éléments correspondant au retour du siretage MULTIPLE.</td></tr>
<tr><td>
'nom_colonnes'</td><td>
Comme infos retournées, avec une chaine de caractère correspondante contenant le nom ex :
{‘NOMEN’ : ‘Raison Sociale’}</td><td>
{} dictionnaire de correspondance entre les valeurs retournées (info_retournees) et le nom des colonnes (cas d’affichage tableau)</td></tr>
<tr><td>
‘message_pas_de_resultat'</td><td></td><td>
Le message affiché en cas d’erreur. Défaut : « Pas de résultat »</td></tr>
<tr><td>
'cacher_modale_bootstrap_sur_selection'</td><td>
True / false </td><td>
Si une modale bootstrap est utilisée, activer cette option pour la cacher une fois le résultat choisi.</td></tr>
<tr><td>
'remplir_champs_apres_selection'</td><td>
True / False</td><td>
Remplir immédiatement les champs de résultats (ctg_siretage_result_clicked= ‘’) sur sélection</td></tr>
<tr><td>
‘fiche_complete_sur_selection_de_resultat’</td><td>
True / false</td><td>
Fait un siretage fiche sur sélection d’un résultat pour récupérer toutes les informations de l’entreprise.</td></tr></table>

#####Tags des champs de saisie pour les paramètres d’appel WS<a id="4.2"></a>
Ci-après les différents tags à placer sur les inputs, ils servent à récupérer la saisie utilisateur pour la passer en paramètre lors de l’appel WS.

<table><tr><th>Tag</th><th>Description</th></tr>
<tr><td>ctg_form_siretage='Appartement'</td><td>Ligne adresse Appartement</td></tr>
<tr><td>ctg_form_siretage='Batiment'</td><td>Ligne adresse Bâtiment</td></tr>
<tr><td>ctg_form_siretage='Voie'</td><td>Ligne adresse Voie</td></tr>
<tr><td>ctg_form_siretage='BPlieudit'</td><td>Ligne adresse BP Lieu-Dit
<tr><td>ctg_form_siretage='CP'</td><td>Ligne adresse (Requis)</td></tr>
<tr><td>ctg_form_siretage='Civilite'</td><td></td></tr>
<tr><td>ctg_form_siretage='Nom'</td><td></td></tr>
<tr><td>ctg_form_siretage='Prenom'</td><td></td></tr>
<tr><td>ctg_form_siretage='RaisonSociale'</td><td>Raison Sociale de l’entreprise (REQUIS)</td></tr>
<tr><td>ctg_form_siretage='Enseigne'</td><td></td></tr>
<tr><td>ctg_form_siretage='Telephone'</td><td></td></tr>
<tr><td>ctg_form_siretage='Siret'</td><td>(REQUIS)</td></tr>
<tr><td>ctg_form_siretage='Ville'</td><td></td></tr></table>


**Champs Requis pour pouvoir lancer l’appel WS**

 - CP + Raison sociale 
 - Ou Siret

#####Tags pour affichage du résultat du siretage multiple<a id="4.3"></a>
Deux présentations sont actuellement possibles : 

 - Tableau
 - Liste

Pour afficher ce tableau, il faut placer le tag ctg-siretage-mult-resp-table sur un élément du DOM.

Exemple : <div ctg-siretage-mult-resp-table></div>
Liste :

Pour afficher cette liste, il faut placer le tag ctg-siretage-mult-resp-list sur un élément du DOM.
Exemple : <div ctg-siretage-mult-resp-list></div>

Tags pour remplir les champs (à partir des éléments présents le tableau de résultat) après sélection d’un élément du siretage multiple
Ci-après les différents tags à placer sur les inputs  qui servent à afficher le détail d’une fiche entreprise.
Tag
ctg_siretage_result_clicked='NOMEN'
ctg_siretage_result_clicked='ENSEIGNE'
ctg_siretage_result_clicked='SIGLE'
ctg_siretage_result_clicked='L3_CADR'
ctg_siretage_result_clicked='L4_VOIE'
ctg_siretage_result_clicked='L5_DISTSP'
ctg_siretage_result_clicked='L6_POST'
ctg_siretage_result_clicked='CP'
ctg_siretage_result_clicked='Ville'
ctg_siretage_result_clicked='ETAT'

Tags pour pour remplir les champs du formulaire (à partir d’un appel ws fiche entreprise) après sélection d’un élément du siretage multiple
Ci-après les différents tags à placer sur les inputs  qui servent à afficher le détail d’une fiche entreprise.
Tag
ctg_siretage_fiche='SIRET'
ctg_siretage_fiche='NOMEN'
ctg_siretage_fiche='ENSEIGNE'
ctg_siretage_fiche='SIGLE'
ctg_siretage_fiche='L1_NOMEN'
ctg_siretage_fiche='L2_COMP'
ctg_siretage_fiche='L3_CADR'
ctg_siretage_fiche='L4_VOIE'
ctg_siretage_fiche='L5_DISTSP'
ctg_siretage_fiche='L6_POST'
ctg_siretage_fiche='CP'
ctg_siretage_fiche='Ville'
ctg_siretage_fiche='L7_ETRG'
ctg_siretage_fiche='TEL'
ctg_siretage_fiche='FAX'
ctg_siretage_fiche='REGION'
ctg_siretage_fiche='DEPT'
ctg_siretage_fiche='ARRONET'
ctg_siretage_fiche='CTONET'
ctg_siretage_fiche='COD_COM'
ctg_siretage_fiche='LIB_COM'
ctg_siretage_fiche='CODPOS'
ctg_siretage_fiche='ZEMET'
ctg_siretage_fiche='COD_VOIE'
ctg_siretage_fiche='NUM_VOIE'
ctg_siretage_fiche='INDREP'
ctg_siretage_fiche='TYPE_VOIE'
ctg_siretage_fiche='LIB_VOIE'
ctg_siretage_fiche='APET700'
ctg_siretage_fiche='SIEGE'
ctg_siretage_fiche='TEFET'
ctg_siretage_fiche='EFETCENT'
ctg_siretage_fiche='ORIGINE'
ctg_siretage_fiche='DCRET'
ctg_siretage_fiche='ACTIVNAT'
ctg_siretage_fiche='LIEUACT'
ctg_siretage_fiche='ACTISURF'
ctg_siretage_fiche='EXPLET'
ctg_siretage_fiche='PRODPART'
ctg_siretage_fiche='CIVILITE'
ctg_siretage_fiche='CJ'
ctg_siretage_fiche='TEFEN'
ctg_siretage_fiche='EFENCENT'
ctg_siretage_fiche='APEN700'
ctg_siretage_fiche='APRM'
ctg_siretage_fiche='TCA'
ctg_siretage_fiche='DCREN'
ctg_siretage_fiche='EXPLEN'
ctg_siretage_fiche='NBETEXPL'
ctg_siretage_fiche='TCAEXP'
ctg_siretage_fiche='REGIMP'
ctg_siretage_fiche='RPEN'
ctg_siretage_fiche='DEPCOMEN'
ctg_siretage_fiche='ETAT'
ctg_siretage_fiche='DATE_ETAT'

Bouton de soumission pour appel WS Siretage
Pour appel Siretage Multiple : <button type="button" ctg_siretage_multiple_submit>button</button>
Pour appel Siretage Unique :  <button type="button" ctg_siretage_unique_submit>button</button>



Exemple d’implémentation du WS Siretage

Formulaire :

Avec fenêtre modale pour résultat du WS

Configuration


Ce qui donne :

Après clic sur bouton « Siretage Multiple »


Après clic sur une ligne de résultat :




Webservice RNVP
Configuration (Env. de recette)
<script src="http://webservices.formulaire.cartegie.local/wsproxy/Scripts/ctg_ws_app.min.js"></script>
<script> 
var ctg_config = {
       'api_key': "CLE_PUBLIQUE_CLIENT", 
      'services': ["rnvp"], 
      'rnvp_classique_animate_button': true
      }
</script>
Le seul paramètre 'rnvp_classique_animate_button’ (true/false) permet à l’application de mettre un loader sur le bouton d’envoi de la requête ou non.

Tags de champs de saisie pour les paramètres d’appel WS
Ci-après les différents tags à placer sur les inputs, ils servent à récupérer la saisie utilisateur pour la passer en paramètre lors de l’appel WS.
Tag
Description
ctg_rnvp_input='Appartement'
Ligne adresse Appartement
ctg_rnvp_input ='Batiment'
Ligne adresse Bâtiment
ctg_rnvp_input ='Voie'
Ligne adresse Voie
ctg_rnvp_input ='BPlieudit'
Ligne adresse BP Lieu-Dit
ctg_rnvp_input = ‘CodePostal’
Ligne adresse 
ctg_rnvp_input="Ville"

ctg_rnvp_input ='Civilite'

ctg_rnvp_input ='Nom'

ctg_rnvp_input ='Prenom'

ctg_rnvp_input ='RaisonSociale'
Raison Sociale de l’entreprise 
ctg_rnvp_input ='Enseigne'

ctg_rnvp_input ='Telephone'

ctg_rnvp_input ='Siret'

ctg_rnvp_input="NomEnMajuscule"
Booleen

Tags pour affichage du résultat de la RNVP
Tag
Description
ctg_rnvp_result="Ligne1"

ctg_rnvp_result="Ligne2"

ctg_rnvp_result="Ligne3"

ctg_rnvp_result="Ligne4"

ctg_rnvp_result="Ligne5"

ctg_rnvp_result="Ligne6"

ctg_rnvp_result="NumeroDeVoie"

ctg_rnvp_result="NumExt"

ctg_rnvp_result="TypeVoie"

ctg_rnvp_result="LibVoie"

ctg_rnvp_result="CP"

ctg_rnvp_result="Ville"

ctg_rnvp_result="BurDist"

ctg_rnvp_result="MatVoie"

ctg_rnvp_result="CodCom"

ctg_rnvp_result="LibCom"

ctg_rnvp_result="IdHexaPoste"

ctg_rnvp_result="IdExaligne3"

ctg_rnvp_result="CEA"


Bouton de soumission pour appel WS RNVP
<button type="button" ctg_rnvp_normalise_button>button</button>

Loader  Hors du bouton
Placer une div avec ctg_loader.


Webservice saisie des champs CP, Ville, Rue en mode assistée
Configuration (Env. de recette)
<script src="http://webservices.formulaire.cartegie.local/wsproxy/Scripts/ctg_ws_app.min.js"></script>
<script> 
var ctg_config = { 
      'api_key': "CLE_PUBLIQUE_CLIENT", 
      'services': [ "rnvp_assiste"], 
      'rnvp_classique_animate_button': true
      }
</script>
Tags de champs de saisie pour les paramètres d’appel WS
Ci-après les différents tags à placer sur les inputs, ils servent à récupérer la saisie utilisateur pour la passer en paramètre lors de l’appel WS.
Mode 1 : CP,Ville et Voie
La première assistance en deux champs : 
* Un champ comprenant à la fois le code postal et le nom de la ville 
* et un champ voie

<input  ctg_rnvp_assiste_input="CP,Ville" type="text" class="form-control" id="codePostalVilleGeocodage" placeholder="Saisissez votre code postal ou ville">
<input ctg_rnvp_assiste_input="Voie" type="text" class="form-control" id="voieGeocodage" placeholder="Voie">
Ce sont les deux seuls tag à placer. Toute la mécanique d’auto complétion et les données seront automatiquement liées à ces champs. 
Attention ! On ne peut remplir la voie si CP/Ville n’est pas rempli !
Mode 2 : Cp, Ville, Voie
Cette fois-ci, il est possible de décomposer les informations en 3 parties : Le Cp, La Ville et la Voie.

<input ctg_rnvp_assiste_input='CP'  type="text" class="form-control" placeholder="Code Postal" >
<input ctg_rnvp_assiste_input='Ville'  type="text" class="form-control" placeholder="Ville">
<input ctg_rnvp_assiste_input="Voie"  type="text" class="form-control" placeholder="N° et Rue">
Idem que pour le scénario 1 ce sont les seuls tags à placer. 
On peut chercher Code Postal puis Ville ou Ville puis Code Postal cela ne pose pas de problèmes. Pour ce qui est de la voie, il faudra que Code Postal ET Ville soient présents.






Exemple
Ici, on souhaite créer un formulaire d’annuaire inversé. On part d’un simple formulaire Bootstrap avec un champ de saisie et un bouton.

On place le tag ctg_annuaire_inverse= « input» (en rouge),
On place le tag ctg_annuaire_inverse= «submit » (en orange) sur le bouton de soumission
Et le résultat sera affiché dans la div ou le tag ctg_annuaire_inverse= « result » a été placé (en jaune). 
Comme on peut le voir, il faut charger le script ctg_ws_app.js et définir la configuration via les 2 lignes en bleu.


