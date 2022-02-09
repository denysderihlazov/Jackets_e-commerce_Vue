<template>
    <div class="home">
        <section class="hero is-medium mb-6 welcome_section">
            <div class="hero-body has-text-centered">
                <p class="title mb-6">
                    {{ $t('welcome') }}
                </p>
                <p class="subtitle">
                    {{ $t('best') }}
                </p>
            </div>
        </section>

        <div class="columns is-multiline product_block" >
            <div class="column is-12">
                <h2 class="is-size-2 has-text-centered">{{ $t('latest_products') }}</h2>
            </div>


            <ProductBox
                v-for="product in latestProducts"
                v-bind:key="product.id"
                v-bind:product="product" />
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import ProductBox from '@/components/ProductBox'

export default {
    name: 'Home',
    data() {
        return {
            latestProducts: []
        }
    },
    components: {
        ProductBox
    },
    mounted() {
        this.getLatestProducts()

        document.title = 'Home | Djackets'
    },
    methods: {
        async getLatestProducts() {
            this.$store.commit('setIsLoading', true)

            await axios
                .get('/api/v1/latest-products/')
                .then(response => {
                    this.latestProducts = response.data
                })
                .catch(error => {
                    console.log(error)
                })
            this.$store.commit('setIsLoading', false)

        }
    }
}
</script>

<style lang="scss">



</style>

