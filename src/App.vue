<template>
    <div>
        <div> 
            Навык: {{ level }}
            <br />
            Удача: {{ luck }}
        </div>
        <hr />
        <div>
            <h3>Сумка</h3>
        </div>        
        <div class='bag'>
            <div v-for='item in bag' :key='item.id' class='bagItems'>
                <div class='fishImg'>
                    <img v-bind:src='require(`./img/fishes/${item.img}.png`)' width='100px' alt='fish'>
                </div>
                <div class='fishCount'>
                    <span> {{ item.count }} </span>
                </div>
            </div>
        </div>
        <div>
            <div id='fishing' v-if='bobber' @click='nibble'>Забросить</div>
        </div>
    </div>
</template>

<script>
   
            
export default {
    name: 'app',
    data() {
        return {
            fish: [{ name: 'gold', chance: 0.02, minLevel: 1, maxLevel: 2000, img: '' },
                   { name: 'Луциан', chance: 1, minLevel: 1, maxLevel: 20, img: 'fish1' },
                   { name: 'Черноротик', chance: 0.6, minLevel: 5, maxLevel: 25, img: 'fish2' },
                   { name: 'Медуза', chance: 0.5, minLevel: 10, maxLevel: 30, img: 'fish3' },
                   { name: 'Форель', chance: 0.4, minLevel: 20, maxLevel: 40, img: 'fish4' },
                   { name: 'Спороус', chance: 0.7, minLevel: 30, maxLevel: 50, img: 'fish5' },
                   { name: 'Сосокунь', chance: 0.6, minLevel: 40, maxLevel: 60, img: 'fish6' },
                   { name: 'Золотая рыбка', chance: 0.7, minLevel: 50, maxLevel: 70, img: 'fish7' },
                   { name: 'Подкорежник', chance: 0.5, minLevel: 60, maxLevel: 80, img: 'fish8' }
                 ],
            
            haul: [{ name: 'Кольцо', chance: 0.06, minLevel: 1, maxLevel: 2000, img: 'haul1' },
                   { name: 'Бутыль', chance: 0.02, minLevel: 1, maxLevel: 2000, img: 'haul2' },
                   { name: 'Монета', chance: 0.04, minLevel: 1, maxLevel: 2000, img: 'haul3' }
                 ],
            
            level: 1,
            luck: 1,
            allChance: 1,
            bag: [],
            bobber: true
        }
    },
    
    computed: {
//        allHaul() {
//            return this.fish.concat(this.haul);
//        }  
    },
    
    methods:{
        // заброс 
        casting() {
            this.isLevelUp();
            this.getFish();
            this.bobber = true;
        },

        // расчет времени клёва
        nibble() {
            this.bobber = false;
            const max = 1; //3
            const time = 1 + Math.floor(Math.random() * Math.floor(max));
            let nibbleTimer = setTimeout(this.casting, time * 1000);
        },

        // повышение навыка
        isLevelUp() {
            const res = parseInt(100 / (this.level / 12) * this.luck);
            const per = res > 100 ? 1 : res / 100;
            let rand = Math.random();
            if(rand < per) {
                this.level++;
                this.allChance = this.level;
                //console.log('lvl up! ' + this.level);
            } else console.log('');
        },

        // вылов рыбы
        getFish() {
            const availableFish = this.fish.filter(item => item.minLevel <= this.allChance);
            for(let i=0; i<availableFish.length; i++){
                console.log(availableFish[i].name + '  ' + availableFish[i].chance);
            }
            const arrlen = availableFish.length;
            let rand = Math.random();
            let slice = 1;
            let haul = 0;
            for(let i = 0; i < arrlen; i++) {

                // срез шанса поимки, если рыбка мелкая
                availableFish[i].maxLevel < this.level ? slice = 3 : slice = 1;
                if(rand < availableFish[i].chance / slice) {
                    //console.log('вы поймали ' + availableFish[i].name + ' с шансом ' + availableFish[i].chance / slice);
                    this.putToBag(availableFish[i].name);
                    haul++;
                }
            }

            if(haul === 0) {
                console.log('вы поймали.. старый грязный сапог!');
            }
        },

        // добавление добычи в сумку
        putToBag(fish) {
            const isInBag = this.bag.findIndex(item => item.name === fish);
            const fishArr = this.fish.findIndex(item => item.name === fish);
            if(isInBag < 0) {
                this.bag.push({
                    name: fish,
                    count: 1,
                    img: this.fish[fishArr].img
                });
                //console.log('теперь их у вас: 1');
            } else {
                this.bag[isInBag].count++;
                //console.log('теперь их у вас: ' + this.bag[isInBag].count);
            }
        }
    }
}
</script>

<style lang='scss'>
    #fishing {
        background: orange;
        padding: 5px;
        margin: 10px;
        width: 100px;
        text-align: center;
        border-radius: 5px;
        cursor: pointer;
        
        &:hover {
            background: darkorange;
        }
    }
    
    .bag {
        display: grid;
        grid-template-columns: repeat(4, 6em);
        
        .bagItems {
            position: relative;
            
            .fishImg img {
                width: 60px;
                height: 60px;
            }

            .fishCount {
                position: absolute;
                bottom: 0;
                left: 55px;

                & span {
                    font-weight: bold;
                    color: blue;
                }
            }
        }
    }
</style>
       