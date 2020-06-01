<template>
    <div class="control">
        <div class="control_btn">
            <button><img class="refresh" src="../assets/refresh.png" alt=""></button>
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
            <button type="submit">{{content[lang].searchBtn}}</button>
        </form>
    </div>
</template>

<script>
export default {
    props : ["content", "lang", "celsius"],
    data() {
        return {
            cityValue: ''
        }
    },
    methods: {
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
</style>
