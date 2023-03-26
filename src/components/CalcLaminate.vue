<template>
    <div>
        <form>
            <h3>Помещение</h3>
            <v-row>
                <v-col cols="12" sm="6">
                    <p>Длина (см)</p>
                    <v-text-field
                        v-model="room_length"
                    />
                </v-col>
        
                <v-col cols="12" sm="6">
                    <p>Ширина (см)</p>
                    <v-text-field
                        v-model="room_width"
                    />
                </v-col>
            </v-row>

            <h3>Ламинат</h3>
            <v-row>
                <v-col cols="12" sm="6">
                    Длина плашки (мм)
                    <v-text-field
                        v-model="board_length"
                    />
                </v-col>

                <v-col cols="12" sm="6">
                    Ширина плашки (мм)
                    <v-text-field
                        v-model="board_width"
                    />
                </v-col>

                <v-col cols="12" sm="6">
                    Количество плашек в упаковке
                    <v-text-field
                        v-model="packing_boards_count"
                    />
                </v-col>

                <v-col cols="12" sm="6">
                    Цена за одну упаковку
                    <v-text-field
                        v-model="packing_price"
                    />
                </v-col>
            </v-row>

            <h3>Укладка</h3>

            <v-row>
                <v-col cols="12">
                    Способ укладки

                    <v-radio-group
                        v-model="technique"
                        column
                    >
                        <v-radio
                            v-for="(item, index) in technique_options"
                            :key="index"
                            :label="item.label"
                            :value="item.value"
                        />
                    </v-radio-group>
                </v-col>
            </v-row>

            <div>
                <v-btn
                    color="success"
                    :disabled="!isPossibleCalc()"
                    @click="calcLaminate"
                >
                    Расчитать
                </v-btn>
            </div>

            <v-alert
                v-if="is_show_result"
                class="mt-3"
            >
                <h2>Результат: </h2>
                <p>Количество плашек: {{ board_count }}</p>
                <p>Количество упаковок: {{ packing_count }}</p>
                <p>Требуемая площадь ламината: {{ general_board_space }} см<sup>2</sup></p>
                <p>Стоимость ламината: {{ price }} p.</p>
                <p>Остаток ламината: {{ rest }}</p>
            </v-alert>
        </form>
    </div>
</template>

<script>
export default {
    name: 'CalcLaminate',


    data: () => ({
        room_length: null, // длина помещения
        room_width: null, // ширина помещения
        board_length: null, // длина плашки
        board_width: null, // ширина плашки
        packing_boards_count: null, // количество плашек
        packing_price: null, // цена одной упаковки
        technique: null, // способ укладки

        technique_options: [
                { value: 'straight', label: 'по прямой (добавляем 5% запаса)' },
                { value: 'diagonal', label: 'по диагонали (добавляем 10% запаса)' }
            ],

        board_count: null, // Количество плашек
        packing_count: null, // Количество упаковок
        general_board_space: null, // Требуемая площадь ламината
        price: null, // Стоимость ламината
        rest: null, // Остаток ламинара

        is_show_result: false,
    }),

    watch: {
        room_length() {
            this.clearResult()
        },

        room_width () {
            this.clearResult()
        },

        board_length () {
            this.clearResult()
        },

        board_width () {
            this.clearResult()
        },

        packing_boards_count () {
            this.clearResult()
        },

        packing_price () {
            this.clearResult()
        },

        technique () {
            this.clearResult()
        }
    },

    methods: {
        // определяем заблокирована или нет кнопка для расчета результата
        isPossibleCalc () {
            return !!this.room_length && !!this.room_width && !!this.board_length && !!this.board_width &&
            !!this.packing_boards_count && !!this.technique
        },

        // рассчитываем результат
        calcLaminate () {
            // 1) общая площадь комнаты
            let room_space = this.room_length * this.room_width

            // 2) вычисляем % запаса
            let additional_percent = (this.technique === 'straight') ? 5 : 10

            // 3) вычисляем дополнительную площадь в соответствии с % запаса
            let additional_space = (room_space * additional_percent) / 100

            // 5) вычисляем итоговую необходимую площадь
            room_space = room_space + additional_space
            this.general_board_space = room_space

            // 6) считаем площадь одной плашки, переводим мм в см
            let board_space = (this.board_length / 10) * (this.board_width / 10)

            // 6) считаем сколько плашек необходимо 
            this.board_count = Math.ceil(room_space / board_space)

            // 7) считаем сколько упаковок необходимо
            this.packing_count = Math.ceil(this.board_count / this.packing_boards_count)

            // 8) считаем стоимость ламината
            this.price = (this.packing_count * this.packing_price).toFixed(2)

            // меняем условие, чтобы результат отображался пользователю
            this.is_show_result = true
        },

        // очищение данных и скрытие окна с результатами
        clearResult () {
            this.is_show_result = false

            this.board_count = null
            this.packing_count = null
            this.general_board_space = null
            this.price = null
            this.rest = null
        }
    }
}
</script>