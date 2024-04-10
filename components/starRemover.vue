<template>
    <div v-if="$route.fullPath === '/member/starred_documents'">
        <select v-model="selectItem" type="text" class="form-control">
            <option v-for="item in itemList" :key="item" :value="item">{{ item }}</option>
        </select>
        <button @click="onclickRemove" type="submit" class="btn btn-danger">삭제</button>
    </div>
</template>

<style scoped>
div {
    margin-bottom: 1rem;
}

select {
    width: 70%;
    display: inline-block;
}

button {
    display: inline-block;
    margin-left: 10px;
}
</style>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    data() {
        return {
            itemList: [],
            selectItem: ''
        }
    },
    methods: {
        onClickRemove() {
            if (this.selectItem) this.$router.push(this.doc_action_link(this.selectItem, 'member/unstar'));
        },
        refreshItem(document) {
            let docs = document.querySelectorAll('.wiki-article div>ul>li>a');
            for (const doc of docs) {
                this.itemList.push(doc.innerText);
            }
        }
    },
    mounted() {
        this.$nextTick(() => { 
            this.refreshItem(document);
        });
    },
    watch: {
        $route() {
            this.$nextTick(() => { 
                this.refreshItem(document);
            });
        }
    }
}
</script>