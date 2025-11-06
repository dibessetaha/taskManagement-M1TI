# 🎓 Tutoriel EXPRESS - Le langage des fichiers STEP

## 📚 Introduction

EXPRESS est le langage de modélisation de données utilisé dans les fichiers STEP (ISO 10303). C'est comme du SQL ou du JSON, mais spécialisé pour décrire des objets 3D et leurs relations.

Analysons ton fichier `roue_de_ferrari.stp` (qui n'est pas vraiment une roue de Ferrari 😄) pour comprendre !

---

## 🏗️ Structure d'un fichier STEP

```
ISO-10303-21;              ← Signature du format
HEADER;                    ← Section des métadonnées
  FILE_DESCRIPTION(...)
  FILE_NAME(...)
  FILE_SCHEMA(...)
ENDSEC;
DATA;                      ← Section des données (le vrai contenu)
  #16=PRODUCT(...)
  #2=PRODUCT_CONTEXT(...)
  ...
ENDSEC;
END-ISO-10303-21;          ← Fin du fichier
```

### 1️⃣ La section HEADER

```express
FILE_DESCRIPTION(
  ('3DEXPERIENCE STEP','CAx-IF Rec.Pracs...'),
  '2;1'
)
```
- **Rôle** : Décrit le contenu et la version
- **Contenu** : Métadonnées sur l'origine du fichier

```express
FILE_NAME(
  'C:\\Users\\TDE16\\Documents\\Formation\\roue de ferrari.stp',
  '2025-11-06T15:20:00+00:00',
  ('none'),
  ('none'),
  '3DEXPERIENCE Platform',
  '3DEXPERIENCE Platform STEP AP242 v3',
  'none'
)
```
- Chemin du fichier source
- Date de création
- Auteur, organisation
- Système qui l'a créé

```express
FILE_SCHEMA(('AP242_MANAGED_MODEL_BASED_3D_ENGINEERING_MIM_LF'))
```
- **Crucial** : Indique le protocole utilisé (ici AP242)

---

## 🔢 Système de numérotation : Les Entités

### Syntaxe de base

```express
#16 = PRODUCT('Physical Product00000320AA', 'Physical Product00000320AA', '', (#2));
 ↑     ↑                                                                           ↑
 ID   TYPE                                                                    RÉFÉRENCE
```

### Composants :

1. **#N** : Identifiant unique (comme une clé primaire en SQL)
2. **TYPE** : Le nom de l'entité EXPRESS
3. **Attributs** : Entre parenthèses, séparés par des virgules
4. **Références** : Les #N d'autres entités (comme des clés étrangères)

---

## 🎯 Les types d'entités dans ton fichier

### 1. Entités de contexte (métadonnées produit)

```express
#1 = APPLICATION_CONTEXT('managed model based 3d engineering');
#2 = PRODUCT_CONTEXT(' ', #1, 'mechanical');
#16 = PRODUCT('Physical Product00000320AA', 'Physical Product00000320AA', '', (#2));
```

**Hiérarchie** :
```
APPLICATION_CONTEXT (#1)
    ↓
PRODUCT_CONTEXT (#2)
    ↓
PRODUCT (#16) ← Le produit principal
```

### 2. Entités géométriques de base

#### Points (CARTESIAN_POINT)
```express
#14 = CARTESIAN_POINT('', (0., 0., 0.));
#31 = CARTESIAN_POINT('Axis2P3D Location', (10., -8., -5.));
                      ↑ nom optionnel      ↑ coordonnées (x, y, z)
```

#### Directions (DIRECTION)
```express
#32 = DIRECTION('Axis2P3D Direction', (1., 0., 0.));
                                       ↑ vecteur unitaire (x, y, z)
```

#### Vecteurs (VECTOR)
```express
#38 = VECTOR('Line Direction', #37, 1.);
                               ↑     ↑
                          direction  magnitude
```

### 3. Systèmes de coordonnées (AXIS2_PLACEMENT_3D)

```express
#34 = AXIS2_PLACEMENT_3D('Cylinder Axis2P3D', #31, #32, #33);
                         ↑ nom               ↑     ↑     ↑
                                          origine  axeZ  axeX
```

**Signification** :
- Définit un repère local 3D
- **#31** : Point d'origine
- **#32** : Direction de l'axe Z
- **#33** : Direction de l'axe X
- L'axe Y est calculé automatiquement (produit vectoriel)

### 4. Courbes géométriques

#### Lignes (LINE)
```express
#39 = LINE('Line', #36, #38);
                   ↑     ↑
               origine  direction
```

#### Cercles (CIRCLE)
```express
#48 = CIRCLE('generated circle', #47, 22.627416998);
                                 ↑    ↑
                            placement  rayon
```

### 5. Surfaces

#### Surface cylindrique
```express
#35 = CYLINDRICAL_SURFACE('generated cylinder', #34, 22.627416998);
                                                 ↑    ↑
                                            placement  rayon
```

#### Plans (PLANE)
```express
#92 = PLANE('', #91);
              ↑
         placement (définit la position et orientation)
```

---

## 🔗 Topologie : Comment tout est connecté

### Hiérarchie topologique (du plus petit au plus grand)

```
VERTEX_POINT          ← Point géométrique
    ↓
EDGE_CURVE            ← Arête (ligne/courbe entre 2 points)
    ↓
ORIENTED_EDGE         ← Arête avec orientation
    ↓
EDGE_LOOP             ← Boucle fermée d'arêtes
    ↓
FACE_OUTER_BOUND      ← Contour extérieur d'une face
    ↓
ADVANCED_FACE         ← Face avec une surface
    ↓
CLOSED_SHELL          ← Enveloppe fermée de faces
    ↓
MANIFOLD_SOLID_BREP   ← Solide 3D final
```

### Exemple concret de ton fichier

#### Étape 1 : Créer les sommets
```express
#40 = CARTESIAN_POINT('Vertex', (0., 14.627416998, -5.));
#41 = VERTEX_POINT('', #40);  ← Sommet topologique basé sur le point géométrique
```

#### Étape 2 : Créer les arêtes
```express
#44 = EDGE_CURVE('', #41, #43, #39, .T.);
                     ↑     ↑     ↑    ↑
                  début  fin  courbe  sens
```

#### Étape 3 : Orienter les arêtes
```express
#65 = ORIENTED_EDGE('', *, *, #44, .F.);
                              ↑     ↑
                          arête  orientation (.T. ou .F.)
```

#### Étape 4 : Créer des boucles
```express
#64 = EDGE_LOOP('', (#65, #66, #67, #68));
                     ↑ liste d'arêtes orientées formant un contour fermé
```

#### Étape 5 : Créer les faces
```express
#69 = FACE_OUTER_BOUND('', #64, .T.);
#70 = ADVANCED_FACE('PartBody', (#69), #35, .T.);
                                 ↑      ↑     ↑
                             contours surface orientation
```

#### Étape 6 : Assembler en solide
```express
#30 = CLOSED_SHELL('Closed Shell', (#70, #87, #97, #107));
#21 = MANIFOLD_SOLID_BREP('PartBody', #30);
```

---

## 🎨 Apparence visuelle

### Couleurs
```express
#22 = COLOUR_RGB('Colour', 0.882352941176, 0.882352941176, 0.882352941176);
                           ↑ rouge         ↑ vert          ↑ bleu
                           (valeurs entre 0 et 1)
```

### Style de surface
```express
#23 = FILL_AREA_STYLE_COLOUR(' ', #22);
#24 = FILL_AREA_STYLE(' ', (#23));
#25 = SURFACE_STYLE_FILL_AREA(#24);
#26 = SURFACE_SIDE_STYLE(' ', (#25));
#27 = SURFACE_STYLE_USAGE(.BOTH., #26);  ← Appliqué des 2 côtés
#28 = PRESENTATION_STYLE_ASSIGNMENT((#27));
#29 = STYLED_ITEM(' ', (#28), #21);  ← Lié au solide #21
```

---

## 📐 Unités et contexte

```express
#8 = (LENGTH_UNIT() NAMED_UNIT(*) SI_UNIT(.MILLI., .METRE.));
                                          ↑        ↑
                                      préfixe   unité de base
```
**Signification** : Les dimensions sont en millimètres

```express
#9 = (NAMED_UNIT(*) PLANE_ANGLE_UNIT() SI_UNIT($, .RADIAN.));
```
**Signification** : Les angles sont en radians

```express
#11 = UNCERTAINTY_MEASURE_WITH_UNIT(LENGTH_MEASURE(0.005), #8, 
      'distance_accuracy_value', 'CONFUSED CURVE UNCERTAINTY');
```
**Signification** : Précision de ±0.005 mm

---

## 🧩 Comprendre ton objet : C'est quoi ta "roue" ?

Analysons la géométrie :

### Surface cylindrique
```express
#35 = CYLINDRICAL_SURFACE('generated cylinder', #34, 22.627416998);
```
- Rayon : **22.627 mm**
- Position : Centré autour de `(10, -8, -5)` avec axe X

### Cercles aux extrémités
```express
#74 = CIRCLE('generated circle', #73, 22.627416998);  ← À x=0
#79 = CIRCLE('generated circle', #78, 22.627416998);  ← À x=20
```

### Conclusion
C'est un **cylindre** de :
- **Rayon** : 22.63 mm
- **Longueur** : 20 mm (de x=0 à x=20)
- **4 faces** : 1 cylindrique + 2 plans circulaires + des faces latérales

Donc techniquement... c'est plus un **tube** ou un **pneu miniature** qu'une roue complète ! 😄

---

## 💡 Concepts clés EXPRESS

### 1. Types de valeurs

```express
'texte'              ← STRING
123.456              ← REAL (nombre réel)
123                  ← INTEGER
.T. ou .F.           ← BOOLEAN (TRUE/FALSE)
$                    ← NULL/undefined
*                    ← Dérivé (calculé automatiquement)
```

### 2. Listes et agrégats

```express
(#65, #66, #67, #68)     ← Liste d'entités
(0., 0., 0.)             ← Tuple de valeurs
```

### 3. Références circulaires permises

```express
#18 = PRODUCT_DEFINITION('...', ' ', #17, #3);
#20 = SHAPE_DEFINITION_REPRESENTATION(#19, #13);
#19 = PRODUCT_DEFINITION_SHAPE(' ', ' ', #18);  ← Référence #18 qui vient avant
```

---

## 🛠️ Exercices pratiques

### Exercice 1 : Modifier le rayon
Change le rayon du cylindre de 22.63 mm à 30 mm.
**Solution** : Modifier toutes les occurrences de `22.627416998` par `30.0`

### Exercice 2 : Changer la couleur
Mettre la couleur en rouge vif.
**Solution** : 
```express
#22 = COLOUR_RGB('Colour', 1.0, 0.0, 0.0);
                           ↑ R  ↑ G  ↑ B
```

### Exercice 3 : Comprendre la topologie
Compte combien de :
- **VERTEX_POINT** : 4 (#41, #43, #50, #57)
- **EDGE_CURVE** : 6 (#44, #51, #58, #63, #75, #80)
- **ADVANCED_FACE** : 4 (#70, #87, #97, #107)

---

## 📚 Ressources supplémentaires

### Documentation officielle
- **ISO 10303-11** : Spécification EXPRESS
- **ISO 10303-21** : Format de fichier STEP
- **ISO 10303-242** : Protocole AP242

### Sites utiles
- [CAx-IF](https://www.cax-if.org/) : Recommandations d'implémentation
- [STEP Tools](https://www.steptools.com/) : Outils et documentation
- [Wikipedia STEP](https://en.wikipedia.org/wiki/ISO_10303) : Vue d'ensemble

### Outils pour explorer les STEP
- **CAD Assistant** (OpenCascade) - Gratuit
- **FreeCAD** - Open source
- **STEP File Analyzer** - Outil d'analyse textuel

---

## 🎯 Points clés à retenir

1. **#N** = Identifiant unique pour chaque entité
2. Les **entités** sont comme des classes en POO
3. Les **références** créent un graphe de dépendances
4. La **topologie** va du point vers le solide
5. La **géométrie** définit les formes (cercles, plans, surfaces)
6. Le **style** définit l'apparence visuelle
7. Tout est **relatif** (systèmes de coordonnées locaux)

---

## 🚀 Pour aller plus loin

### Schéma EXPRESS complet d'une entité

```express
ENTITY CARTESIAN_POINT
  SUBTYPE OF (POINT);
  coordinates : LIST [1:3] OF length_measure;
WHERE
  WR1: SIZEOF(coordinates) >= 1;
END_ENTITY;
```

### Créer ton propre STEP programmatiquement

En Python avec `pythonOCC` :
```python
from OCC.Core.BRepPrimAPI import BRepPrimAPI_MakeCylinder
from OCC.Core.STEPControl import STEPControl_Writer

# Créer un cylindre
cylinder = BRepPrimAPI_MakeCylinder(22.63, 20).Shape()

# Exporter en STEP
writer = STEPControl_Writer()
writer.Transfer(cylinder, STEPControl_AsIs)
writer.Write("mon_cylindre.stp")
```

---

## ✅ Quiz final

1. Quelle est la différence entre CARTESIAN_POINT et VERTEX_POINT ?
2. Pourquoi y a-t-il des ORIENTED_EDGE ?
3. Comment EXPRESS gère-t-il les unités de mesure ?
4. Qu'est-ce qu'un MANIFOLD_SOLID_BREP ?

**Réponses** :
1. CARTESIAN_POINT = géométrie pure, VERTEX_POINT = topologie (référence un point)
2. Pour définir le sens de parcours des arêtes dans une boucle
3. Via LENGTH_UNIT, PLANE_ANGLE_UNIT dans le GEOMETRIC_REPRESENTATION_CONTEXT
4. Un solide 3D défini par ses frontières (B-Rep = Boundary Representation)

---

**Félicitations !** 🎉 Tu connais maintenant les bases d'EXPRESS et tu peux lire/comprendre un fichier STEP !
