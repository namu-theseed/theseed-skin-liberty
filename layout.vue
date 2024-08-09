<template>
    <div class="Liberty" :style="skinConfig">
        <div id="top"></div>
        <div class="nav-wrapper" :class="{ 'navbar-fixed-top': $store.state.localConfig['liberty.fixed_navbar'] === true }">
            <nav class="navbar navbar-dark">
                <nuxt-link class="navbar-brand" to="/">{{ $store.state.config['skin.liberty.navbar_logo_text'] }}</nuxt-link>
                <ul class="nav navbar-nav">
                    <li class="nav-item">
                        <nuxt-link class="nav-link" to="/RecentChanges"><span class="fa fa-refresh"></span><span class="hide-title">최근 변경</span></nuxt-link>
                    </li>
                    <li class="nav-item">
                        <nuxt-link class="nav-link" to="/RecentDiscuss"><span class="fa fa-comments"></span><span class="hide-title">최근 토론</span></nuxt-link>
                    </li>
                    <li class="nav-item">
                        <nuxt-link class="nav-link" to="/random"><span class="fa fa-random"></span><span class="hide-title">임의 문서</span></nuxt-link>
                    </li>
                    <li class="nav-item">
                        <dropdown>
                            <template #toggle>
                                <a class="nav-link dropdown-toggle dropdown-toggle-fix" href="#" @click.prevent>
                                    <span class="fa fa-gear"></span><span class="hide-title">도구</span>
                                </a>
                            </template>
                            <div class="dropdown-menu" role="menu">
                                <nuxt-link to="/NeededPages" class="dropdown-item">작성이 필요한 문서</nuxt-link>
                                <nuxt-link to="/OrphanedPages" class="dropdown-item">고립된 문서</nuxt-link>
                                <nuxt-link to="/UncategorizedPages" class="dropdown-item">분류가 되지 않은 문서</nuxt-link>
                                <nuxt-link to="/OldPages" class="dropdown-item">편집된 지 오래된 문서</nuxt-link>
                                <nuxt-link to="/ShortestPages" class="dropdown-item">내용이 짧은 문서</nuxt-link>
                                <nuxt-link to="/LongestPages" class="dropdown-item">내용이 긴 문서</nuxt-link>
                                <nuxt-link to="/BlockHistory" class="dropdown-item">차단 내역</nuxt-link>
                                <nuxt-link to="/RandomPage" class="dropdown-item">RandomPage</nuxt-link>
                                <nuxt-link to="/Upload" class="dropdown-item">파일 올리기</nuxt-link>
                                <nuxt-link to="/License" class="dropdown-item">라이선스</nuxt-link>
                                <template v-if="$store.state.session.menus.length">
                                    <div class="dropdown-divider"></div>
                                    <nuxt-link v-for="m in $store.state.session.menus" :key="m.l" :to="m.l" class="dropdown-item">{{ m.t }}</nuxt-link> 
                                </template>
                            </div>
                        </dropdown>
                    </li>
                </ul>
                <div class="navbar-login">
                    <dropdown class="login-menu">
                        <template #toggle>
                            <a id="login-menu" class="dropdown-toggle" type="button">
                                <img v-if="$store.state.session.gravatar_url" class="profile-img" :src="$store.state.session.gravatar_url">
                                <span v-else class="fa fa-user"></span>
                            </a>
                        </template>
                        <div class="dropdown-menu dropdown-menu-right login-dropdown-menu">
                            <div v-if="$store.state.session.account.type === 1" class="username dropdown-item">
                                <b>{{ $store.state.session.account.name }}</b><br>Member
                            </div>
                            <div v-else-if="$store.state.session.account.type === 0" class="username dropdown-item">
                                <b>{{ $store.state.session.account.name }}</b><br>Please login!
                            </div>
                            <div class="dropdown-divider"></div>
                            <a href="#" class="dropdown-item" @click.prevent="openSettingModal">설정</a>
                            <a v-if="$store.state.currentTheme === 'light'" href="#" class="dropdown-item" @click.prevent="$store.commit('localConfigSetValue', {key: 'wiki.theme', value: 'dark'})">다크 테마로</a>
                            <a v-if="$store.state.currentTheme === 'dark'" href="#" class="dropdown-item" @click.prevent="$store.commit('localConfigSetValue', {key: 'wiki.theme', value: 'light'})">라이트 테마로</a>
                            <div class="dropdown-divider"></div>
                            <template v-if="$store.state.session.account.type === 1">
                                <nuxt-link to="/member/mypage" class="dropdown-item">내 정보</nuxt-link>
                                <nuxt-link :to="doc_action_link(user_doc($store.state.session.account.name), 'w')" class="dropdown-item">내 사용자 문서</nuxt-link>
                                <nuxt-link to="/member/starred_documents" class="dropdown-item">내 문서함</nuxt-link>
                                <div class="dropdown-divider"></div>
                            </template>
                            <template v-if="$store.state.session.account.uuid">
                                <nuxt-link class="dropdown-item" :to="contribution_link($store.state.session.account.uuid)">내 문서 기여 목록</nuxt-link>
                                <nuxt-link class="dropdown-item" :to="contribution_link_discuss($store.state.session.account.uuid)">내 토론 기여 목록</nuxt-link>
                                <div class="dropdown-divider"></div>
                            </template>
                            <nuxt-link v-if="$store.state.session.account.type === 1" :to="{path:'/member/logout',query:{redirect:$route.fullPath}}" class="dropdown-item">로그아웃</nuxt-link>
                            <nuxt-link v-else :to="{path:'/member/login',query:{redirect:$route.fullPath}}" class="dropdown-item">로그인</nuxt-link>
                        </div>
                    </dropdown>
                </div>
                <search-form />
            </nav>
        </div>
        <div class="content-wrapper" :class="{ 'hide-sidebar': $store.state.localConfig['liberty.sidebar'] === 'hide' }">
            <div class="liberty-sidebar">
                <div class="liberty-right-fixed" :class="{ 'fixed': $store.state.localConfig['liberty.sidebar'] === 'fix' }">
                    <div class="live-recent">
                        <div class="live-recent-header">
                            <ul class="nav nav-tabs">
                                <li class="nav-item">
                                    <a id="liberty-recent-tab1" class="nav-link active">최근 변경</a>
                                </li>
                            </ul>
                        </div>
                        <recent-card />
                        <div class="live-recent-footer">
                            <nuxt-link to="/RecentChanges" title="최근 변경내역"><span class="label label-info">더 보기</span></nuxt-link>
                        </div>
                    </div>
                </div>
            </div>
            <div class="container-fluid liberty-content">
                <div v-if="$store.state.config['wiki.sitenotice']" id="site-notice" class="notification">
                    <span class="label label-danger" v-html="$store.state.config['wiki.sitenotice']" />
                </div>
                <div class="liberty-content-header">
                    <content-tool @onClickEditBtn="showEditMessage" />
                    <div class="title">
                        <h1 v-if="$store.state.page.data.document && $store.state.page.viewName !== 'error'">
                            <nuxt-link :to="doc_action_link($store.state.page.data.document, 'w')"><span v-if="$store.state.page.data.document.forceShowNamespace !== false" class="namespace">{{$store.state.page.data.document.namespace}}:</span>{{$store.state.page.data.document.title}}</nuxt-link>
                            <small v-if="$store.state.page.viewName === 'edit_edit_request' || $store.state.page.viewName === 'edit_request'">(편집 요청)</small>
                            <small v-else-if="$store.state.page.viewName === 'edit' && $store.state.page.data.body.section">(r{{$store.state.page.data.body.baserev}} 문단 편집)</small>
                            <small v-else-if="$store.state.page.viewName === 'edit' && $store.state.page.data.body.baserev === '0'">(새 문서 생성)</small>
                            <small v-else-if="$store.state.page.viewName === 'edit'">(r{{$store.state.page.data.body.baserev}} 편집)</small>
                            <small v-else-if="$store.state.page.viewName === 'history'">(문서 역사)</small>
                            <small v-else-if="$store.state.page.viewName === 'backlink'">(역링크)</small>
                            <small v-else-if="$store.state.page.viewName === 'move'">(이동)</small>
                            <small v-else-if="$store.state.page.viewName === 'delete'">(삭제)</small>
                            <small v-else-if="$store.state.page.viewName === 'acl'">(ACL)</small>
                            <small v-else-if="$store.state.page.viewName === 'thread'">(토론)</small>
                            <small v-else-if="$store.state.page.viewName === 'thread_list'">(토론 목록)</small>
                            <small v-else-if="$store.state.page.viewName === 'thread_list_close'">(닫힌 토론)</small>
                            <small v-else-if="$store.state.page.viewName === 'edit_request_close'">(닫힌 편집 요청)</small>
                            <small v-else-if="$store.state.page.viewName === 'diff'">(비교)</small>
                            <small v-else-if="$store.state.page.viewName === 'revert' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}}로 되돌리기)</small>
                            <small v-else-if="$store.state.page.viewName === 'raw' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}} RAW)</small>
                            <small v-else-if="$store.state.page.viewName === 'blame' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}} Blame)</small>
                            <small v-else-if="$store.state.page.viewName === 'wiki' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}} 판)</small>
                        </h1>
                        <h1 v-else>{{ $store.state.page.title }}</h1>
                    </div>
                </div>
                <div class="liberty-content-main wiki-article">
                    <alert v-if="isShowACLMessage && $store.state.page.data.edit_acl_message" @close="isShowACLMessage = false" error closable>
                        <span v-html="$store.state.page.data.edit_acl_message" @click="onDynamicContentClick($event)"></span>
                        <span v-if="requestable"><br v-if="$store.state.page.data.edit_acl_message.includes('\n')">대신 <nuxt-link :to="doc_action_link($store.state.page.data.document, 'new_edit_request')">편집 요청</nuxt-link>을 생성할 수 있습니다.</span>
                    </alert>
                    <alert v-if="$store.state.session.user_document_discuss && $store.state.localConfig['wiki.hide_user_document_discuss'] !== $store.state.session.user_document_discuss" @close="$store.commit('localConfigSetValue', {key: 'wiki.hide_user_document_discuss', value: $store.state.session.user_document_discuss})" closable theme="primary">
                        현재 진행 중인 <nuxt-link :to="doc_action_link(user_doc($store.state.session.account.name), 'discuss')">사용자 토론</nuxt-link>이 있습니다.
                    </alert>
                    <alert v-if="$store.state.page.viewName === 'notfound' && $store.state.page.data.document.namespace === '문서'" style="line-height: 2.1rem;">
                        '{{ $store.state.page.title }}'을(를) 검색하시겠습니까?
                        <div class="float-right"><seed-link-button :to="'/Search?q='+ $store.state.page.title">검색</seed-link-button></div>
                        <div class="clearfix"></div>
                    </alert>
                    <rev-selector v-if="$store.state.page.viewName === 'diff' || $store.state.page.viewName === 'blame'" />
                    <from-selector v-if="$store.state.page.viewName === 'history'" />
                    <nuxt />
                    <div v-if="$store.state.page.viewName === 'license'">
                        <h2>Liberty skin license</h2>
                        <pre>{{ License }}</pre>
                    </div>
                </div>
                <div id="bottom" class="liberty-footer">
                    <ul v-if="$store.state.page.viewName === 'wiki' && $store.state.page.data.date" class="footer-info">
                        <li class="footer-info-lastmod">이 문서는 <local-date :date="$store.state.page.data.date" />에 마지막으로 편집되었습니다.</li>
                        <li class="footer-info-copyright" v-html="$store.state.config['wiki.copyright_text']" />
                    </ul>
                    <ul class="footer-places" @click="onDynamicContentClick($event)" v-html="$store.state.config['skin.liberty.footer_html']" />
                    <ul class="footer-icons">
                        <li class="footer-poweredbyico">
                            <a href="//gitlab.com/librewiki/Liberty-MW-Skin">Liberty</a> | <a href="//theseed.io/">the seed</a>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <div class="scroll-buttons">
            <nuxt-link class="scroll-toc" to="#toc"><i class="fa fa-list-alt" aria-hidden="true"></i></nuxt-link>
            <nuxt-link id="left" class="scroll-button" to="#top"><i class="fa fa-arrow-up" aria-hidden="true"></i></nuxt-link>
            <nuxt-link id="right" class="scroll-bottom" to="#bottom"><i class="fa fa-arrow-down" aria-hidden="true"></i></nuxt-link>
        </div>
    </div>
