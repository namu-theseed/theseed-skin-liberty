<template>
    <div :class="$style['live-recent-content']">
        <ul :class="$style['live-recent-list']" id="live-recent-list">
            <template v-if="recent.length === 0">
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
                <li><span :class="$style['recent-item']">&nbsp;</span></li>
            </template>
            <template v-else>
                <li v-for="(r,i) in recent" :key="i">
                    <nuxt-link :class="[$style['recent-item'], { [$style.removed]: r.status === 'delete' }]" :key="r.document" :to="doc_action_link(r.document, 'w')">[<local-date :date="r.date" :format="getDateType(r.date)" />] {{ r.document }}</nuxt-link>
                </li>
            </template>
        </ul>
    </div>
</template>

<style module scoped>
.live-recent-content {
    background-color: #fff;
    border: 1px solid #e1e8ed;
    border-top: 0;
}

.live-recent-content .live-recent-list {
    list-style: none;
    margin: 0;
    padding: 0;
}

.live-recent-content .live-recent-list li {
    border-bottom: 1px solid #e1e8ed;
    padding: 0.2rem 0.6rem;
}

.live-recent-content .live-recent-list li:last-child {
    border-bottom: none;
}

.live-recent-content .live-recent-list .recent-item {
    font-size: 0.80rem;
    color: #373a3c;
}

.live-recent-content .live-recent-list .recent-item .new {
    font-size: 0.80rem;
    color: #b73333;
}

.live-recent-content .live-recent-list .recent-item.removed {
    text-decoration: line-through;
}
</style>

<script>
import RecentCardMixin from '~/mixins/recentCard';
export default {
    mixins: [RecentCardMixin],
    methods: {
        getDateType(date) {
            const now = Math.floor((new Date()).getTime() / 1000);
            return (now - 86400) > date ? 'Y/m/d' : 'H:i:s';
        }
    }
}
</script>