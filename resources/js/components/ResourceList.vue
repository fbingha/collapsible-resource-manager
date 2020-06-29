<template>

    <ul class="second-level list-reset" v-if="resources.length">

        <li 
            class="second-level-li leading-tight text-sm ml-4" 
            v-for="resource of resources" 
            :class="{ 'pl-3 py-2': resource.groups == undefined }">

            <collapsible-resource-manager 
                v-if="resource.groups != undefined"
                :data="resource"
                :secondLevelMenu="true"
                :remember-menu-state="rememberMenuState"/>

            <template v-else>

                <a v-if="resource.absolute"
                   class="relative text-white text-justify no-underline dim"
                   :href="resource.route"
                   :target="resource.route.startsWith('http') ? '_blank' : '_self'">

                    {{ resource.label }}

                    <svg v-if="resource.route.startsWith('http')"
                         class="absolute icon"
                         viewBox="0 0 24 24"
                         width="24"
                         height="24">
                        <path
                            fill="currentColor"
                            d="M19 6.41L8.7 16.71a1 1 0 1 1-1.4-1.42L17.58 5H14a1 1 0 0 1 0-2h6a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0V6.41zM17 14a1 1 0 0 1 2 0v5a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V7c0-1.1.9-2 2-2h5a1 1 0 0 1 0 2H5v12h12v-5z"/>
                    </svg>

                </a>

                <router-link 
                    v-else-if="resource.index" 
                    class="relative text-white text-justify no-underline dim"
                    :to="{ name: 'index', params: { resourceName: resource.route } }">

                    <div v-if="resource.icon" class="absolute icon flex" v-html="resource.icon"/>

                    {{ resource.label }}

                </router-link>

                <router-link 
                    v-else 
                    class="relative text-white text-justify no-underline dim"
                    :to="{ name: resource.route, params: resource.params }">

                    <div v-if="resource.icon" class="absolute icon flex" v-html="resource.icon"/>

                    {{ resource.label }}

                </router-link>

            </template>

        </li>

    </ul>

</template>

<script>

    export default {
        name: 'ResourceList',
        props: {
            resources: { type: Array, required: true },
            rememberMenuState: { type: Boolean, required: true },
        }
    }

</script>

<style scoped>
.second-level {
    background: #3e4552;
}
.second-level-li {
    border-left: 1px solid rgba(255, 255, 255, 0.08);
    border-top: 1px solid rgba(255, 255, 255, 0.08);
}
.icon {
    width: 15px;
    top: -3px;
    left: -25px;
}
</style>
