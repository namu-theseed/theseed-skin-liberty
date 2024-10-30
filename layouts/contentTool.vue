<template>
    <div v-if="main.length" class="content-tools">
        <div class="btn-group">
            <template v-for="l in main" :key="l.to">
                <a v-if="l.onclick" @click.prevent="l.onclick" href="#" class="btn btn-secondary tools-btn" :class="l.class" v-tooltip="l.tooltip" v-text="l.title" v-html="l.html"></a>
                <nuxt-link v-else :to="l.to" class="btn btn-secondary tools-btn" :class="l.class" v-tooltip="l.tooltip" v-text="l.title" v-html="l.html"></nuxt-link>
            </template>
            <template v-if="menu.length">
                <dropdown class="btn btn-secondary tools-btn">
                    <template #toggle>
                        <div class="dropdown-toggle"><span class="caret"></span></div>
                    </template>
                    <div class="dropdown-menu dropdown-menu-right" role="menu">
                        <template v-for="m in menu" :key="m.to">
                            <a v-if="m.onclick" @click.prevent="m.onclick" href="#" class="dropdown-item" :class="m.class">{{ m.title }}</a>
                            <nuxt-link v-else :to="m.to" class="dropdown-item" :class="m.class">{{ m.title }}</nuxt-link>
                        </template>
                    </div>
                </dropdown>
            </template>
        </div>
    </div>
</template>

<style scoped>
.content-tools {
    padding-right: 1rem;
    padding-top: 1rem;
    float: right;
}

.content-tools .tools-btn {
    font-size: 0.9rem;
    padding: 0.4rem 0.8rem;
}

.content-tools .tools-btn:deep().fa-star,
.content-tools .tools-btn:deep().fa-star-o {
    color: #ff6200;
    margin-right: 0.1em;
}

.content-tools .dropdown.btn.tools-btn {
    padding: 0;
}

.content-tools .dropdown-toggle {
    padding: 0.4rem 0.3rem;
    margin-left: -1px;
}

.content-tools .tools-btn:hover,
.content-tools .tools-btn:focus,
.content-tools .tools-btn:active {
    background-color: #e6e6e6;
    border-color: #adadad;
    color: #000;
    outline: 0;
    transition: 0s;
}

.theseed-dark-mode .content-tools .tools-btn:active,
.theseed-dark-mode .content-tools .tools-btn:focus,
.theseed-dark-mode .content-tools .tools-btn:hover {
	background-color: #383b40;
    color: white;
}

.content-tools .tools-btn.btn-danger,
.content-tools .tools-btn.btn-danger:hover,
.content-tools .tools-btn.btn-danger:focus,
.content-tools .tools-btn.btn-danger:active {
    background-color: #d9534f;
    border-color: #adadad;
    color: white;
}

.theseed-dark-mode .content-tools .btn.btn-danger.tools-btn,
.theseed-dark-mode .content-tools .btn.btn-danger.tools-btn:hover,
.theseed-dark-mode .content-tools .btn.btn-danger.tools-btn:active,
.theseed-dark-mode .content-tools .btn.btn-danger.tools-btn:focus {
	background-color: #d9534f;
	color: #ddd;
}

.content-tools .tools-btn.btn-discuss-progress {
    background-color: #bbeabb;
}

.theseed-dark-mode .content-tools .tools-btn.btn-discuss-progress {
    background-color: #325a56;
}

.content-tools .tools-btn.btn-discuss-progress:hover,
.content-tools .tools-btn.btn-discuss-progress:focus,
.content-tools .tools-btn.btn-discuss-progress:active {
    background-color: #c5f4c5;
}

.theseed-dark-mode .content-tools .tools-btn.btn-discuss-progress:hover,
.theseed-dark-mode .content-tools .tools-btn.btn-discuss-progress:active,
.theseed-dark-mode .content-tools .tools-btn.btn-discuss-progress:focus {
    background-color: #438a83;
}

.content-tools .tools-btn.btn-info {
    color: #373a3c;
    background-color: #c7eef9;
    border-color: #78d4ef;
}

.theseed-dark-mode .content-tools .tools-btn.btn-info {
    background-color: #334351;
}

.content-tools .tools-btn.btn-info:hover,
.content-tools .tools-btn.btn-info:focus,
.content-tools .tools-btn.btn-info:active {
    background-color: #a8ebff;
    border-color: #51c8eb;
}

.theseed-dark-mode .content-tools .tools-btn.btn-info:hover,
.theseed-dark-mode .content-tools .tools-btn.btn-info:focus,
.theseed-dark-mode .content-tools .tools-btn.btn-info:active {
    background-color: #2a343d;
}

.content-tools .dropdown-menu {
    top: auto;
}

