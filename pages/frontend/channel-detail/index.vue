<template>
<div v-if="$fetchState.pending">
    <div class="loadingio-spinner-dual-ring-v9pna4viv1d page-detail">
        <div class="ldio-1b03m6p5bfr">
            <div></div>
            <div>
                <div></div>
            </div>
        </div>
    </div>
</div>
<p v-else-if="$fetchState.error">Error while fetching channels</p>
<div v-else>
    <div class="video-js-responsive-container vjs-hd">
        <vplayer ref="videoPlayer" class="vjs-default-skin vjs-big-play-centered" :options="playerOptions" @play="onPlayerPlay($event)" @ready="onPlayerReady($event)" />
    </div>
    <InfoVideo v-if="infoData" :data="infoData" />
</div>
</template>

<script>
import Vue from 'vue'
import vplayer from '../../../components/core/videoplayer/v-player.vue'
import {
    core
} from '../../../assets/app/app'
import InfoVideo from '../../../components/frontend/channel-detail/infovideo.vue'

export default {
    layout: 'FrontendLayout',
    name: 'TvshowDetail',
    components: {
        vplayer,
        InfoVideo
    },
    data() {
        return {
            playSrc: '',
            infoData: {
                title: "",
                duration: "",
                date: "",
                desc: "",
            },
            playerOptions: {
                autoplay: false,
                controls: true,
                controlBar: {
                    timeDivider: true,
                    durationDisplay: true
                }
            },
        }
    },
    computed: {
        player() {
            return this.$refs.videoPlayer.player
        }
    },
    async fetch() {
        let query = this.$route.query;
        let data = {
            id: query.id
        };
        let item = await this.$store.dispatch("loadChannelItem", data);

        this.playSrc = await this.$store.dispatch("loadChannelSrc", data);

        this.infoData.poster = item.logo;
        this.infoData.title = item.name;
        this.infoData.duration = item.id;
        this.infoData.date = "";
    },
    mounted() {
        core.index();
    },
    methods: {
        onPlayerPlay(player) {
            console.log('player play!', player)
        },
        onPlayerReady(player) {
            console.log('player ready!', player);

            const video = {
                withCredentials: false,
                type: Vue.prototype.getVideoType(this.playSrc),
                src: this.playSrc
            };
            this.player.src(video);
            this.player.play();
        },
    },
    fetchOnServer: false
}
</script>
