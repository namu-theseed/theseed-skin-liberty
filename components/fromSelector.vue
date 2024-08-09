<template>
    <div v-if="$store.state.localConfig['liberty.rev_selector'] !== false">
        <input v-model="until" type="checkbox">
        <input v-model="revInput" type="text" inputmode="numeric" pattern="\d*" class="form-control" placeholder="rev">
        <button @click="onClickSearch" type="button" class="btn btn-secondary">
            <span class="fa fa-search"></span>
        </button>
    </div>
</template>

<style scoped>
div {
    float: right;
    margin: 0.6rem;
}

input[type="text"] {
    width: 5rem;
    display: inline-block;
}

input[type="checkbox"] {
    margin-right: 0.6rem;
    vertical-align: middle;
}

button {
    margin-left: 0.2rem;
}

button > .fa {
    font-size: 0.9rem;
}

.theseed-dark-mode input[type="text"],
.theseed-dark-mode button {
    background-color: #27292d;
    border-color: #383b40;
    color: #ddd;
}

.theseed-dark-mode button:hover {
    background-color: #2d2f34;
    border-color: #383b40;
}
</style>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    data() {
        return {
            until: !!this.$route.query.until,
            revInput: (this.$route.query.from || this.$route.query.until) ?? ''
        }
    },
    methods: {
        onClickSearch() {
            if (!this.revInput) this.$router.push(this.doc_action_link(this.$store.state.page.data.document, 'history'));
            else this.$router.push(this.doc_action_link(this.$store.state.page.data.document, 'history', this.until ? { until: this.revInput } : { from: this.revInput }));
        }
    },
    watch: {
        '$route.query'() {
            this.until = !!this.$route.query.until;
            this.revInput = (this.$route.query.from || this.$route.query.until) ?? '';
        }
    }
}
</script>