<template>
    <div class="control">
        <div class="control_btn">
            <button><img  class="refresh" src="../assets/refresh.png" alt="" @click="photo()"></button>
            <select id="select" v-on:change="test()">
                <option value="ru">RU</option>
                <option value="en">EN</option>
                <option value="be">BE</option>
            </select>
            <div class="degree">
                <button v-bind:class='{ notactive : !celsius }'
                @click="changeDegree()">°C</button>
                <button v-bind:class='{ notactive : celsius }'
                @click="changeDegree()">°F</button>
            </div>
            
        </div>
        <form @submit.prevent="onSubmit">
            <input type="text" 
            v-bind:placeholder="content[lang].searchInput"
            v-model="cityValue"
            >
            <div class="micro">
                <img  src="../assets/micro.png" alt="" @click="rec()">
                <div class="micActive" v-bind:class='{ active : microActive }' ></div>
            </div>
            
            
            <button type="submit">{{content[lang].searchBtn}}</button>
        </form>
    </div>
</template>

<script>
export default {
    props : ["content", "lang", "celsius"],
    data() {
        return {
            cityValue: '',
            microActive: false
            

        }
    },
    methods: {
        photo() {
            let x = new Date();
            let currentTime = (this.timezone / 60 + x.getTimezoneOffset())*60*1000;
            let time = new Date(x.getTime()+currentTime).getHours();
            let str = ''
            if(time >= 0 && time < 7 ){
                str = 'night'
            }else if(time >= 7 && time < 12){
                str = 'dawn'
            }else if (time >= 12 && time < 6){
                str = 'noon'
            }else {
                str ='sunset'
            }
            console.log('Параметр запроса картинки:', str )
            fetch(`https://api.unsplash.com/photos/random?orientation=landscape&per_page=1&query=${str}&client_id=udLcIb5SCH9DA3omtchD6rN2wVb2LK8HuyeH4rcp3y0`)
            .then(res => res.json())
            .then(data =>{
                let img = new Image();
                img.src = data.urls.full;
                img.addEventListener('load', ()=>{
                    document.body.style.background = `center/ cover url('${data.urls.full}')`
                })
            })
            .catch(()=>{
                document.body.style.background = `url('https://rulib.info/uploads/11_05_2013/view/201209/oboik.ru_7234.jpg')`
                alert('Закончились запросы по картинкам')
            })

        },
        rec() {
            this.microActive = true
            let SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition
            let recognition = SpeechRecognition? new SpeechRecognition() : false
            recognition.addEventListener('result', e => {
                this.cityValue = e.results[0][0].transcript;
                this.microActive = false
                this.onSubmit()
        })
           recognition.start()
        },
        test: function () {
           this.$emit('changeLang', event.target.value)
        },
        changeDegree() {
            this.$emit('changeDegree')
        },
        async onSubmit() {
            if (this.cityValue !== ''){
                if(!/^\d+$/.test(this.cityValue)){
                    const res = await fetch(`https://translate.yandex.net/api/v1.5/tr.json/detect?hint=en&key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${this.cityValue}`)
                    const data = await res.json()
                    if (data.lang != 'en') {
                        const resTranslate = await fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${this.cityValue}&lang=${data.lang}-en`)
                        const dataTranslate = await resTranslate.json() 
                        this.$emit('changeCity', dataTranslate.text[0]);
                        this.cityValue = '';
                    }   
                    else {
                        this.$emit('changeCity', this.cityValue);
                        this.cityValue = '';
                    }
                }else{
                    this.$emit('changeCity', this.cityValue);                
                    this.cityValue = '';
                }
            }
            
        }
    },
    mounted(){
        document.getElementById('select').value = this.lang
    }

}
</script>

<style scoped>
    .control {
        display: flex;
        justify-content: space-between;
        background: #353535d5;
        padding: 20px;
        border-radius: 10px;
    }
    .control_btn {
        display: flex;
    }
    .control_btn button {
        width: 40px;
        height: 40px;
        margin-right: 5px ;
        background: #858585;
        border: 2px solid #858585;
        border-radius: 5px;
        color: #ffffff;
        font-weight: 600;
        font-size: 15px ;
        cursor: pointer;
    }
    select {
        height: 40px;
        margin-right: 5px ;
        background: #858585;
        border: 2px solid #858585;
        border-radius: 5px;
        color: #ffffff;
        font-family: 'Montserrat', sans-serif;
        font-weight: 600;
        font-size: 15px ;
        cursor: pointer;
    }
    .refresh {
        width: 20px;
        height: 20px;
    }
    .degree {
        height: 36px;
        border-radius: 5px;
        border: 2px solid #858585;
    }
    .degree button {
        height: 36px;
        border: none;
        border-radius: 0;
        margin-right: 0;
        font-family: 'Montserrat', sans-serif;
        font-size: 20px;
    }
    .degree button.notactive {
        opacity: 0.5;
    }
    form {
        display: flex;
        height: 36px;
        border: 2px solid #858585;
        border-radius: 5px;
    }
    form button {
        height: 36px;
        background: #858585;
        border: none;
        color: #ffffff;
        font-family: 'Montserrat', sans-serif;
        font-weight: 600;
        font-size: 20px ;
        cursor: pointer;
    }
    form input {
        height:  34px;
        border: none;
        padding-left: 5px;
        border-radius: 3px 0 0 3px;
        font-family: 'Montserrat', sans-serif;
        
    }
    button:active, button:focus {
    outline: none;
    }
    select:active, select:focus {
    outline: none;
    }
    input {
    outline: none;
    }
    .micro {
        display: flex;
    }
    .micro img {
        display: flex;
        width: 35px;
        background: #ffffff;
        cursor: pointer;
    }
    .micActive {
        display: none;
        width: 8px;
        height: 8px;
        background: red;
        position: absolute;
        border-radius: 4px;
        margin-left: 23px;
    }
    .micActive.active {
        display: block;
    }
@media  (max-width: 700px){
    .control{
        padding: 10px;
    }
    input {
    width: 20vw;
    font-size: 10px;
}

}

@media  (max-width: 500px){
    .control {
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    form {
        margin-top: 5px;
    }
}
</style>
