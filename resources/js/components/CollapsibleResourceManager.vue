<template>
    <div 
        v-if="!isEmpty" 
        :class="{ 
            'secondLevelMenu': secondLevelMenu, 
            'mb-8 list-wrap': !secondLevelMenu,
        }">
        
        <h3 class="flex items-center font-normal text-white mb-6 text-base no-underline" v-if="data.title">
            <div v-if="data.icon" class="sidebar-icon" v-html="data.icon"/>
            <svg v-else class="sidebar-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                <path fill="var(--sidebar-icon)"
                      d="M3 1h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2H3c-1.1045695 0-2-.8954305-2-2V3c0-1.1045695.8954305-2 2-2zm0 2v4h4V3H3zm10-2h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2h-4c-1.1045695 0-2-.8954305-2-2V3c0-1.1045695.8954305-2 2-2zm0 2v4h4V3h-4zM3 11h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2H3c-1.1045695 0-2-.8954305-2-2v-4c0-1.1045695.8954305-2 2-2zm0 2v4h4v-4H3zm10-2h4c1.1045695 0 2 .8954305 2 2v4c0 1.1045695-.8954305 2-2 2h-4c-1.1045695 0-2-.8954305-2-2v-4c0-1.1045695.8954305-2 2-2zm0 2v4h4v-4h-4z"/>
            </svg>
            <span class="sidebar-label">{{ data.title }}</span>
        </h3>

        <ResourceList 
            class="resources-only"
            v-if="data.resources"
            :resources="data.resources"
            :remember-menu-state="rememberMenuState"/>

        <template v-for="(group, index) of data.groups" v-if="group.resources.length">

            <h4 class="top-level relative select-none ml-0 p-3 text-sm cursor-pointer text-white"
                v-if="group.title"
                @click="toggleGroup(index)">

                {{ group.title }}

                <div class="absolute flex flex-auto collapsible-indicator">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24">
                        <path v-if="activeMenu[index]"
                              fill="currentColor"
                              d="M16 12c0 .55-.45 1-1 1H9c-.55 0-1-.45-1-1s.45-1 1-1h6c.55 0 1 .45 1 1z"/>
                        <path v-else
                              fill="currentColor"
                              d="M13 11h2c.55 0 1 .45 1 1s-.45 1-1 1h-2v2c0 .55-.45 1-1 1s-1-.45-1-1v-2H9c-.55 0-1-.45-1-1s.45-1 1-1h2V9c0-.55.45-1 1-1s1 .45 1 1v2z"/>
                    </svg>
                </div>

            </h4>

            <CollapseTransition :duration="150">
                <ResourceList 
                    v-if="activeMenu[index]"
                    :resources="group.resources"
                    :remember-menu-state="rememberMenuState"/>
            </CollapseTransition>

        </template>

    </div>
</template>

<script>

    import { CollapseTransition } from 'vue2-transitions'
    import ResourceList from './ResourceList'

    export default {
        name: 'CollapsibleResourceManager',
        components: { CollapseTransition, ResourceList },
        props: {
            data: { type: Object, required: true },
            rememberMenuState: { type: Boolean, default: false },
            secondLevelMenu: {type: Boolean, default: false},
        },
        data() {
            return {
                activeMenu: this.data.groups.map(group => !!group.expanded),
            }
        },
        created() {
            if (this.rememberMenuState) {
                const state = localStorage.getItem(this.cacheKey)

                if (state) {
                    const activeMenu = JSON.parse(state)

                    if (Array.isArray(activeMenu)) {
                        this.activeMenu = activeMenu
                    }
                }

                this.$watch(() => this.activeMenu, (state) => {
                    localStorage.setItem(this.cacheKey, JSON.stringify(state))
                })
            }
        },

        computed: {
            cacheKey() {
                return `menu-state.${this.data.key}`;
            },
            isEmpty() {
                return this.data.resources.length + this.data.groups.filter(group => group.resources.length).length === 0;
            }
        },

        methods: {
            toggleGroup(index) {
                this.$set(this.activeMenu, index, !this.activeMenu[ index ])
            }
        }
    }

</script>

<style>
.list-wrap {
    margin-left: -1.5rem;
    margin-right: -1.5rem;
}

.top-level {
    background: #454d5b;
    border-top: 1px solid rgba(255, 255, 255, 0.15);
}
.collapsible-indicator {
    top: 6px;
    right: 6px;
}

.resources-only li:first-child {
    padding-top: 0;
}

.secondLevelMenu h4:first-child {
    margin-top: 0;
}

.secondLevelMenu h4 {
    margin-left: 0 !important;
}
</style>
