<template>
    <div v-if="toolList.length" class="content-tools">
        <div class="btn-group" role="group" aria-label="content-tools">
            <template v-if="toolList.includes('star')">
                <nuxt-link v-if="$store.state.page.data.starred" :to="doc_action_link($store.state.page.data.document, 'member/unstar')" class="btn btn-secondary tools-btn" v-tooltip="`Unstar`">
                    <span class="fa fa-star"></span>
                    <span class="star-count">{{ $store.state.page.data.star_count }}</span>
                </nuxt-link>
                <nuxt-link v-else-if="$store.state.page.data.star_count >= 0" :to="doc_action_link($store.state.page.data.document, 'member/star')" class="btn btn-secondary tools-btn" v-tooltip="`Star`">
                    <span class="fa fa-star-o"></span>
                    <span class="star-count">{{ $store.state.page.data.star_count }}</span>
                </nuxt-link>
            </template>
            <nuxt-link v-if="toolList.includes('wiki')" :to="doc_action_link($store.state.page.data.document, 'w', rev ? { rev } : undefined)" class="btn btn-secondary tools-btn">보기</nuxt-link>
            <nuxt-link v-if="toolList.includes('backlink')" :to="doc_action_link($store.state.page.data.document, 'backlink')" class="btn btn-secondary tools-btn">역링크</nuxt-link>
            <nuxt-link v-if="toolList.includes('discuss')" :to="doc_action_link($store.state.page.data.document, 'discuss')" class="btn btn-secondary tools-btn" :class="{ 'btn-discuss-progress': $store.state.page.data.discuss_progress }">토론</nuxt-link>
            <template v-if="toolList.includes('edit')">
                <a v-if="$store.state.page.data.editable === true && $store.state.page.data.edit_acl_message" href="#" @click.prevent="$emit('onClickEditBtn')" class="btn btn-secondary tools-btn"><span class="fa fa-pencil-square"></span> 편집 요청</a>
                <a v-else-if="$store.state.page.data.editable === false && $store.state.page.data.edit_acl_message" href="#" @click.prevent="$emit('onClickEditBtn')" class="btn btn-secondary tools-btn"><span class="fa fa-lock"></span> 편집</a>
                <nuxt-link v-else-if="$store.state.page.data.editable === true && !$store.state.page.data.edit_acl_message" :to="doc_action_link($store.state.page.data.document, 'edit')" class="btn btn-secondary tools-btn"><span class="fa fa-edit"></span> 편집</nuxt-link>
                <nuxt-link v-else :to="doc_action_link($store.state.page.data.document, 'edit')" class="btn btn-secondary tools-btn">편집</nuxt-link>
            </template>
            <nuxt-link v-if="toolList.includes('history')" :to="doc_action_link($store.state.page.data.document, 'history', rev ? { from: rev } : undefined)" class="btn btn-secondary tools-btn">역사</nuxt-link>
            <nuxt-link v-if="toolList.includes('acl')" :to="doc_action_link($store.state.page.data.document, 'acl')" class="btn btn-secondary tools-btn">ACL</nuxt-link>
            <nuxt-link v-if="toolList.includes('delete')" :to="doc_action_link($store.state.page.data.document, 'delete')" class="btn btn-danger tools-btn">삭제</nuxt-link>
            <nuxt-link v-if="toolList.includes('move')" :to="doc_action_link($store.state.page.data.document, 'move')"  class="btn btn-secondary tools-btn">이동</nuxt-link>
            <nuxt-link v-if="toolList.includes('userdoc')" :to="doc_action_link(user_doc($store.state.page.data.account.name), 'w')" class="btn btn-secondary tools-btn">사용자 문서</nuxt-link>
            <a v-if="toolList.includes('block')" href="#" @click.prevent="block" class="btn btn-danger tools-btn">차단</a>
            <template v-if="toolList.includes('contribution') || toolList.includes('raw') || toolList.includes('blame') || toolList.includes('diff') || toolList.includes('revert') || toolList.includes('menu')">
                <dropdown class="btn btn-secondary tools-btn">
                    <template #toggle>
                        <div class="dropdown-toggle"><span class="caret"></span></div>
                    </template>
                    <div class="dropdown-menu dropdown-menu-right" role="menu">
                        <nuxt-link v-if="toolList.includes('contribution')" :to="contribution_link($store.state.page.data.user.uuid)" class="dropdown-item">기여 목록</nuxt-link>
                        <nuxt-link v-if="toolList.includes('raw')" :to="doc_action_link($store.state.page.data.document, 'raw', rev ? { rev } : undefined)" class="dropdown-item">RAW</nuxt-link>
                        <nuxt-link v-if="toolList.includes('blame')" :to="doc_action_link($store.state.page.data.document, 'blame', rev ? { rev } : undefined)" class="dropdown-item">Blame</nuxt-link>
                        <nuxt-link v-if="toolList.includes('diff')" :to="doc_action_link($store.state.page.data.document, 'diff', rev ? { rev, oldrev: rev - 1 } : undefined)" class="dropdown-item">이전 리버전과 비교</nuxt-link>
                        <nuxt-link v-if="toolList.includes('revert')" :to="doc_action_link($store.state.page.data.document, 'revert', rev ? { rev } : undefined)" class="dropdown-item">이 리버전으로 되돌리기</nuxt-link>
                        <nuxt-link v-if="toolList.includes('menu')" v-for="m in $store.state.page.data.menus" :key="m.to" :to="m.to" class="dropdown-item">{{ m.title }}</nuxt-link>
                    </div>
                </dropdown>
            </template>
        </div>
    </div>
