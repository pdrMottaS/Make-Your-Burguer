<template>
    <div>
        <Message :msg="msg" v-show="msg"/>
        <div>
            <form id="burguer-form" @submit="createBurguer">
                <div class="input-container">
                    <label for="nome">Nome do cliente:</label>
                    <input type="text" id="id" name="name" v-model="nome" placeholder="Digite seu nome"/>
                </div>
                <div class="input-container">
                    <label for="pao">Escolha seu pão:</label>
                    <select name="pao" id="pao" v-model="pao">
                        <option value="">Selecione seu Pão:</option>
                        <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
                    </select>
                </div>
                <div class="input-container">
                    <label for="carne">Escolha a carne do seu Burguer:</label>
                    <select name="carne" id="carne" v-model="carne">
                        <option value="">Selecione seu tipo de carne:</option>
                        <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
                    </select>
                </div>
                <div id="opcionais-container" class="input-container">
                    <label id="opcionais-title" for="opcionais">Selecione seus opcionais:</label>
                    <div class="checkbox-container" v-for="opcional in opicionaisdata" :key="opcional.id">
                        <input type="checkbox" name="opcionais" v-model="opicionais" :value="opcional.tipo"/>
                        <span>{{ opcional.tipo }}</span>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar meu Burguer"/>
                </div>
            </form>
        </div>
    </div>
</template>
<script>
import Message from './Message.vue'

export default {
    name:"BurguerForm",
    components:{
        Message
    },
    data(){
        return{
            nome: null,
            paes: null,
            carnes: null,
            opicionaisdata: null,
            pao: null,
            carne: null,
            opicionais: [],
            status: "Solicitado",
            msg: null
        }
    },
    methods:{
        async getIngredientes(){
            const req = await fetch("http://localhost:3000/ingredientes")
            const data = await req.json()
            this.paes = data.paes
            this.carnes = data.carnes
            this.opicionaisdata = data.opcionais
        },
        async createBurguer(e){
            e.preventDefault()
            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opicionais: Array.from(this.opicionais),
                status: this.status
            }
            const dataJson = JSON.stringify(data)
            const req = await fetch("http://localhost:3000/burgers",{
                method: "POST",
                headers: { "Content-Type" : "application/json" },
                body: dataJson
            })
            const res = await req.json()
            console.log(res)
            this.msg = `Pedido Nº ${res.id} realizado com sucesso`
            setTimeout(()=>this.msg = "",3000)
            this.nome = ""
            this.carne = ""
            this.pao = ""
            this.opicionais = []
        }
    },
    mounted(){
        this.getIngredientes()
    }
}
</script>
<style scoped>
    #burguer-form{
        max-width: 400px;
        margin: 0 auto;
    }
    .input-container{
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }
    label{
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #FCBA03;
    }
    input, select{
        padding: 5px 10px;
        widows: 300px;
    }
    #opcionais-container {
        flex-direction: row;
        flex-wrap: wrap;
    }
    #opicionais-title{
        width: 100%;
    }
    .checkbox-container{
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }
    .checkbox-container span,
    .checkbox-container input{
        width: auto;
    }
    .checkbox-container span{
        margin-left: 6px;
        font-weight: bold;
    }
    .submit-btn{
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 10px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    .submit-btn:hover{
        background-color: transparent;
        color: #222;
    }
</style>