</template>

<style>
@import "./css/bootstrap.min.css";
@import "./css/font-awesome.min.css";
@import "./css/font/Noto Sans KR.css";
@import "./css/default.css";
@import './css/default_mobile.css';
@import "./css/dark.css";
</style>

<script>
import Common from '~/mixins/common';
import Alert from '~/components/alert';
import SeedLinkButton from '~/components/seedLinkButton';
import LocalDate from '~/components/localDate';
import RecentCard from './layouts/recentCard';
import SearchForm from './layouts/searchForm';
import ContentTool from './layouts/contentTool';
import Dropdown from './components/dropdown';
import RevSelector from './components/revSelector';
import FromSelector from './components/fromSelector';
import SettingModal from './components/settingModal';
import License from "raw-loader!./LICENSE";

export default {
    mixins: [Common],
    components: {
        Alert,
        SeedLinkButton,
        LocalDate,
        RecentCard,
        SearchForm,
        Dropdown,
        ContentTool,
        RevSelector,
        FromSelector
    },
    data() {
        return {
            License,
            isShowACLMessage: false
        };
    },
    watch: {
        $route() {
            this.isShowACLMessage = false;
        }
    },
    head() {
        return {
            meta: [{ name: 'theme-color', content: this.brand_color }]
        };
    },
    computed: {
        brand_color() {
            return this.selectByTheme(this.$store.state.config['skin.liberty.brand_color_1'] ?? '#4188f1', '#2d2f34');
        },
        skinConfig() {
            return {
                '--liberty-brand-color': this.brand_color,
                '--liberty-brand-dark-color': this.selectByTheme(this.$store.state.config['skin.liberty.brand_dark_color_1'] ?? this.darkenColor(this.brand_color), '#16171a'),
                '--liberty-brand-bright-color': this.selectByTheme(this.$store.state.config['skin.liberty.brand_bright_color_1'] ?? this.lightenColor(this.brand_color), '#383b40'),
                '--liberty-navbar-logo-image': this.$store.state.config['skin.liberty.navbar_logo_image'],
                '--liberty-navbar-logo-minimum-width': this.$store.state.config['skin.liberty.navbar_logo_minimum_width'],
                '--liberty-navbar-logo-width': this.$store.state.config['skin.liberty.navbar_logo_width'],
                '--liberty-navbar-logo-size': this.$store.state.config['skin.liberty.navbar_logo_size'],
                '--liberty-navbar-logo-padding': this.$store.state.config['skin.liberty.navbar_logo_padding'],
                '--liberty-navbar-logo-margin': this.$store.state.config['skin.liberty.navbar_logo_margin'],
                '--brand-color-1': 'var(--liberty-brand-color)',
                '--brand-color-2': this.selectByTheme(this.$store.state.config['skin.liberty.brand_color_2'] ?? 'var(--liberty-brand-color)', 'var(--liberty-brand-color)'),
                '--brand-bright-color-1': 'var(--liberty-brand-bright-color)',
                '--brand-bright-color-2': this.selectByTheme(this.$store.state.config['skin.liberty.brand_bright_color_2'] ?? 'var(--liberty-brand-bright-color)', 'var(--liberty-brand-bright-color)'),
                '--text-color': this.selectByTheme('#373a3c', '#ddd'),
                '--article-background-color': this.selectByTheme('#fff', '#000'),
            };
        },
        requestable() {
            return this.$store.state.page.data.editable === true && this.$store.state.page.data.edit_acl_message;
        }
    },
    methods: {
        showEditMessage() {
            if (this.isShowACLMessage) {
                this.$router.push(this.doc_action_link(this.$store.state.page.data.document, this.requestable ? 'new_edit_request' : 'edit'));
            }
            else {
                this.isShowACLMessage = true;
            }
        },
        darkenColor(hex, percent=50) {
            let r = parseInt(hex.substring(1, 3), 16);
            let g = parseInt(hex.substring(3, 5), 16);
            let b = parseInt(hex.substring(5, 7), 16);

            r = Math.round(r * (1 - percent / 100));
            g = Math.round(g * (1 - percent / 100));
            b = Math.round(b * (1 - percent / 100));

            return "#" + ((r < 16 ? "0" : "") + r.toString(16)) + ((g < 16 ? "0" : "") + g.toString(16)) + ((b < 16 ? "0" : "") + b.toString(16));
        },
        lightenColor(hex, percent=50) {
            let r = parseInt(hex.substring(1, 3), 16);
            let g = parseInt(hex.substring(3, 5), 16);
            let b = parseInt(hex.substring(5, 7), 16);

            r = Math.round(r + (255 - r) * (percent / 100));
            g = Math.round(g + (255 - g) * (percent / 100));
            b = Math.round(b + (255 - b) * (percent / 100));

            return "#" + ((r < 16 ? "0" : "") + r.toString(16)) + ((g < 16 ? "0" : "") + g.toString(16)) + ((b < 16 ? "0" : "") + b.toString(16));
        },
        selectByTheme(light, dark) {
            return this.$store.state.currentTheme === 'dark' ? dark : light;
        },
        openSettingModal() {
            this.$vfm.show({ component: SettingModal });
        }
    }
}
</script>