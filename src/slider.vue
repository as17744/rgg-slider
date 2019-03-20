<template>
    <div class="slider-full-frame" @touchstart="touchimg" @touchmove="moveImg" @touchend="leaveImg">
        <div 
            class="slider-container" 
            ref="container" 
            :style="containerStyle">
            <div v-for="(item, index) in list" :key="index">
                <slot :name="`item${index}`"></slot>
            </div>
        </div>
    </div>
</template>

<script>
let $container;
const distance = window.innerWidth;
export default {
    props: {
        position: {
            type: Number,
            default: 0,
        },
        play: {
            type: Boolean,
            default: false,
        },
        duration: {
            type: Number,
            default: 3000,
        },
        vertical: {
            type: Boolean,
            default: true,
        },
        list: Array,
        containerStyle: Object,
    },
    data() {
        return {
            curIndex: 0, // 当前在轮播图的第几张
            direction: 0, // 0表示未确定手势移动方向，1表示左右，2表示上下
            posX: 0, // 轮播图滚动的距离
            startX: 0, // 手指刚接触图片的横向位子
            startY: 0, // 手指刚接触图片的纵向位子
            x: 0, // 每次移动记录的手指位子
        };
    },
    methods: {
        slideTo(pos, target) {
            const style = $container.style;
            this.posX = -pos * distance;
            style.webkitTransitionDuration = '300ms';
            style.MozTransitionDuration = '300ms';
            style.msTransitionDuration = '300ms';
            style.OTransitionDuration = '300ms';
            style.transitionDuration = '300ms';
            style.transform = `translateX(${this.posX}px)`;
            if (target !== 0) {
                this.$emit('slide', {
                    target,
                });
            }
        },
        touchimg(e) {
            this.direction = 0;
            const et = e.touches[0];
            this.startX = et.pageX;
            this.startY = et.pageY;
            this.x = et.pageX;
        },
        moveImg(e) {
            const et = e.touches[0];
            const disX = et.pageX - this.startX;
            const disY = et.pageY - this.startY;
            if (this.direction === 0) {
                this.direction = Math.abs(disX) < Math.abs(disY) ? 2 : 1;
            }
            if (!this.vertical || this.direction === 1) {
                e.preventDefault();
                const realDis = et.pageX - this.x; // 真实移动的距离
                this.invalid = (realDis > 0 && this.curIndex <= 0) || (this.curIndex + 1 >= this.list.length && realDis < 0); // eslint-disable-line
                this.x = et.pageX;
                this.posX = this.posX + realDis;
                if (!this.invalid) {
                    $container.style.transform = `translateX(${this.posX}px)`;
                }
            }
        },
        leaveImg(e) {
            const moveDis = this.x - this.startX;
            let targetIndex;
            if (moveDis < -10 && this.curIndex + 1 < this.list.length) {
                targetIndex = 1;
            } else if (moveDis > 10 && this.curIndex > 0) {
                targetIndex = -1;
            } else {
                targetIndex = 0;
            }
            this.curIndex = this.curIndex + targetIndex;
            this.slideTo(this.curIndex, targetIndex);
        },
        autoPlay() {
            let autoDirection = 1;
            setInterval(() => {
                if (this.curIndex === this.list.length) {
                    autoDirection = -1;
                } else if (this.curIndex === 0) {
                    autoDirection = 1;
                }
                this.curIndex = this.curIndex + autoDirection;
                this.slideTo(this.curIndex, autoDirection);
            }, this.duration);
        },
    },
    mounted() {
        this.curIndex = this.position;
        $container = this.$refs.container;
        this.posX = -this.curIndex * distance;
        $container.style.transform = `translateX(${this.posX}px)`;
        if (this.play) {
            this.autoPlay();
        }
    },
};
</script>

<style>
    .slider-full-frame {
        width: 100%;
        overflow-x: hidden;
    }
    .slider-container {
        display: flex;
        flex-wrap: nowrap;
    }
</style>
