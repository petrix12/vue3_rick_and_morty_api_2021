# Vue 3 - Composition API, Vuex, API Rest - Rick And Morty
+ https://www.udemy.com/course/vue-3-composition-api-vuex-api-rest-rick-and-morty
+ [Repositorio en GitHub del Autor](https://github.com/carloscdev/udemy-vue3-rick-and-morty)
+ [Demo del autor](https://udemy-vue3-rick-and-morty.netlify.app)
+ [Mi repositorio GitHub](https://github.com/petrix12/vue3_rick_and_morty_api_2021.git)
+ [Mi demo](https://solucionespp-rick-and-morty.netlify.app)
1. Crear proyecto:
    + $ vue create 99rick-and-morty
    + Seleccionar: Manually select features
    + Check the features needed for your project:
        + Choose Vue version
        + Babel
        + Vuex
        + CSS Pre-processors
    + Choose a version of Vue.js that you want to start the project with: **3.x**
    + Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): **Sass/SCSS (with node-sass)**
    + Where do you prefer placing config for Babel, ESLint, etc.?: **In dedicated config files**
    + Save this as a preset for future projects?: **n**
2. Para ejecutar el nuevo proyecto:
    + $ cd 99rick-and-morty
    + $ npm run serve
3. Modificar **99rick-and-morty\src\App.vue**:
    ```vue
    <template>
        <div class="container">
            <h1>Rick And Morty</h1>
            <FilterByStatus />
            <FilterByName />
            <ListCharacters />
        </div>
    </template>

    <script>
    import ListCharacters from '@/components/ListCharacters'
    import FilterByStatus from '@/components/FilterByStatus'
    import FilterByName from '@/components/FilterByName'
    export default {
        name: 'App',
        components: {
            ListCharacters,
            FilterByStatus,
            FilterByName
        }
    }
    </script>

    <style lang="scss">
    :root {
        --background-body: #24282F;
        --background-card: #3C3E44;
        --text-white: #FFFFFF;
        --text-gray: #c0c0c0;
        --text-orange: #FF9800;
    }
    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }
    body {
        background-color: var(--background-body);
        color: var(--text-white);
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }
    h1 {
        margin-bottom: 3rem;
        text-align: center;
    }
    .container {
        width: 980px;
        max-width: 90%;
        margin: 5rem auto;
    }
    img {
        width: 100%;
    }
    </style>
    ```
4. Eliminar componente: **99rick-and-morty\src\components\HelloWorld.vue**.
5. Eliminar logo: **99rick-and-morty\src\assets\logo.png**.
6. Modificar **99rick-and-morty\src\store\index.js**:
    ```js
    import { createStore } from 'vuex'

    export default createStore({
        state: {
            characters: [],
            CharactersFilter: []
        },
        mutations: {
            setCharacters(state, payload){
                state.characters = payload
            },
            
            setCharactersFilter(state, payload){
                state.charactersFilter = payload
            }
        },
        actions: {
            async getCharacters({commit}){
                try {
                    const response = await fetch('https://rickandmortyapi.com/api/character')
                    const data = await response.json()
                    /* console.log(data) */
                    commit('setCharacters', data.results)
                    commit('setCharactersFilter', data.results)
                } catch (error) {
                    console.error(error)
                }
            },
            filterByStatus({commit, state}, status) {
                const filter = state.characters.filter((character) => {
                    return character.status.includes(status)
                })
                commit('setCharactersFilter', filter)
            },
            filterByName({commit, state}, name) {
                const formatName = name.toLowerCase()
                const filter = state.characters.filter((character) => {
                    const characterName = character.name.toLowerCase()
                    if(characterName.includes(formatName)) {
                        return character
                    }
                })
                commit('setCharactersFilter', filter)
            }
        },
        modules: {
        }
    })
    ```
    + **Nota**: obtener las API de https://rickandmortyapi.com/documentation
        + URL Base: https://rickandmortyapi.com/api
        + URL para obtener los personajes: https://rickandmortyapi.com/api/character
7. Crear componente **99rick-and-morty\src\components\ListCharacters.vue**:
    ```vue
    <template>
        <section>
            <div class="characters">
                <div class="characters__item" v-for="character in characters" :key="character.id">
                    <CardCharacter :character="character" />
                </div>
            </div>
        </section>
    </template>

    <script>
    import { computed, onMounted } from 'vue'
    import { useStore } from 'vuex'
    import CardCharacter from '@/components/CardCharacter'
        export default {
        name: 'Characters',
        components: {
            CardCharacter
        },
        setup() {
            const store = useStore()
            const characters = computed(() => {
                return store.state.charactersFilter
            })
            onMounted(() => {
                store.dispatch('getCharacters')
            })
            return {
                characters
            }
        }
    }
    </script>

    <style lang="scss">
    .characters {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-gap: 3rem;
        margin: 3rem 0;
    }
    </style>
    ```
8. Crear componente **99rick-and-morty\src\components\CardCharacters.vue**:
    ```vue
    <template>
        <div class="character">
            <img :src="character.image" :alt="character.name">
            <div class="character__info">
                <h3>{{ character.name }}</h3>
                <div class="status">
                    <span
                        :class="
                            character.status == 'Alive' ? 'alive' :
                            character.status == 'Dead' ? 'dead' :
                            'default'
                        "
                    ></span>
                    <span>{{ character.status }} - {{ character.species }}</span>
                </div>
                <div class="origen">
                    <span>
                    Origin: 
                    {{ character.origin.name }}
                    </span>
                </div>
                <div class="location">
                    <span>
                    Location: 
                    {{ character.location.name }}
                    </span>
                </div>
            </div>
        </div>
    </template>

    <script>
    export default {
        props: ['character']
    }
    </script>

    <style lang="scss">
    .character {
        background-color: var(--background-card);
        border-radius: 20px;
        box-shadow: 0 0 10px 1px var(--background-body);
        overflow: hidden;
        cursor: pointer;
        transition: transform 200ms ease-in-out;
        height: 100%;
        &:hover {
            transform: scale(1.05);
            h3 {
                color: var(--text-orange);
            }
        }
        span {
            color: var(--text-gray);
        }
        h3 {
            margin-bottom: 0.5rem;
        }
        &__info {
            padding: 1.5rem;
            .status{
                display: flex;
                align-items: center;
                margin-bottom: 0.5rem;
                span {
                    color: var(--text-gray);
                    &:first-child {
                        width: 10px;
                        height: 10px;
                        border-radius: 50%;
                        margin-right: 0.5rem;
                    }
                }
                .alive {
                    background-color: green;
                }
                .dead {
                    background-color: red;
                }
                .default {
                    background-color: white;
                }
            }
            .origen {
                margin-bottom: 0.5rem;
            }
        }
    }
    </style>
    ```
9. Crear componente **99rick-and-morty\src\components\FilterByStatus.vue**:
    ```vue
    <template>
        <div class="filter">
            <div class="item" @click="filter('')">
                All
            </div>
            <div class="item" @click="filter('Alive')">
                Alive
            </div>
            <div class="item" @click="filter('Dead')">
                Dead
            </div>
            <div class="item" @click="filter('unknown')">
                Unknown
            </div>
        </div>
    </template>

    <script>
    import { useStore } from 'vuex'
    export default {
        setup() {
            const store = useStore()
            const filter = ((status) => {
                store.dispatch('filterByStatus', status)
            })
            return {
                filter
            }
        }
    }
    </script>

    <style lang="scss">
    .filter {
        width: 400px;
        margin: 0 auto;
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        border-radius: 10px;
        overflow: hidden;
        .item {
            padding: 1rem 0.5rem;
            background-color: var(--background-card);
            text-align: center;
            cursor: pointer;
            &:hover {
                color: var(--text-orange);
            }
        }
    }
    </style>
    ```
10. Crear componente **99rick-and-morty\src\components\FilterByName.vue**:
    ```vue
    <template>
        <div class="search">
            <input
                type="text"
                placeholder="Search by name"
                v-model="name"
                @keyup="filter()"
            >
        </div>
    </template>

    <script>
    import { ref } from 'vue'
    import { useStore } from 'vuex'
    export default {
        setup() {
                const name = ref('')
                const store = useStore()
                const filter = () => {
                store.dispatch('filterByName', name.value)
            }
            return {
                name,
                filter
            }
        }
    }
    </script>

    <style lang="scss">
    .search {
        width: 400px;
        margin: 3rem auto 0;
        input {
            height: 53px;
            width: 400px;
            border: none;
            border-radius: 10px;
            padding: 0 0.5rem;
        }
    }
    </style>
    ```
11. Crear proyecto en la página de [GitHub](https://github.com) con el nombre: **vue3_rick_and_morty_api_2021**.
    + **Description**: Proyecto para seguir el curso de Udemy Vue 3 - Composition API, Vuex, API Rest - Rick And Morty de Carlos Córdova. URL: https://www.udemy.com/course/vue-3-composition-api-vuex-api-rest-rick-and-morty.
    + **Public**.
12. En la ubicación raíz del proyecto en la terminal de la máquina local:
    + $ git init
    + $ git add .
    + $ git commit -m "Rick And Morty"
    + $ git branch -M main
    + $ git remote add origin https://github.com/petrix12/vue3_rick_and_morty_api_2021.git
    + $ git push -u origin main
13. Ir a la página de [netlify](https://www.netlify.com), iniciar sesión e ir a **Site**.
14. Arrastrar la carpeta para distribución **99rick-and-morty** en la página de netlify.
15. Ir a **Site settings** y luego a **Change site name** y cambiar el **Site name** a **solucionespp-rick-and-morty**.
    + **Nota**: ahora podemos ver nuestra aplicación en producción en: https://solucionespp-rick-and-morty.netlify.app