.dropdown-item.admin {
    background-color: #c94545;
    color: white;
    border-top: 1px white solid;
}

.dropdown-item.admin:hover {
    background-color: #ab0000;
}

.theseed-dark-mode .dropdown-item.admin {
    background-color: #711;
    color: white;
    border-top: 1px var(--liberty-brand-dark-color, #16171a) solid;
}

.theseed-dark-mode .dropdown-item.admin:hover {
    background-color: #970000;
}
</style>

<script>
import { vTooltip } from 'floating-vue';
import Common from '~/mixins/common';
import dropdown from '../components/dropdown';

export default {
    directives: { tooltip: vTooltip },
    mixins: [Common],
    components: {
        dropdown
    },
    data() {
        return {
            main: [],
            menu: []
        }
    },
    computed: {
        data() {
            return this.$store.state.page.data;
        },
        rev() {
            return this.data.rev || this.$route.query.rev;
        }
    },
    methods: {
        calculate() {
            this.main = [];
            this.menu = [];
            switch (this.$store.state.page.viewName) {
                case 'wiki':
                    if (this.data.date === null) {
                        this.main.push({
                            to: this.doc_action_link(this.data.document, 'backlink'),
                            title: "역링크"
                        });
                        this.main.push({
                            to: this.doc_action_link(this.data.document, 'acl') + '#namespace.read',
                            title: "ACL"
                        });
                        break;
                    }
                    else if (!this.rev) {
                        if (this.data.starred) this.main.push({
                            to: this.doc_action_link(this.data.document, 'member/unstar'),
                            tooltip: "Unstar",
                            html: `<span class="fa fa-star"></span> <span class="star-count">${this.data.star_count}</span>`
                        });
                        else if (this.data.star_count >= 0) this.main.push({
                            to: this.doc_action_link(this.data.document, 'member/star'),
                            tooltip: "Star",
                            html: `<span class="fa fa-star-o"></span> <span class="star-count">${this.data.star_count}</span>`
                        });
                        this.main.push({
                            to: this.doc_action_link(this.data.document, 'backlink'),
                            title: "역링크"
                        });
                        this.main.push({
                            to: this.doc_action_link(this.data.document, 'discuss'),
                            class: this.data.discuss_progress ? 'btn-discuss-progress' : null,
                            title: "토론"
                        });
                        if (this.data.editable === true && this.data.edit_acl_message) this.main.push({
                            onclick: () => this.$emit('onClickEditBtn'),
                            html: `<span class="fa fa-pencil-square"></span> 편집 요청`
                        });
                        else if (this.data.editable === false && this.data.edit_acl_message) this.main.push({
                            onclick: () => this.$emit('onClickEditBtn'),
                            html: `<span class="fa fa-lock"></span> 편집`
                        });
                        else this.main.push({
                            to: this.doc_action_link(this.data.document, 'edit'),
                            html: `<span class="fa fa-edit"></span> 편집`
                        });
                        this.main.push({
                            to: this.doc_action_link(this.data.document, 'history', this.rev ? { from: this.rev } : undefined),
                            title: "역사"
                        });
                        this.main.push({
                            to: this.doc_action_link(this.data.document, 'acl'),
                            title: "ACL"
                        });
                        if (this.data.user) {
                            this.menu.push({
                                to: this.contribution_link(this.data.user.uuid),
                                title: "기여 목록"
                            });
                            if (this.$store.state.session.quick_block && this.$store.state.localConfig['liberty.admin_convenience'] !== false) {
                                this.menu.push({
                                    class: 'admin',
                                    onclick: () => this.openQuickACLGroup({
                                        username: this.data.document.title,
                                        note: `${this.doc_fulltitle(this.data.document)} 긴급차단`
                                    }),
                                    title: "사용자 차단"
                                });
                                this.menu.push({
                                    class: 'admin',
                                    to: `/BlockHistory?query=${this.data.user.uuid}&target=text`,
                                    title: "차단 내역"
                                });
                                this.menu.push({
                                    class: 'admin',
                                    onclick: () => this.copyUuid(this.data.user.uuid),
                                    title: "UUID 복사"
                                });
                            }
                        }
                        break;
                    }
                case 'raw':
                case 'blame':
                case 'revert':
                case 'diff':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'history', this.rev ? { from: this.rev } : undefined),
                        class: 'btn-info',
                        title: "역사"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'w', this.rev ? { rev: this.rev } : undefined),
                        class: this.$store.state.page.viewName === 'wiki' ? 'disabled' : null,
                        title: "보기"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'raw', this.rev ? { rev: this.rev } : undefined),
                        class: this.$store.state.page.viewName === 'raw' ? 'disabled' : null,
                        title: "RAW"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'blame', this.rev ? { rev: this.rev } : undefined),
                        class: this.$store.state.page.viewName === 'blame' ? 'disabled' : null,
                        title: "blame"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'revert', this.rev ? { rev: this.rev } : undefined),
                        class: this.$store.state.page.viewName === 'revert' ? 'disabled' : null,
                        title: "되돌리기"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'diff', this.rev ? { rev: this.rev, oldrev: this.rev - 1 } : undefined),
                        class: this.$store.state.page.viewName === 'diff' ? 'disabled' : null,
                        title: "비교"
                    });
                    break;
                case 'notfound':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'backlink'),
                        title: "역링크"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'discuss'),
                        class: this.data.discuss_progress ? 'btn-discuss-progress' : null,
                        title: "토론"
                    });
                    if (this.data.edit_acl_message) this.main.push({
                        onclick: () => this.$emit('onClickEditBtn'),
                        html: `<span class="fa fa-lock"></span> 편집`
                    });
                    else this.main.push({
                        to: this.doc_action_link(this.data.document, 'edit'),
                        html: `<span class="fa fa-edit"></span> 편집`
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'history', this.rev ? { from: this.rev } : undefined),
                        title: "역사"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl'),
                        title: "ACL"
                    });
                    break;
                case 'edit':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'move'),
                        title: "이동"
                    });
                    this.main.push({
                        class: "btn-danger",
                        to: this.doc_action_link(this.data.document, 'delete'),
                        title: "삭제"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'history'),
                        title: "역사"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl') + '#document.edit',
                        title: "ACL"
                    });
                    break;
                case 'edit_edit_request':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'history'),
                        title: "역사"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl') + '#document.edit_request',
                        title: "ACL"
                    });
                    break;
                case 'history':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'discuss'),
                        title: "토론"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'edit'),
                        title: "편집"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl'),
                        title: "ACL"
                    });
                    break;
                case 'thread_list':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'edit'),
                        title: "편집"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl') + '#document.create_thread',
                        title: "ACL"
                    });
                    break;
                case 'thread_list_close':
                case 'edit_request_close':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'discuss'),
                        title: "토론"
                    });
                    break;
                case 'thread':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'discuss'),
                        title: "토론"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl') + '#document.write_thread_comment',
                        title: "ACL"
                    });
                    break;
                case 'edit_request':
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'discuss'),
                        title: "토론"
                    });
                    this.main.push({
                        to: this.doc_action_link(this.data.document, 'acl') + '#document.edit',
                        title: "ACL"
                    });
                    break;
                case 'contribution':
                case 'contribution_discuss':
                    this.main.push({
                        to: this.data.account.type === 1 ? this.doc_action_link(this.user_doc(this.data.account.name), 'w') : '',
                        class: this.data.account.type === 1 ? '' : 'disabled',
                        title: "사용자 문서"
                    });
                    if (this.$store.state.session.quick_block && this.$store.state.localConfig['liberty.admin_convenience'] !== false) {
                        if (this.data.account.type !== -1) this.menu.push({
                            class: 'admin',
                            onclick: () => this.openQuickACLGroup({
                                username: this.data.account.type === 1 ? "".concat(this.data.account.name) : undefined,
                                ip: this.data.account.type === 0 ? this.data.account.name + '/' + (this.data.account.name.indexOf('.') === -1 ? "128" : '32') : undefined,
                                note: '기여 목록 긴급차단'
                            }),
                            title: "사용자 차단"
                        });
                        if (this.data.account.type === -1 && this.data.account.uuid) this.menu.push({
                            to: this.doc_action_link(this.user_doc('*' + this.data.account.uuid), 'w'),
                            class: 'admin',
                            title: "삭제된 사용자 문서"
                        });
                        this.menu.push({
                            class: 'admin',
                            to: `/BlockHistory?query=${this.data.account.uuid}&target=text`,
                            title: "차단 내역"
                        });
                        this.menu.push({
                            class: 'admin',
                            onclick: () => this.copyUuid(this.data.account.uuid),
                            title: "UUID 복사"
                        });
                    }
                    break;
                case '':
                    if (this.$store.state.page.title === '오류') this.main.push({
                        onclick: () => this.$router.back(),
                        title: "이전 화면"
                    });
                    break;
            }
            if (this.data.menus) this.menu = this.menu.contact(this.data.menus);
        },
        async copyUuid(uuid) {
            try {
                await navigator.clipboard.writeText(uuid);
            } catch {}
        }
    },
    mounted() {
        this.calculate();
    },
    watch: {
        $route() {
            this.$nextTick(() => {
                this.calculate();
            });
        },
        '$store.state.localConfig'() {
            this.$nextTick(() => {
                this.calculate();
            });
        }
    }
}
</script>