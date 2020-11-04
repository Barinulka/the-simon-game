<template>
    <div class="container">
		<div class="game-board">
            <h1>Simon Says Game</h1>
			<div class="simon">
				<div class="tile red" @click="clicked(1)" :class="{ active: lit1 }"></div>
				<div class="tile blue" @click="clicked(2)" :class="{ active: lit2 }"></div>
				<div class="tile yellow" @click="clicked(3)" :class="{ active: lit3 }"></div>
				<div class="tile green" @click="clicked(4)" :class="{ active: lit4 }"></div>
			</div>
		</div>
		<div class="game-info">
			<h2>Round: <span> {{ round }} </span></h2>
			<button @click="startGame"> Начать </button>
            <div class="game-options">
                <h3>Выберите уровень сложности</h3>   
                <input type="radio" id="easy" value="easy" v-model="level">
                <label for="easy" class="radio">Легкий</label> <br>
                <input type="radio" id="medium" value="medium" v-model="level">
                <label for="medium" class="radio">Средний</label> <br>
                <input type="radio" id="hard" value="hard" v-model="level">
                <label for="hard" class="radio">Трудный</label> <br>   
            </div>
				<!-- <p v-if="lose">В проиграли на <span> {{ round }} </span> раунде</p> -->
			<p> {{ lose }} </p>
		</div>
	</div>
</template>

<script>
export default {
    data() {
        return {
			lit1: false,
			lit2: false,
			lit3: false,
			lit4: false,
            steps: [], // Массив с последовательностью ходов
			round: 0,
			lose: '',
            index: 0, // Будет записывать длину массива steps для проверки 
            level: 'easy',

        }
    },
    methods: {
        startGame: function() {  // Функция обнуляет все значения и вызывает функцию запуска игры
			this.steps = [];
			this.lose = '';
			this.round = 0;
            this.index = 0;
            this.newRound(); // Запускаем игру
        },
        newRound: function() { // Запуск игры
            this.index = 0; // Обнуляем счетчик
            this.round++; // Записываем текущий раунд
            this.steps.push(this.getRandomNumber()); // Пушим в массив случайное число для последовательности
            this.showSteps(); // Запускаем показ последовательности
        },
        showSteps: function(){
            var self = this; // self нужна для вложенной функции, без не работает
            var i = 0;
            if(this.level == 'easy'){
                setInterval(function(){  // Вызываем функцию регулярно через заданный интервал
                if(i >= self.steps.length){ // Она считает количество повторений
                    self.stopTimer();	// в зависимости от длины массива и 
                }							// записей в нем
				self.boardEffect(self.steps[i]);
				i++;
			}, 1200); 
            }
            else if(this.level == 'medium') {
                setInterval(function(){  
                if(i >= self.steps.length){
                    self.stopTimer();	
                }							
				self.boardEffect(self.steps[i]);
				i++;
			}, 800); 
            }
            else if(this.level == 'hard') {
            setInterval(function(){  
            if(i >= self.steps.length){
                self.stopTimer();	
            }							
			self.boardEffect(self.steps[i]);
			i++;
			}, 400); 
            }
        },
        stopTimer: function() {
            clearInterval(setInterval);
        },
        getRandomNumber: function() { // Получаем случайное число от 1 до 4
            return Math.floor((Math.random() * 4) + 1);
        },
        clicked: function(box) { // Проверяем нажатое поле
            var self = this;
            this.boardEffect(box); 
            var isCorrect = this.checkSteps(box); // Отслеживаем нажатое поле
            if(!isCorrect){ // Если повторили не правильно вызвем endGame
                self.endGame();
            }
            else{ 											
                if(this.index === this.steps.length - 1) { // Проверяем правильно ли повторили последовательность
                    self.newRound(); // Если все повторили правильно, запускаем следующий раунд
                }
                else {
                    this.index++;
                }
            }
        },
        endGame: function() {  		// Выводим сообщение о проигрыше и
			// this.lose = true;	// сбрасываем параметр игры
			this.lose = 'Вы проиграли на ' + this.round + ' раунде'
            this.resetGame();
        },
        checkSteps: function(box) {
            return (this.steps[this.index] === box);
        },
        resetGame: function() {
            this.round = 0;
			this.steps = [];
            this.index = 0;
            this.userTurn = false;
		},
		boardEffect: function(box) { // Принимаем номер нажатого поля
            var self = this;
            switch (box) {
                case 1:
                    this.lit1 = true;  // Делаем класс lit активным, для подсвечивания
                    var audio1 = new Audio(require('./../assets/sounds/1.ogg'));
                    audio1.play();  // Проигрываем аудио файл 
                    setTimeout(function(){
                        self.lit1 = false; // Делаем класс lit неактивным
                    }, 100);
                break;
                case 2:
                    this.lit2 = true;
                    var audio2 = new Audio(require('./../assets/sounds/2.ogg'));
                    audio2.play();
                    setTimeout(function(){
                        self.lit2 = false;
                    }, 100);
                break;
                case 3:
                    this.lit3 = true;
                    var audio3 = new Audio(require('./../assets/sounds/3.ogg'));
                    audio3.play();
                    setTimeout(function(){
                        self.lit3 = false;
                    }, 100);
                break;
                case 4:
                    this.lit4 = true;
                    var audio4 = new Audio(require('./../assets/sounds/4.ogg'));
                    audio4.play();
                    setTimeout(function(){
                        self.lit4 = false;
                    }, 100);
                break;
            }
            return
        }
    }
}
</script>

