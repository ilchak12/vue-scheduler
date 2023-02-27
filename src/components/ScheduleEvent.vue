<template>
    <div
        ref="eventBlock"
        class="schedule__event"
        :style="{
            width: width + 'px',
            left: leftPosition + 'px',
            top: topPosition + 'px',
            transition: isDragging || isResizing ? 'none' : '0.3s linear all',
            cursor: isCursor
                ? 'e-resize' : isDragging
                ? 'grab' : 'initial'
        }"
        @mousedown="start"
    >
        <div>
            Hello
        </div>
    </div>
</template>

<script>
export default {
    name: "ScheduleEvent",
    props: ['parentWidth'],
    data() {
        return {
            isResizing: false,
            startX: 0,
            startY: 0,
            startWidth: 0,
            startHeight: 0,
            minWidth: 50,
            width: 100,
            resizeDirection: '',
            leftStart: 0,
            isCursor: false,

            leftPosition: 0,
            topPosition: 0,

            isDragging: false,
            mouseOffsetX: 0,
            mouseOffsetY: 0,
        }
    },
    methods: {
        start(event) {
            const { left, right } = this.$el.getBoundingClientRect();

            if (event.clientX <= left + 15) {
                this.isResizing = true;
                this.startX = event.clientX;
                this.startY = event.clientY;
                this.startWidth = this.width;
                this.leftStart = this.leftPosition;
                this.resizeDirection = 'left';
            } else if (event.clientX >= right - 15) {
                this.isResizing = true;
                this.startX = event.clientX;
                this.startY = event.clientY;
                this.startWidth = this.width;
                this.resizeDirection = 'right';
            } else {
                this.isDragging = true;
                this.mouseOffsetX = event.clientX - this.leftPosition;
                this.mouseOffsetY = event.clientY - this.topPosition;
            }
        },
        action(event) {
            const { left, right } = this.$el.getBoundingClientRect();
            this.isCursor = event.clientX >= right - 15 || event.clientX <= left + 15;

            console.log(this.isCursor)

            if (this.isResizing) {
                if (!this.isResizing) return;
                const deltaX = event.clientX - this.startX;

                if (this.resizeDirection === 'left') {
                    this.width = Math.max(0, this.startWidth - deltaX);
                    if (this.width < 50) {
                        this.width = 50;
                        return;
                    }
                    this.leftPosition = Math.max(0, Math.min(this.leftStart + deltaX, this.leftPosition + this.width));
                } else if (this.resizeDirection === 'right') {
                    this.width = Math.max(0, this.startWidth + deltaX);
                }
            } else if (this.isDragging) {
                this.topPosition = event.clientY - this.mouseOffsetY;
                this.leftPosition = event.clientX - this.mouseOffsetX;
            }
        },
        stop() {
            if (this.isResizing) {
                this.isResizing = false;

                if (this.resizeDirection === "left") {
                    this.leftPosition = Math.round(this.leftPosition / 50) * 50;
                    this.width = Math.round(this.width / 50) * 50;
                } else if (this.resizeDirection === "right") {
                    this.width = Math.round(this.width / 50) * 50;
                }

                if (this.leftPosition + this.width > this.parentWidth) {
                    this.width = this.parentWidth - this.leftPosition;
                }
            } else if (this.isDragging) {
                this.isDragging = false;
                this.topPosition = Math.round((event.clientY - this.mouseOffsetY) / 50) * 50;
                this.leftPosition = Math.round((event.clientX - this.mouseOffsetX) / 50) * 50;
            }
        }
    },
    mounted() {
        document.addEventListener('mousemove', this.action);
        document.addEventListener('mouseup', this.stop);
    }
}
</script>

<style lang="scss" scoped>
.schedule {
    &__event {
        position: absolute;
        z-index: 10;
        left: 0;
        top: 0;
        bottom: 0;
        background-color: blue;
        border-radius: 4px;
        height: 100%;
        width: 100px;
        min-width: 50px;
        overflow: auto;
        user-select: none;

        div {
            height: 100%;
        }
    }
}
</style>