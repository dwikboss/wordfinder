<template>
    <div>
        <div class="grid" :style="gridStyle">
            <CrosswordCell v-for="(cell, index) in cells" :key="index" :letter="cell.letter" :row="cell.row"
                :col="cell.col" :selected="isSelected(cell.row, cell.col)" @cell-clicked="handleCellClick" />
        </div>
    </div>
</template>

<script>
import CrosswordCell from './CrosswordCell.vue';

export default {
    components: {
        CrosswordCell
    },
    props: ['puzzle'],
    data() {
        return {
            cells: [],
            selectedCells: []
        };
    },
    watch: {
        puzzle: {
            handler() {
                this.initializeGrid();
            },
            deep: true,
            immediate: true
        }
    },
    methods: {
        initializeGrid() {
            const size = this.puzzle.size;
            const grid = Array(size).fill(null).map((_, rowIndex) =>
                Array(size).fill(null).map((_, colIndex) => ({ letter: '', row: rowIndex, col: colIndex }))
            );

            this.puzzle.words.forEach(wordObj => {
                wordObj.positions.forEach(pos => {
                    if (pos.row >= 0 && pos.row < size && pos.col >= 0 && pos.col < size) {
                        grid[pos.row][pos.col] = { letter: pos.letter, row: pos.row, col: pos.col };
                    } else {
                        console.warn(`Position out of bounds: row ${pos.row}, col ${pos.col}`);
                    }
                });
            });

            // Fill empty cells with random letters
            for (let row = 0; row < size; row++) {
                for (let col = 0; col < size; col++) {
                    if (grid[row][col].letter === '') {
                        grid[row][col].letter = this.getRandomLetter();
                    }
                }
            }

            this.cells = grid.flat();
        },
        getRandomLetter() {
            const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
            return letters[Math.floor(Math.random() * letters.length)];
        },
        handleCellClick(cell) {
            const cellIndex = this.selectedCells.findIndex(
                selected => selected.row === cell.row && selected.col === cell.col
            );

            if (cellIndex > -1) {
                this.selectedCells.splice(cellIndex, 1); // Deselect the cell if it's already selected
            } else {
                this.selectedCells.push(cell);
            }
            console.log(this.selectedCells);
            this.checkWord();
        },
        isSelected(row, col) {
            return this.selectedCells.some(cell => cell.row === row && cell.col === col);
        },
        checkWord() {
            const selectedLetters = this.selectedCells.map(cell => cell.letter);
            const selectedSet = new Set(selectedLetters);
            const foundWordObj = this.puzzle.words.find(wordObj => {
                const wordSet = new Set(wordObj.word);
                return selectedSet.size === wordSet.size && [...selectedSet].every(letter => wordSet.has(letter));
            });

            if (foundWordObj) {
                this.$emit('word-found', foundWordObj.word);
                this.selectedCells = [];
            }
        }
    },
    computed: {
        gridStyle() {
            return {
                display: 'grid',
                gridTemplateColumns: `repeat(${this.puzzle.size}, 40px)`,
                gridTemplateRows: `repeat(${this.puzzle.size}, 40px)`
            };
        }
    }
};
</script>

<style scoped>
.grid {
    display: grid;
    gap: 2px;
}
</style>