</template>

<script>
import { vTooltip } from 'floating-vue';
import Common from '~/mixins/common';
import Dropdown from '../components/dropdown';

export default {
    directives: { tooltip: vTooltip },
    mixins: [Common],
    components: {
        Dropdown
    },
    computed: {
        convenience() {
            return this.$store.state.localConfig['liberty.rev_convenience'] !== false;
        },
        rev() {
            return this.convenience && (this.$store.state.page.data.rev || this.$route.query.rev);
        },
        toolList() {
            const tools = [];
            switch (this.$store.state.page.viewName) {
                case 'wiki':
                    tools.push('backlink', 'acl');
                    if (this.$store.state.page.data.date !== null) {
                        tools.push('star', 'discuss', 'edit', 'history');
                        if (this.convenience) tools.push('raw', 'blame');
                        if (this.rev) tools.push('diff');
                        if (this.rev && this.$store.state.page.data.editable === true && !this.$store.state.page.data.edit_acl_message) tools.push('revert');
                    }
                    if (this.$store.state.page.data.user) tools.push('contribution');
                    break;
                case 'notfound':
                    tools.push('backlink', 'discuss', 'edit', 'history', 'acl');
                    break;
                case 'backlink':
                    tools.push('history', 'edit');
                    break;
                case 'edit':
                case 'edit_edit_request':
                    tools.push('backlink', 'delete', 'move');
                    break;
                case 'history':
                    tools.push('edit', 'backlink');
                    break;
                case 'raw':
                case 'diff':
                case 'blame':
                    tools.push('history', 'edit');
                    if (this.convenience) tools.push('wiki');
                    break;
                case 'thread':
                case 'thread_list_close':
                case 'edit_request':
                case 'edit_request_close':
                    tools.push('discuss');
                    break;
                case 'contribution':
                case 'contribution_discuss':
                    if (this.$store.state.page.data.account.type === 1) tools.push('userdoc');
                    if (this.$store.state.session.quick_block && this.$store.state.page.data.account.type !== -1) tools.push('block');
                    break;
            }
            if (this.$store.state.page.data.menus?.length) tools.push('menu');
            return tools;
        }
    },
    methods: {
        block() {
            this.openQuickACLGroup({
                username: this.$store.state.page.data.account.type === 1 ? "".concat(this.$store.state.page.data.account.name) : undefined,
                ip: this.$store.state.page.data.account.type === 0 ? "".concat(this.$store.state.page.data.account.name) : undefined,
                note: "".concat("기여 목록 긴급차단")
            });
        }
    }
}
</script>