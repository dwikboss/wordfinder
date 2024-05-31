<template>
    <div class="cell" @click="selectCell" :class="{ selected: active }">
        <span>{{ letter }}</span>
    </div>
</template>

<script>
export default {
    data(){
        return {
            active: false
        }
    },
    props: ['letter', 'row', 'col'],
    methods: {
        selectCell() {
            this.active = !this.active;
            this.$emit('cell-clicked', { row: this.row, col: this.col, letter: this.letter });
        }
    }
};
</script>

<style scoped>
.cell {
    width: 40px;
    height: 40px;
    background: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 20px;
    cursor: pointer;
}

.cell.selected {
    background-color: black;
    color: white;
}
</style>