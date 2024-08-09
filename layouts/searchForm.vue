<template>
    <form id="searchform" class="form-inline" @submit.prevent>
        <div class="input-group">
            <div class="input-search">
                <input type="search" name="q" placeholder="검색" accesskey="f" class="form-control" id="searchInput" autocomplete="off" v-on:input="searchText = $event.target.value" v-model="searchTextModel" @blur="blur" @focus="focus" @input="inputChange" @keydown.enter="keyEnter" @keydown.tab="keyEnter" @keydown.up="keyUp" @keydown.down="keyDown">
                <div v-if="show" class="v-autocomplete-list">
                    <div class="v-autocomplete-list-item" v-for="(item, i) in internalItems" @click="onClickItem(item)" v-bind:key="i" :class="{'v-autocomplete-item-active': i === cursor}" @mouseover="cursor = i">
                        <div>{{ item }}</div>
                    </div>
                </div>
            </div>
            <span class="input-group-btn">
              <button type="submit" name="fulltext" value="검색" class="btn btn-secondary" @click="onClickSearch"><span class="fa fa-search"></span></button>
              <button type="submit" name="fulltext" value="보기" class="btn btn-secondary" @click="onClickMove"><span class="fa fa-arrow-right"></span></button>
            </span>
        </div>
    </form>
</template>

<style scoped>
.v-autocomplete-list {
    position: absolute;
    z-index: 3;
    border: 1px solid #CCC;
    background-color: #fff;
    width: 10.8rem;
}

@media (max-width: 1023px) {
    .v-autocomplete-list {
        width: 100%;
    }
}

.theseed-dark-mode .v-autocomplete-list {
    background-color: #2d2f34;
    border: 1px solid #383b40;
}

.v-autocomplete-list-item {
    cursor: pointer;
    color: #373a3c;
    padding: 0.5rem;
    word-break: break-all;
}

.theseed-dark-mode .v-autocomplete-list-item {
    color: #ddd;
}

.v-autocomplete-list-item.v-autocomplete-item-active {
    background-color: #f3f6fa;
}

.theseed-dark-mode .v-autocomplete-list-item.v-autocomplete-item-active {
    background-color: #383b40;
}
</style>

<script>
import AutocompleteMixin from '~/mixins/autocomplete';
import Common from '~/mixins/common';

export default {
    mixins: [AutocompleteMixin],
    methods: {
        onClickSearch() {
            if (!this.searchText) return;
            this.$router.push('/Search?q=' + encodeURIComponent(this.searchText));
        },
        onClickMove() {
            if (!this.searchText) return;
            this.$router.push(Common.methods.doc_action_link(this.searchText, 'w'));
        }
    },
    watch: {
        $route() {
            if (this.$store.state.localConfig["liberty.reset_search_on_move"] !== false) this.reset();
        }
    }
}
</script>