# Réponses au TP Vue.js

## Q1 : Un fichier .vue a 3 sections séparées. Un fichier .tsx mélange tout. Quel avantage/inconvénient de chaque approche ?

**Avantages du `.vue` (séparation par sections)** :
- Lisibilité : le template, la logique et le style sont clairement distincts.
- Maintenabilité : plus facile pour un groupe de développeurs (ex. un designer peut modifier le template sans toucher au script).
- Scoped CSS : le style peut être automatiquement limité au composant.

**Inconvénients** :
- Moins flexible : on ne peut pas facilement interpoler du HTML dans la logique (pas de JSX).
- Nécessite un compilateur spécifique (Vue CLI, Vite).

**Avantages du `.tsx` (JSX)** :
- Flexibilité totale : on peut mélanger logique et balisage (ex. `tasks.map(...)` directement dans le rendu).
- Puissance de JavaScript : conditions, boucles, expressions sans directives spécifiques.

**Inconvénients** :
- Moins lisible pour les gros composants (JSX + logique mélangés).
- Le style est souvent séparé ailleurs (CSS Modules ou styled-components).

---

## Q2 : Pourquoi React ne propose-t-il pas de two-way binding natif ?

React privilégie le **flux de données unidirectionnel** explicite (état → vue).  
Le two-way binding (v-model) masque le fait que l’état est modifié indirectement.  
React estime que la clarté de `value` + `onChange` est préférable pour les grosses applications, car toute modification de l’état est visible dans un gestionnaire d’événement.  
Cela rend le débogage plus prévisible (pas de "magie").

---

## Q3 : En React, `tasks.push(data)` est INTERDIT (immutabilité). En Vue, `tasks.value.push(data)` fonctionne. Expliquez avec le mot « Proxy ».

Vue 3 utilise des **Proxy** JavaScript pour envelopper les objets réactifs (`ref` et `reactive`).  
Quand on fait `tasks.value.push(...)`, le Proxy intercepte cette mutation et notifie automatiquement les composants dépendants.  
React n’utilise pas de Proxy ; il compare les anciennes et nouvelles références des objets. Sans immutabilité, il ne peut pas détecter les changements.

---

## Q4 : `useEffect(fn, [])` vs `onMounted(fn)`. Lequel est plus lisible ? Pourquoi React utilise un tableau de dépendances ?

`onMounted` est **plus lisible** car il exprime clairement "au montage" sans ambiguïté.  
`useEffect` avec `[]` est un détournement (effet de bord sans dépendances).  

React utilise un tableau de dépendances pour **réexécuter les effets** lorsqu’une valeur change (ex. `[userId]`).  
Cela permet de synchroniser un effet avec des props/state, là où Vue propose `watchEffect` ou `watch`.

---

## Q5 : React passe des fonctions en props (`onDelete={fn}`). Vue émet des événements (`@delete`). Quelle approche est plus proche du HTML natif ? Pourquoi ?

Vue est plus proche du HTML natif car :
- Les événements personnalisés (`@delete`) ressemblent aux événements DOM (`onclick`).
- Le mécanisme d’écoute (`emit`) est similaire au `new CustomEvent`.

React (fonctions en props) est plus proche du JavaScript pur et non du HTML.

---

## Q6 : En React, si on oublie `onDelete`, le composant crash au clic. En Vue, si on oublie `@delete`, que se passe-t-il ?

Vue **ignore silencieusement** l'émission de l'événement.  
Le code enfant appelle `emit('delete')`, personne ne l’écoute → rien ne se passe (pas d’erreur).  
Cela rend les composants plus robustes (pas de crash) mais peut masquer des bugs.

---

## Q7 : `useParams` / `useNavigate` vs `useRoute` / `useRouter` : logique différente ou juste les noms ?

La logique est **identique** mais les noms diffèrent légèrement :
- `useParams()` → `useRoute().params`
- `useNavigate()` → `useRouter().push()`
- `useLocation()` → `useRoute()`

Les deux approches sont des hooks qui exposent l’état du routeur.

---

## Q8 : Routes dans JSX (React) vs fichier de config (Vue). Avantage de la séparation de Vue ?

Avantages de la séparation Vue (fichier `router/index.ts`) :
- **Centralisation** : toutes les routes sont visibles en un seul endroit.
- **Moins de boilerplate JSX** : pas besoin d’envelopper `<Routes>` dans un composant.
- **Plus facile pour les permissions/génération automatique** : un fichier de config peut être généré par un script.

L’approche React (JSX) est plus "déclarative" mais peut éparpiller les routes dans l’arbre des composants.

---

## Q9 : Redux Toolkit vs Pinia – nombre de concepts à apprendre.

**Redux Toolkit** (beaucoup de concepts) :
- `createSlice`
- `configureStore`
- `<Provider>`
- `useSelector`
- `useDispatch`
- `reducers`, `actions`, `immer`
- thunk ou `createAsyncThunk`

