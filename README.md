# touchdesginer-and-emg-
creative biofeedback and touchdesigner
Niveau : intermédiaire 
Outils : TouchDesigner (CHOP / SOP / Audio CHOP), capteur EMG (MyoWare ou signal simulé), ordinateur avec sortie audio
Objectif pédagogique : comprendre comment un signal corporel peut piloter une matière visuelle et sonore, et concevoir un dispositif sensible cohérent.

1. Objectifs d’apprentissage
À l’issue du cours, les étudiant·e·s seront capables de :
    • Comprendre la logique signal → transformation → perception
    • Lire un flux de données (capteur ou valeur simulée)
    • Traiter un signal (lissage, seuil, enveloppe)
    • Relier un signal à une transformation visuelle
    • Générer un son piloté par données
    • Concevoir un dispositif interactif expressif (pas seulement technique)
Compétences mobilisées :
    • Pensée systémique
    • Sensibilité aux données
    • Esthétique de l’interaction
    • Notions de biofeedback et art-système

2. Objectifs d’apprentissage
À l’issue du cours, les étudiant·e·s seront capables de :
    • Comprendre la logique signal → transformation → perception
    • Lire un flux de données (capteur ou valeur simulée)
    • Traiter un signal (lissage, seuil, enveloppe)
    • Relier un signal à une transformation visuelle
    • Générer un son piloté par données
    • Concevoir un dispositif interactif expressif (pas seulement technique)

3. Concept pédagogique : pourquoi ce dispositif ?
Le projet propose une chaîne simple mais puissante :
Corps → Signal → Interprétation → Forme visuelle → Forme sonore → Perception
Ce n’est pas un gadget interactif :
C’est un écosystème relationnel, où la matière semble réagir comme un organisme.
On travaille ici sur :
    • la notion de présence
    • la transformation du corps en agent esthétique
    • la relation entre données et poétique

   5. Architecture générale du projet
Le système repose sur trois réseaux principaux :
    1. CHOP (données) : traitement du signal (EMG ou valeur simulée)
    2. SOP (forme) : déformation spatiale pilotée par les données
    3. Audio CHOP (son) : génération sonore pilotée par les données
Schéma logique :

A — Créer la bulle (visuel de base)
Étape A1 — sphere1 (SOP)
    • Ce que tu fais : tu crées une sphère.
    • Pourquoi : base neutre, une forme simple qui devient un “organisme” dès qu’on la déforme.
Étape A2 — noise1 (SOP, bleu) branché sur la sphère
Chaîne :
sphere1 → noise1 → geo1
    • Rôle de Noise SOP : déformer la géométrie, créer une matière vivante (ondulation, membrane).
    • Paramètres (exemples) :
        ◦ Period : taille des ondulations (0.15–0.4 pour un effet organique)
        ◦ Harmonics : complexité (2–3)
        ◦ Roughness : texture (0.3–0.6)
Étape A3 — geo1 + render1
    • Rôle de geo1 : afficher/rendre la géométrie SOP.
    • Pourquoi : séparer “forme” (SOP) et “affichage” (Geo/Render).

B — Rendu en points + “palpitation” esthétique (au lieu de gonfler)
Étape B1 — Identifier le matériau : line1 (Line MAT)
Dans geo1 onglet Render, tu avais Material = line1.
    • Rôle de line1 (Line MAT) : rendre la sphère en points / lignes (look particulaire).
Étape B2 — Piloter la taille des points (effet organique)
Dans line1 → onglet Point :
    • Paramètre : Point Size Multiplier

C — Construire la chaîne de signal (d’abord simulée, puis capteur)
Étape C1 — Simulation : constant1 (CHOP)
Chaîne test :
constant1 → lag1 → math1 → limit1 → trigger1
    • Rôle de Constant CHOP : simuler un capteur, tester sans hardware.
    • Pourquoi pédagogique : on valide la logique de traitement avant la partie série.
Étape C2 — lag1 (Lag CHOP)
    • Rôle : lisser (smoothing), enlever les micro-sauts.
    • Pourquoi : un signal “vivant” doit être stable (sinon jitter visuel/sonore).
Étape C3 — math1 (Math CHOP)
    • Rôle : remapper / amplifier.
    • Pourquoi : adapter la sensibilité au capteur / à la personne.
