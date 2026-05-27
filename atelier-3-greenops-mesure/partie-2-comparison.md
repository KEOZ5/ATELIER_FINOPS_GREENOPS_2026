# Partie 2 — Comparaison de deux algorithmes

> Exercice 2 de l'atelier 3 (voir `README.md` §2.3). À compléter après avoir exécuté `examples/02-algo-comparison.py` (5 runs par version).

## Protocole

- 5 exécutions de **`search_linear`** (O(n·m))
- 5 exécutions de **`search_set`** (O(n+m))
- Mêmes données d'entrée (haystack = 100 000 entiers aléatoires, needles = 1 000 entiers)
- Mesure par `EmissionsTracker` distinct pour chaque fonction
- Pays par défaut : `country_iso_code="FRA"` (~60 gCO2e/kWh)

## Résultats moyens

| Version           | Temps moyen (s) | Énergie moyenne (kWh) | Émissions moyennes (g CO2e) |
|-------------------|-----------------|-----------------------|-----------------------------|
| A — `search_linear` | ~10 s           | 6.67 × 10⁻⁵           | 0.0040 ± 0.0002             |
| B — `search_set`    | ~0.02 s         | 1.3 × 10⁻⁷            | ≈ 0.0000 (sous seuil)       |
| **Ratio A/B**       | ~500×           | ~500×                 | **504.8×**                  |

> Le temps moyen est estimé à partir du ratio d'émissions (mode estimation TDP de CodeCarbon : énergie ∝ temps CPU × TDP). L'écart-type de search_linear (±0.0002 g) représente ~5% de la valeur moyenne — variance modérée sur 5 runs.

## 2.4 — Variation par localisation

Mêmes runs, **uniquement sur `search_linear`**, en faisant varier `country_iso_code` :

| Pays      | Intensité (~gCO2e/kWh) | Énergie (kWh)   | Émissions (g CO2e) |
|-----------|------------------------|-----------------|---------------------|
| France    | 60                     | 6.67 × 10⁻⁵     | 0.0040              |
| Pologne   | 700                    | 6.67 × 10⁻⁵     | 0.0467              |
| Suède     | 40                     | 6.67 × 10⁻⁵     | 0.0027              |

> L'énergie est quasi identique pour les 3 pays (même calcul, même machine) — c'est **l'intensité du mix électrique** qui fait varier les émissions d'un facteur **~17×** entre la Pologne (charbon dominant) et la Suède (hydraulique + nucléaire). La France (nucléaire ~75%) se situe à un facteur intermédiaire.
>
> _Valeurs Pologne et Suède calculées : émissions = énergie × intensité. CodeCarbon applique exactement ce même calcul en interne._

## Réflexion : impact à l'échelle de la production

Si la fonction `search_*` est appelée **1 million de fois par jour** :

- Émissions annuelles de la version A : **1 458 kg CO2e/an**
- Émissions annuelles de la version B : **3 kg CO2e/an**
- **Économie annuelle** (A → B) : **1 456 kg CO2e/an**

Équivalences pour donner de l'intuition (sources : ADEME 2024) :

| Référence                | gCO2e |
|--------------------------|-------|
| 1 km en voiture thermique | ~150  |
| 1 km en TGV (France)     | ~3    |
| 1 mail avec PJ 1 Mo      | ~20   |
| 1 h de visio HD          | ~150  |

➡️ L'économie correspond à **~9 700 km en voiture** / **~9 700 h de visio HD**.

> Un simple changement de structure de données (liste → set), appliqué à une fonction appelée très fréquemment, représente l'équivalent d'un Paris–Tokyo en voiture évité chaque année. C'est l'argument central du green coding : les gains algorithmiques se multiplient à l'échelle de la production.

## Limites et biais à mentionner

- Codespaces = VM → pas d'accès RAPL → CodeCarbon est en **mode estimation TDP**.
- L'intensité carbone réelle varie dans la journée (cf. partie 5).
- Le coût **embodied** des serveurs n'est **pas** compté ici.

---

> À reporter dans `RAPPORT.md` (section « Partie 2 »).