<style>
body {
	font-family: Arial, serif;
	color: #333;
	
}

h1 {
	margin: 1em 0 2em;
	text-align: center;
}

.active {
	opacity: 1 !important;
}

.container {
	width: 540px;
	margin: 0 auto;
}

.simon {
	background: #fff;
	position: relative;
	float: left;
	margin-right: 3em;	
	width: 302px;
	height: 295px;
	border-radius: 150px 150px 150px 150px;
    box-shadow: 2px 1px 12px #aaa;
}

.tile {
	opacity: 0.6;
	-webkit-transition: opacity 250ms ease;
	-moz-transition: opacity 250ms ease;
	-ms-transition: opacity 250ms ease;
	-o-transition: opacity 250ms ease;
	transition: opacity 250ms ease;
}

.tile.lit {
	opacity: 1;
}

.red, .blue, .yellow, .green {
	height: 290px;
	-webkit-border-radius: 150px 150px 150px 150px;
	border-radius: 150px 150px 150px 150px;
	position: absolute;
	text-indent: 10000px;
}

.red:hover, .blue:hover, .yellow:hover, .green:hover {
	border: 2px solid black
}

.red {
	background: #FF5643;
	clip: rect(0px, 300px, 150px, 150px);
	width: 296px;
}

.blue {
	background: dodgerblue;
	clip: rect(0px, 150px, 150px, 0px);
	width: 300px;
}

.yellow {
	background: #FEEF33;
	clip: rect(150px, 150px, 300px, 0px);
	width: 300px;
}

.green {
	background: #BEDE15;
	clip: rect(150px,300px, 300px, 150px);
	width: 296px;
}

.game-info {
	margin-top: 90px;
}

.game-info button {
	width: 5em;
	box-sizing: border-box;
	font-size: 1.4em;
	-webkit-border-radius: 10px 10px 10px 10px;
	border-radius: 10px 10px 10px 10px;
	background: #6DABE8;
	border: none;
	padding: 0.3em 0.6em;
}

.game-info button:hover {
	background: #78BCFF;
}

.game-options h2 {
	margin-top: 30px;
	margin-bottom: 0;
}

.game-options input[type="radio"] {
	margin-right: 10px;
}

.hoverable:hover {
	cursor: pointer;
}
</style>