Exemple de remap (quand signal brut 0..1023) :
    • From Range : 0..1023
    • To Range : 0..1
Étape C4 — limit1 (Limit CHOP)
    • Rôle : clamp/limiter la plage (0..1).
    • Pourquoi : éviter que des pics détruisent l’esthétique ou saturent l’audio.
Étape C5 — trigger1 (Trigger CHOP)
    • Rôle : transformer un flux continu en événement temporel (attaque/relâchement).
    • Pourquoi : c’est là que naît la sensation “organique / battement”.
Réglage qu’on a fixé (version qui déclenche bien) :
    • Trigger On : Off to On
    • Threshold : 0.6 (à ajuster)
    • Attack : 0.02
    • Sustain : 0
    • Release : 0.4–0.8
D — Lier le signal au visuel (onde organique)
Étape D1 — Piloter Noise SOP amplitude avec Trigger
Dans noise1 (SOP) → paramètre Amplitude :
Expression :
 op('trigger1')['chan1'] * 0.5
Rôle : l’activation EMG “allume” la déformation.
Pourquoi : au repos → calme ; contraction → matière agitée.

Étape D2 — Donner l’impression que l’onde se déplace (propagation)
Dans noise1, si tu as Translate/Offset, on a utilisé :
absTime.seconds * 0.2
Rôle : déplacer le bruit dans l’espace au fil du temps.
Pourquoi : ça produit une onde “qui voyage” au lieu d’un frémissement figé.

E — Remplacer la simulation par le capteur EMG (MyoWare + Arduino Nano)
Étape E1 — Arduino Nano : code série “palier 0” (test brut)
Pourquoi : valider la communication série avant tout traitement.
Étape E2 — Arduino Nano : palier 2 recommandé (enveloppe EMG)
Pourquoi : l’EMG utile = amplitude (énergie). Ce palier donne un signal stable pour Trigger + son.
Étape E3 — TouchDesigner : lecture série (Serial DAT)
- serial1 (Serial DAT)
    • Baud : 115200
    • Port : Arduino Nano
    • Rôle : recevoir du texte (lignes) depuis Arduino.
    • Pourquoi : c’est la porte d’entrée capteur.
- dattochop1 (DAT to CHOP)
Paramètres (interface récente) :
    • DAT = serial1
    • Row Index = -1
    • Col Index = 0
    • Rôle : convertir “texte” → “channel numérique”.
    • Pourquoi : tout le traitement et l’animation se fait en CHOP.

F — Ajouter le son (piloté par le même Trigger)
Étape F1 — audioosc1 (Audio Oscillator CHOP)
    • Rôle : générer un son continu (sinus/triangle/noise…).
    • Dans ta version, le volume s’appelle Amplitude (pas “Volume”).
Dans audioosc1 → Amplitude :


Option (hauteur liée à l’effort) dans audioosc1 → Base Frequency :

Pourquoi : le son devient un feedback corporel, synchronisé avec la matière visuelle.
Étape F2 — audiodeviceout1 (Audio Device Out CHOP)
    • Rôle : envoyer l’audio vers les haut-parleurs/casque.
    • Si rouge : régler via Dialogs → Audio Device In/Out.
Chaîne :
audioosc1 → audiodeviceout1
G — Expressions finales (celles à retenir)
Visuel : déformation organique
Dans noise1 → Amplitude :

Option propagation (Translate/Offset) :

Matériau : pulsation des points
Dans line1 → Point Size Multiplier :

Son : volume (Amplitude)
Dans audioosc1 → Amplitude :

Option : fréquence


H — Pourquoi ces outils (résumé pédagogique rapide)
    • Serial DAT : reçoit le flux capteur (texte)
    • DAT to CHOP : convertit en signal exploitable (channel)
    • Lag CHOP : stabilise / organicité
    • Math CHOP : remap/gain (adapter à chaque corps)
    • Limit CHOP : évite pics et saturation
    • Trigger CHOP : transforme en événement (pulsation dramaturgique)
    • Noise SOP : matérialise l’influx dans une membrane
    • Line MAT (Point Size Multiplier) : donne une palpitation de “matière”
    • Audio Oscillator + Audio Device Out : matérialise l’influx en son