**Pinia** (peu de concepts) :
- `defineStore`
- `useTaskStore()`
- `ref` / `state`, `actions`

👉 Pinia est beaucoup plus simple pour les projets de taille moyenne.

---

## Q10 : `dispatch(addTask(...))` vs `store.addTask(...)`. Le plus intuitif ? Redux a-t-il un avantage ?

`store.addTask()` est plus intuitif (appel de méthode normale).  
`dispatch(action)` est indirect mais décuple la puissance des **DevTools** (time-travel, log des actions, rejouer des actions).  
Pinia a des DevTools mais moins avancés sur le time-travel.

Avantage Redux : traçabilité absolue des actions.  
Inconvénient : boilerplate.

---

## Q11 : Concepts IDENTIQUES vs FONDAMENTALEMENT différents entre React et Vue ?

**Identiques** (nom différent) :
- État local (`useState` / `ref`)
- Effet de bord (`useEffect` / `watchEffect` ou `onMounted`)
- Props
- Callback vers parent (callback prop / `emit`)

**Fondamentalement différents** :
- React : immutabilité explicite, JSX, pas de two-way binding, fonctions vs classes.
- Vue : mutations directes (Proxy), templates directives (`v-if`, `v-for`), two-way binding (`v-model`).

---

## Q12 : Vue est plus « magique » (v-model, mutations directes). Avantage ou inconvénient ?

**Avantage** : rapidité de développement, moins de code, plus intuitif pour les débutants.

**Inconvénient** : quand un bug arrive (ex. mutation non détectée dans un cas limite), la « magie » rend le débogage plus difficile.  
React, plus verbeux, est plus prévisible et facile à déboguer avec les DevTools.

Donc : avantage pour les petits/moyens projets, inconvénient pour les très grosses apps critiques.

---

## Q13 : E-commerce 50+ pages, équipe 10 devs, dashboard admin complexe : React ou Vue ?

**React** est mieux adapté car :
- Écosystème plus large (librairies UI, graphiques, forms, etc.)
- Emploi/recrutement plus facile (énorme marché)
- Flexibilité JSX pour des dashboards très dynamiques
- Outils de test plus matures (Jest, React Testing Library)
- Next.js pour le SEO et l’e-commerce

Vue pourrait fonctionner mais l’entreprise préférera React pour du long terme avec une grosse équipe.

---

## Q14 : Débutant sans expérience framework : React ou Vue en premier ?

**Vue** est meilleur pour commencer :
- Syntaxe plus simple et proche du HTML.
- Moins de concepts initiaux (pas d’immutabilité, de JSX, de fonctions pures).
- `v-model`, `v-if`, `v-for` sont immédiats.
- La courbe d’apprentissage est plus douce.

Ensuite, apprendre React est facile car on comprend déjà les composants, props, état, cycle de vie.

sinon si vous maitrisez déjà react, la transition vers vue.js sera facile, juste une syntaxe qui est différente

---

## Tableau comparatif final

| Concept                   | React                                                     | Vue 3                                                     |
|---------                  |-------                                                    |-------                                                    |
| State local               | `const [x, setX] = useState(0)`                           | `const x = ref(0)`                                        |
| Two-way binding           | `value={x} onChange={e => setX(e.target.value)}`          | `v-model="x"`                                             |
| Fetch au montage          | `useEffect(() => { fetch() }, [])`                        | `onMounted(() => { fetch() })`                            |
| Ajouter au state          | `setItems(prev => [...prev, data])`                       | `items.value.push(data)`                                  |
| Boucle template           | `items.map(i => <li key={i.id}>...</li>)`                 | `<li v-for="i in items" :key="i.id">`                     |
| Conditionnel              | `{condition && <div>...</div>}`                           | `<div v-if="condition">...</div>`                         |
| Event                     | `onClick={handler}`                                       | `@click="handler"`                                        |
| Props enfant              | `<Child name={x} />`                                      | `<Child :name="x" />`                                     |
| Callback parent           | `<Child onDelete={handler} />`                            | `<Child @delete="handler" />`                             |
| Router lien               | `<Link to="/x">Lien</Link>`                               | `<RouterLink to="/x">Lien</RouterLink>`                   |
| Params URL                | `const { id } = useParams()`                              | `useRoute().params.id`                                    |
| Navigate                  | `const nav = useNavigate(); nav("/r")`                    | `const router = useRouter(); router.push("/r")`           |
| State global              | `createSlice` + `useSelector` + `useDispatch`             | `defineStore` + `useTaskStore()`                          |
| Appeler action            | `dispatch(addTask(title))`                                | `store.addTask(title)`                                    |
| Style isolé               | CSS Modules / `.module.css`                               | `<style scoped>`                                          |
